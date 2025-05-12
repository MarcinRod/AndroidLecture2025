# Bazy danych w Androidzie – Room

Room to oficjalna biblioteka Jetpack, która upraszcza korzystanie z relacyjnych baz danych SQLite w aplikacjach Android. Zapewnia bezpieczny i wygodny sposób zapisu oraz odczytu danych.

---

## Dlaczego Room?

- Zapewnia warstwę abstrakcji nad SQLite.
- Automatycznie generuje kod SQL na podstawie adnotacji.
- Umożliwia typowane zapytania i migracje bazy.
- Integruje się z korutynami, Flow i StateFlow.

---

## Podstawowe elementy Room

1. **Entity**  
   Klasa danych oznaczona adnotacją `@Entity`, która reprezentuje tabelę w bazie danych. Każde pole klasy odpowiada kolumnie w tabeli.  
   **Klucz główny** oznaczasz adnotacją `@PrimaryKey`. Może to być pojedyncze pole (np. `id`), które może być automatycznie generowane (`autoGenerate = true`), lub możesz zdefiniować klucz złożony (wskazując kilka pól jako klucz główny).  Klucz główny gwarantuje unikalność każdego rekordu w tabeli i jest wymagany przez Room.

   **Przykład:**
   ```kotlin
   @Entity
   data class Produkt(
       @PrimaryKey(autoGenerate = true) val id: Int = 0, // klucz główny, autoinkrementacja
       val nazwa: String,
       val cena: Double
   )
   ```

   Możesz także zdefiniować klucz główny bez autoinkrementacji:
   ```kotlin
   @Entity
   data class Uzytkownik(
       @PrimaryKey val email: String, // klucz główny to email
       val imie: String
   )
   ```



2. **DAO (Data Access Object)**  
   Interfejs oznaczony adnotacją `@Dao`. Zawiera metody do wykonywania operacji na bazie: wstawiania (`@Insert`), aktualizacji (`@Update`), usuwania (`@Delete`) oraz zapytań (`@Query`). Metody mogą być oznaczone jako `suspend` (dla korutyn) lub zwracać `Flow` (do obserwacji zmian).

   - **@Insert**  
     Adnotacja `@Insert` służy do wstawiania nowych rekordów do tabeli. Room automatycznie generuje odpowiednie polecenie SQL na podstawie przekazanego obiektu lub listy obiektów.  
     Możesz określić strategię konfliktu (np. `OnConflictStrategy.REPLACE`), która decyduje, co zrobić w przypadku próby wstawienia rekordu z istniejącym kluczem głównym.

     **Przykład:**
     ```kotlin
     @Insert(onConflict = OnConflictStrategy.REPLACE)
     suspend fun insert(produkt: Produkt)
     ```

   - **@Query**  
     Adnotacja `@Query` pozwala definiować własne zapytania SQL (np. SELECT, ). Room sprawdza poprawność zapytania w czasie kompilacji i automatycznie mapuje wyniki na obiekty Kotlin.  
     Możesz przekazywać parametry do zapytania za pomocą składni `:nazwaParametru`.

     **Przykład:**
     ```kotlin
     @Query("SELECT * FROM Produkt")
     suspend fun getAll(): List<Produkt>

     @Query("SELECT * FROM Produkt WHERE nazwa = :nazwa")
     suspend fun findByName(nazwa: String): List<Produkt>
     ```
     **Przykład z wykorzystaniem Flow:**
     ```kotlin
     @Query("SELECT * FROM Produkt")
     fun observeAll(): Flow<List<Produkt>>
     ```

     **Zaleta korzystania z Flow:**  
     Metoda zwracająca `Flow` pozwala na **obserwowanie zmian w bazie danych w czasie rzeczywistym**. Gdy tylko dane w tabeli się zmienią (np. zostanie dodany nowy produkt), wszystkie composable lub ViewModel, które subskrybują ten `Flow`, automatycznie otrzymają zaktualizowaną listę produktów bez potrzeby ręcznego odświeżania danych.


