# Platforma Firebase

**Firebase** to platforma firmy Google oferująca zestaw narzędzi i usług do szybkiego tworzenia, rozwijania i zarządzania aplikacjami mobilnymi oraz webowymi. Dzięki Firebase możesz łatwo dodać do swojej aplikacji funkcje takie jak: baza danych w chmurze, uwierzytelnianie użytkowników, powiadomienia push, analityka, hosting i wiele innych.

---

## Najważniejsze usługi Firebase

- **Firebase Authentication** – łatwe uwierzytelnianie użytkowników (e-mail, Google, Facebook, anonimowe i inne).
- **Cloud Firestore** – nowoczesna, skalowalna baza danych NoSQL w chmurze, synchronizująca dane w czasie rzeczywistym.
- **Realtime Database** – starsza baza danych czasu rzeczywistego (NoSQL).
- **Firebase Cloud Messaging (FCM)** – powiadomienia push do aplikacji mobilnych i webowych.
- **Firebase Analytics** – zaawansowana analityka użytkowników i zdarzeń w aplikacji.
- **Firebase Storage** – przechowywanie plików (np. zdjęć, dokumentów) w chmurze.
- **Firebase Hosting** – hosting statycznych stron internetowych.
- **Remote Config** – zdalna konfiguracja parametrów aplikacji bez potrzeby aktualizacji w sklepie.
- **Crashlytics** – raportowanie błędów i awarii aplikacji w czasie rzeczywistym.
- **Test Lab** – testowanie aplikacji na wielu urządzeniach w chmurze.

---

## Jak zacząć korzystać z Firebase w Androidzie?

1. **Załóż konto i projekt w [konsoli Firebase](https://console.firebase.google.com/).**
2. **Dodaj aplikację Android do projektu Firebase** (pobierz plik `google-services.json`).
3. **Dodaj zależności do pliku `build.gradle`:**
   ```groovy
   implementation platform('com.google.firebase:firebase-bom:32.7.0')
   implementation 'com.google.firebase:firebase-analytics-ktx'
   // Dodaj inne zależności w zależności od potrzeb, np.:
   // implementation 'com.google.firebase:firebase-auth-ktx'
   // implementation 'com.google.firebase:firebase-firestore-ktx'
   ```
4. **Dodaj plugin do pliku `build.gradle` (na poziomie projektu):**
   ```groovy
   classpath 'com.google.gms:google-services:4.4.1'
   ```
5. **Dodaj na końcu pliku `build.gradle` (moduł/app):**
   ```groovy
   apply plugin: 'com.google.gms.google-services'
   ```

---



## Firebase Authentication – uwierzytelnianie użytkowników

**Firebase Authentication** umożliwia szybkie i bezpieczne dodanie logowania do aplikacji. Obsługuje wiele metod uwierzytelniania, m.in. e-mail i hasło, Google, Facebook, anonimowe konta i inne.

### Jak dodać Firebase Authentication do projektu?

1. Dodaj zależność do pliku `build.gradle`:
   ```groovy
   implementation 'com.google.firebase:firebase-auth-ktx'
   ```

2. Skonfiguruj wybrane metody logowania w [konsoli Firebase](https://console.firebase.google.com/) (np. e-mail, Google).

---

### Przykład: Rejestracja i logowanie użytkownika przez e-mail i hasło

```kotlin
import com.google.firebase.auth.FirebaseAuth

val auth = FirebaseAuth.getInstance()

// Rejestracja nowego użytkownika
auth.createUserWithEmailAndPassword(email, password)
    .addOnCompleteListener { task ->
        if (task.isSuccessful) {
            // Rejestracja zakończona sukcesem
            val user = auth.currentUser
        } else {
            // Obsługa błędu
        }
    }

// Logowanie użytkownika
auth.signInWithEmailAndPassword(email, password)
    .addOnCompleteListener { task ->
        if (task.isSuccessful) {
            // Logowanie zakończone sukcesem
            val user = auth.currentUser
        } else {
            // Obsługa błędu
        }
    }
```
#### Obiekt FirebaseAuth

`FirebaseAuth` to główny obiekt służący do obsługi uwierzytelniania użytkowników w Firebase. Pozwala na rejestrację, logowanie, wylogowywanie, resetowanie hasła oraz zarządzanie aktualnym użytkownikiem. Instancję tego obiektu uzyskujesz przez `FirebaseAuth.getInstance()`.  
Dzięki niemu możesz łatwo sprawdzić, czy użytkownik jest zalogowany (`auth.currentUser`), pobrać jego dane lub wykonać operacje związane z kontem.

Przykład sprawdzenia, czy użytkownik jest zalogowany:
```kotlin
val auth = FirebaseAuth.getInstance()
val user = auth.currentUser
if (user != null) {
    // Użytkownik jest zalogowany
} else {
    // Brak zalogowanego użytkownika
}
```
---

### Najważniejsze cechy Firebase Authentication

- Obsługa wielu metod logowania (e-mail, Google, Facebook, Apple, anonimowe i inne).
- Prosta integracja z aplikacją.
- Automatyczne zarządzanie sesją użytkownika.
- Możliwość resetowania hasła, weryfikacji e-maila, zmiany danych użytkownika.

**Więcej informacji:**  
- [Firebase Authentication – dokumentacja](https://firebase.google.com/docs/auth)

---

## Firebase Realtime Database – baza danych czasu rzeczywistego

**Firebase Realtime Database** to chmurowa baza danych NoSQL, która umożliwia przechowywanie i synchronizację danych pomiędzy użytkownikami w czasie rzeczywistym. Każda zmiana w bazie jest natychmiast widoczna dla wszystkich podłączonych klientów.

### Jak dodać Firebase Realtime Database do projektu?

1. Dodaj zależność do pliku `build.gradle`:
   ```groovy
   implementation 'com.google.firebase:firebase-database-ktx'
   ```

2. Skonfiguruj Realtime Database w [konsoli Firebase](https://console.firebase.google.com/) (utwórz bazę, ustaw reguły dostępu).

---
### Struktura bazy danych w Firebase Realtime Database

Firebase Realtime Database przechowuje dane w formie **drzewa JSON**. Cała baza to jeden duży obiekt JSON, w którym możesz tworzyć dowolnie zagnieżdżone gałęzie (klucze i wartości). Nie ma tu tabel ani relacji jak w klasycznych bazach SQL – wszystko opiera się na strukturze klucz-wartość.

**Przykład struktury bazy:**
```json
{
  "produkty": {
    "id1": {
      "nazwa": "Kawa",
      "cena": 12.99
    },
    "id2": {
      "nazwa": "Herbata",
      "cena": 8.50
    }
  },
  "uzytkownicy": {
    "uid1": {
      "email": "jan@wp.pl",
      "imie": "Jan"
    }
  }
}
```

**Najważniejsze cechy struktury:**
- Każdy węzeł (gałąź) może zawierać kolejne zagnieżdżone dane.
- Klucze mogą być generowane automatycznie (`push().key`) lub ustalane ręcznie.
- Możesz odwoływać się do dowolnego miejsca w drzewie za pomocą ścieżki, np. `"produkty/id1/nazwa"`.
- Dane są pobierane i synchronizowane na poziomie wybranego węzła – możesz pobrać całą gałąź lub tylko jej fragment.

**Wskazówki projektowe:**
- Unikaj zbyt głębokiego zagnieżdżania danych – lepiej stosować płaską strukturę, aby łatwiej pobierać i aktualizować dane.
- Jeśli potrzebujesz relacji (np. produkt należy do użytkownika), przechowuj identyfikatory (klucze) zamiast zagnieżdżania całych obiektów.

#### Obiekt FirebaseDatabase

`FirebaseDatabase` to główny obiekt służący do komunikacji z Firebase Realtime Database w aplikacji Android. Dzięki niemu możesz uzyskać dostęp do bazy danych, tworzyć referencje do wybranych gałęzi (węzłów) oraz wykonywać operacje takie jak zapis, odczyt, aktualizacja i usuwanie danych.

Instancję tego obiektu uzyskujesz przez:
```kotlin
val database = FirebaseDatabase.getInstance()
```

Najważniejsze cechy:
- Pozwala na dostęp do całej bazy lub wybranych fragmentów (poprzez referencje).
- Obsługuje synchronizację danych w czasie rzeczywistym.
- Umożliwia pracę offline – dane są buforowane lokalnie i synchronizowane po odzyskaniu połączenia.
- Pozwala ustawiać reguły bezpieczeństwa i autoryzacji na poziomie bazy.

**Przykład użycia:**
```kotlin
val database = FirebaseDatabase.getInstance()
val ref = database.getReference("produkty") // referencja do gałęzi "produkty"
```

Dzięki `FirebaseDatabase` możesz łatwo zarządzać danymi w chmurze i synchronizować je pomiędzy wieloma użytkownikami w czasie rzeczywistym.

### Czym jest referencja w strukturze bazy Firebase?

**Referencja** (ang. *reference*) w Firebase Realtime Database to obiekt, który wskazuje na konkretne miejsce (węzeł) w drzewie bazy danych. Za pomocą referencji możesz odczytywać, zapisywać, aktualizować lub usuwać dane w wybranym miejscu bazy.

Referencję tworzysz za pomocą metody `getReference()` na instancji bazy danych, podając ścieżkę do interesującego Cię węzła:

```kotlin
val database = FirebaseDatabase.getInstance()
val ref = database.getReference("produkty") // referencja do gałęzi "produkty"
```

Możesz tworzyć referencje do dowolnego poziomu drzewa, np.:

```kotlin
val refDoProduktu = database.getReference("produkty/id1") // referencja do konkretnego produktu
val refDoUzytkownika = database.getReference("uzytkownicy/uid1") // referencja do konkretnego użytkownika
```

**Do czego służy referencja?**
- Pozwala na wykonywanie operacji na wybranym fragmencie bazy (np. zapis, odczyt, nasłuchiwanie zmian).
- Umożliwia łatwe poruszanie się po strukturze drzewa JSON.
- Dzięki referencjom możesz pracować tylko z danymi, które Cię interesują, bez pobierania całej bazy.

**Przykład użycia referencji:**
```kotlin
// Zapis nowego produktu
val ref = database.getReference("produkty")
val produktId = ref.push().key
if (produktId != null) {
    ref.child(produktId).setValue(produkt)
}

// Odczyt danych z konkretnego produktu
val refDoProduktu = database.getReference("produkty/$produktId")
refDoProduktu.addListenerForSingleValueEvent(object : ValueEventListener {
    override fun onDataChange(snapshot: DataSnapshot) {
        val nazwa = snapshot.child("nazwa").getValue(String::class.java)
        // obsługa danych
    }
    override fun onCancelled(error: DatabaseError) {}
})
```

**Podsumowanie:**  
Referencja to "wskaźnik" na wybraną gałąź w bazie Firebase, dzięki któremu możesz wygodnie zarządzać danymi w strukturze drzewa JSON.

**Więcej o strukturze danych:**  
- [Projektowanie struktury bazy w Realtime Database](https://firebase.google.com/docs/database/web/structure-data)

---
### Przykład: zapis i odczyt danych

Poniżej znajduje się przykładowy kod pokazujący, jak zapisać nowy produkt do bazy oraz jak odczytać dane z Firebase Realtime Database.

**Zapis nowego produktu:**
```kotlin
import com.google.firebase.database.FirebaseDatabase

val database = FirebaseDatabase.getInstance()
val ref = database.getReference("produkty")

// Tworzymy mapę z danymi produktu
val produkt = mapOf(
    "nazwa" to "Kawa",
    "cena" to 12.99
)

// Generujemy unikalny klucz dla nowego produktu
val produktId = ref.push().key
if (produktId != null) {
    ref.child(produktId).setValue(produkt)
}
```
#### Wymagania względem zapisywanego obiektu

Aby zapisać obiekt do bazy za pomocą `setValue()`, obiekt powinien:
- być typu prostego (np. String, Int, Double, Boolean, Map, List) **lub**
- być klasą danych z publicznymi właściwościami i domyślnym (bezparametrowym) konstruktorem.

Przykład klasy danych:
```kotlin
data class Produkt(
    val nazwa: String? = null,
    val cena: Double? = null
)
```
**Ważne:**  
Jeśli zapisujesz własny obiekt (np. `Produkt`), wszystkie jego właściwości muszą być publiczne i mieć wartości domyślne (np. `= null`), aby Firebase mógł poprawnie zdeserializować dane podczas odczytu.

**Odczyt danych (nasłuchiwanie zmian w gałęzi "produkty"):**
```kotlin
import com.google.firebase.database.DataSnapshot
import com.google.firebase.database.DatabaseError
import com.google.firebase.database.ValueEventListener

ref.addValueEventListener(object : ValueEventListener {
    override fun onDataChange(snapshot: DataSnapshot) {
        for (child in snapshot.children) {
            val nazwa = child.child("nazwa").getValue(String::class.java)
            val cena = child.child("cena").getValue(Double::class.java)
            // obsługa danych, np. dodanie do listy produktów
        }
    }
    override fun onCancelled(error: DatabaseError) {
        // obsługa błędu
    }
})
```

**Wyjaśnienie:**
- Do zapisu danych używamy metody `setValue()` na referencji do wybranego miejsca w bazie.
- Do odczytu danych używamy nasłuchiwania zmian (`addValueEventListener`), dzięki czemu aplikacja automatycznie otrzymuje aktualizacje w czasie rzeczywistym.
- Możesz odczytywać całą gałąź (np. wszystkie produkty) lub pojedynczy element (np. jeden produkt po jego ID).

**Pojedyncze pobranie danych z bazy**

Jeśli chcesz pobrać dane tylko raz (bez nasłuchiwania zmian), użyj metody `addListenerForSingleValueEvent`:

```kotlin
val refDoProduktu = database.getReference("produkty/$produktId")
refDoProduktu.addListenerForSingleValueEvent(object : ValueEventListener {
    override fun onDataChange(snapshot: DataSnapshot) {
        val nazwa = snapshot.child("nazwa").getValue(String::class.java)
        val cena = snapshot.child("cena").getValue(Double::class.java)
        // obsługa pojedynczego produktu
    }
    override fun onCancelled(error: DatabaseError) {
        // obsługa błędu
    }
})
```
**Pobieranie danych jednorazowo za pomocą metody get()**

Firebase Realtime Database umożliwia pobranie danych jednorazowo bez nasłuchiwania zmian, korzystając z metody `get()`. Jest to wygodna alternatywa dla `addListenerForSingleValueEvent`.

**Przykład użycia:**
```kotlin
val ref = FirebaseDatabase.getInstance().getReference("produkty/$produktId")
ref.get().addOnSuccessListener { snapshot ->
    val produkt = snapshot.getValue(Produkt::class.java)
    // obsługa pobranego produktu
}.addOnFailureListener {
    // obsługa błędu
}
```

#### Czym jest DataSnapshot?

`DataSnapshot` to obiekt reprezentujący dane pobrane z wybranego miejsca w bazie (referencji). Pozwala na:
- odczyt wartości konkretnego pola (`snapshot.child("nazwa").getValue(String::class.java)`),
- iterowanie po dzieciach (np. po wszystkich produktach w gałęzi),
- sprawdzenie, czy dane istnieją (`snapshot.exists()`).

#### Jak działa metoda getValue?

Metoda `getValue()` konwertuje dane z bazy na wskazany typ w Kotlinie/Java.  
Przykład:
```kotlin
val nazwa = snapshot.child("nazwa").getValue(String::class.java)
val cena = snapshot.child("cena").getValue(Double::class.java)
```
Możesz też pobrać cały obiekt, jeśli masz odpowiednią klasę danych:
```kotlin
data class Produkt(val nazwa: String? = null, val cena: Double? = null)
val produkt = snapshot.getValue(Produkt::class.java)
```
---

### Najważniejsze cechy Realtime Database

- Synchronizacja danych w czasie rzeczywistym między wszystkimi klientami.
- Struktura danych oparta na drzewie JSON (NoSQL).
- Obsługa offline – dane są buforowane lokalnie i synchronizowane po odzyskaniu połączenia.
- Możliwość ustawiania reguł bezpieczeństwa i autoryzacji.

**Więcej informacji:**  
- [Firebase Realtime Database – dokumentacja](https://firebase.google.com/docs/database)

---

## Zalety korzystania z Firebase

- Szybka integracja i gotowe rozwiązania backendowe.
- Bezpieczne uwierzytelnianie użytkowników.
- Skalowalność i niezawodność usług Google.

---

**Więcej informacji:**  
- [Oficjalna dokumentacja Firebase](https://firebase.google.com/docs/android/setup)
- [Przykłady i przewodniki](https://firebase.google.com/docs)

---

### 🧭 **Powrót do głównej strony:** [Start](https://github.com/MarcinRod/AndroidLecture2025/tree/main)