3. **Database**  
   Klasa abstrakcyjna dziedzicząca po `RoomDatabase`, oznaczona adnotacją `@Database`. Określasz w niej listę encji (`entities`) oraz wersję bazy (`version`). Musi zawierać abstrakcyjne metody zwracające DAO dla każdej encji, z której chcesz korzystać.

   - W tej klasie nie implementujesz żadnych metod – Room generuje całą logikę dostępu do bazy na podstawie deklaracji DAO.
   - Klasa bazy danych powinna być singletonem w aplikacji (tworzona tylko raz).


   ##### **Ogólny proces tworzenia instancji RoomDatabase:**
   1. Utwórz publiczną klasę abstrakcyjną (abstract class), która rozszerza `RoomDatabase`. Klasa jest abstrakcyjna, ponieważ biblioteka Room stworzy jej implementację.
   2. Opatrz klasę adnotacją `@Database`. W argumentach wymień encje dla bazy danych i ustaw numer wersji.
   3. Zdefiniuj abstrakcyjną metodę lub właściwość, która zwraca instancję DAO.
   4. Potrzebujemy tylko jednej instancji `RoomDatabase` dla całej aplikacji, więc powinna być singletonem.
   5. Aby utworzyć bazę danych, wykorzystujemy `Room.databaseBuilder`, ale tylko wtedy, gdy nie istnieje. W przeciwnym razie zwracamy istniejącą bazę danych.

   **Przykład:**
   **Tworzenie instancji bazy (z użyciem companion object):**
   ```kotlin
   @Database(entities = [Produkt::class], version = 1)
   abstract class AppDatabase : RoomDatabase() {
       abstract fun produktDao(): ProduktDao

       companion object {
           @Volatile
           private var INSTANCE: AppDatabase? = null

           fun getInstance(context: Context): AppDatabase {
               return INSTANCE ?: synchronized(this) {
                   val instance = Room.databaseBuilder(
                       context.applicationContext,
                       AppDatabase::class.java,
                       "produkty.db"
                   ).build()
                   INSTANCE = instance
                   instance
               }
           }
       }
   }
   ```
   Dzięki companion object masz pewność, że w całej aplikacji będzie tylko jedna instancja bazy danych (singleton).



Adnotacja `@Volatile` w Kotlinie (i Javie) oznacza, że zmienna może być współdzielona między różnymi wątkami i jej wartość będzie zawsze aktualna dla wszystkich wątków.  


Dzięki `@Volatile`:
- Każdy wątek zawsze widzi najnowszą wartość zmiennej `INSTANCE`.
- Zapobiega to sytuacji, w której dwa wątki utworzą dwie różne instancje bazy danych.
- Jest to ważne przy implementacji wzorca singleton w środowisku wielowątkowym.

`synchronized` to słowo kluczowe w Kotlinie i Javie, które pozwala na synchronizację dostępu do fragmentu kodu przez wiele wątków. Dzięki temu tylko jeden wątek na raz może wykonać kod znajdujący się w bloku `synchronized`.

---

**Dodatkowe elementy Room:**

- **TypeConverter** – pozwala zapisywać w bazie typy, które nie są wspierane natywnie przez SQLite (np. listy, daty).


---

## Przykład: zapis danych z Room

### 1. Definicja encji

```kotlin
import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity
data class Produkt(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val nazwa: String,
    val cena: Double
)
```

### 2. DAO – interfejs dostępu do danych

```kotlin
import androidx.room.*

@Dao
interface ProduktDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(produkt: Produkt)

    @Query("SELECT * FROM Produkt")
    suspend fun getAll(): List<Produkt>

    @Query("SELECT * FROM Produkt")
    fun observeAll(): Flow<List<Produkt>>
}
```

### 3. Klasa bazy danych (singleton z companion object)

```kotlin
import android.content.Context
import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase

@Database(entities = [Produkt::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun produktDao(): ProduktDao

    companion object {
        @Volatile
        private var INSTANCE: AppDatabase? = null

        fun getInstance(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "produkty.db"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

### 4. Uzyskanie DAO i zapis danych w bazie (np. w ViewModelu)

```kotlin
// W ViewModelu lub repozytorium:
val db = AppDatabase.getInstance(context)
val produktDao = db.produktDao()

viewModelScope.launch {
    val nowyProdukt = Produkt(nazwa = "Kawa", cena = 12.99)
    produktDao.insert(nowyProdukt)
}
```

### 5. Obserwowanie zmian w bazie (np. w ViewModelu)

```kotlin
val produktyFlow: Flow<List<Produkt>> = produktDao.observeAll()
```

---

**Wskazówki:**
- Wszystkie operacje na bazie wykonuj w korutynach (`suspend fun` lub `Flow`), aby nie blokować wątku głównego.
- Do obserwowania zmian w bazie używaj `Flow`.


**Więcej informacji:**  
- [Oficjalna dokumentacja Room](https://developer.android.com/training/data-storage/room)
- [Room Persistence Library – przewodnik](https://developer.android.com/jetpack/androidx/releases/room)

---

### 🧭 **Następny temat:** [Komunikacja przez Internet - Retrofit](https://github.com/MarcinRod/AndroidLecture2025/blob/main/12%20Komunikacja%20przez%20Internet%20-%20Retorfit.md)