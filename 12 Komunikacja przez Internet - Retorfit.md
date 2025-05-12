# Komunikacja przez Internet w Androidzie

Współczesne aplikacje mobilne bardzo często komunikują się z serwerami przez Internet, aby pobierać lub wysyłać dane (np. pobieranie listy produktów, logowanie użytkownika, synchronizacja danych). W Androidzie najczęściej do tego celu wykorzystuje się biblioteki takie jak **Retrofit** lub **OkHttp**.

---

## Pozwolenie na dostęp do Internetu

Aby aplikacja mogła komunikować się z serwerem przez Internet, musisz dodać odpowiednie pozwolenie w pliku `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Bez tego pozwolenia żadne zapytanie sieciowe (np. przez Retrofit, OkHttp) nie zadziała, a aplikacja otrzyma błąd połączenia.

**Ważne:**  
To pozwolenie jest wymagane w każdej aplikacji, która korzysta z Internetu – zarówno do pobierania, jak i wysyłania danych.

---


## Najważniejsze pojęcia

- **API (Application Programming Interface)** – zestaw reguł i adresów (punktów końcowych ang. *endpoint*), przez które aplikacja komunikuje się z serwerem.
- **REST API** – najpopularniejszy styl komunikacji, oparty na protokole HTTP i operacjach takich jak GET, POST, PUT, DELETE.
- **JSON** – najczęściej używany format wymiany danych między aplikacją a serwerem.

---

## Popularne biblioteki do komunikacji

- **Retrofit** – najpopularniejsza biblioteka do obsługi REST API w Androidzie. Umożliwia łatwe wykonywanie zapytań HTTP i mapowanie odpowiedzi na obiekty Kotlin/Java.
- **OkHttp** – niskopoziomowa biblioteka HTTP, na której opiera się Retrofit.

---
## Retrofit – podstawy i przykład użycia z konwerterem Moshi

**Retrofit** to najpopularniejsza biblioteka do komunikacji z REST API w Androidzie. Pozwala w prosty sposób wykonywać zapytania HTTP i mapować odpowiedzi JSON na obiekty Kotlin/Java. Retrofit obsługuje różne konwertery JSON, np. GSON lub Moshi.

---

### Dodanie zależności (build.gradle)

```groovy
implementation "com.squareup.retrofit2:retrofit:2.9.0"
implementation "com.squareup.retrofit2:converter-moshi:2.9.0"
implementation "com.squareup.moshi:moshi:1.15.0"
implementation "com.squareup.moshi:moshi-kotlin:1.15.0"
```

---

### Definicja modelu danych

```kotlin
import com.squareup.moshi.Json
import com.squareup.moshi.JsonClass

@JsonClass(generateAdapter = true)
data class Produkt(
    val id: Int,
    val nazwa: String,
    val cena: Double
)
```

#### Wyjaśnienie adnotacji Moshi

- **@JsonClass(generateAdapter = true)**  
  Ta adnotacja informuje Moshi, aby automatycznie wygenerował adapter do serializacji i deserializacji tej klasy (czyli zamiany obiektu Kotlin na JSON i odwrotnie). Jest wymagana, aby Moshi poprawnie obsłużył klasy danych w Kotlinie.

- **@Json(name = "nazwa_pola")**  
  Pozwala zmapować pole w klasie na inne pole w JSON-ie, jeśli nazwy się różnią.

**Przykład z użyciem @Json:**
```kotlin
@JsonClass(generateAdapter = true)
data class Produkt(
    val id: Int,
    @Json(name = "product_name") val nazwa: String,
    val cena: Double
)
```
W tym przykładzie pole `nazwa` w Kotlinie zostanie zmapowane na pole `product_name` w JSON-ie.

#### Obsługa opcjonalnych pól w obiektach Moshi

Jeśli pole w JSON-ie może być opcjonalne (może nie występować lub mieć wartość `null`), w modelu danych w Kotlinie należy oznaczyć je jako typ nullable (`?`) i (opcjonalnie) ustawić wartość domyślną.

**Przykład:**
```kotlin
@JsonClass(generateAdapter = true)
data class Produkt(
    val id: Int,
    val nazwa: String,
    val cena: Double,
    val opis: String? = null // pole opcjonalne, może być null jeśli nie ma go w JSON-ie
)
```

- Jeśli pole `opis` nie pojawi się w odpowiedzi z serwera, Moshi automatycznie ustawi je na `null`.
- Możesz także ustawić inną wartość domyślną, np. `val opis: String? = "brak opisu"`.

**Ważne:**  
Jeśli pole w modelu nie jest oznaczone przez `?` i nie ma wartości domyślnej, a nie pojawi się w JSON-ie, Moshi zgłosi błąd podczas deserializacji.

---

### Definicja interfejsu API

Interfejs API w Retrofit to miejsce, gdzie deklarujesz metody odpowiadające zapytaniom HTTP, które chcesz wysyłać do serwera. Każda metoda jest opisana odpowiednią adnotacją (np. `@GET`, `@POST`, `@PUT`, `@DELETE`) i może przyjmować parametry, które zostaną przekazane do zapytania.

**Najważniejsze adnotacje Retrofit:**
- `@GET("endpoint")` – zapytanie typu GET (pobieranie danych).
- `@POST("endpoint")` – zapytanie typu POST (wysyłanie danych).
- `@PUT("endpoint")` – zapytanie typu PUT (aktualizacja danych).
- `@DELETE("endpoint")` – zapytanie typu DELETE (usuwanie danych).
- `@Query("param")` – parametr przekazywany w adresie URL (np. `?id=1`).
- `@Path("param")` – parametr dynamiczny w ścieżce URL (np. `/produkty/{id}`).
- `@Body` – przesyłanie obiektu jako treści zapytania (np. w POST).

**Przykład interfejsu API:**
```kotlin
import retrofit2.http.*

interface ApiService {
    @GET("produkty")
    suspend fun getProdukty(): List<Produkt>

    @GET("produkt")
    suspend fun getProduktById(@Query("id") id: Int): Produkt

    @POST("produkty")
    suspend fun dodajProdukt(@Body produkt: Produkt): Produkt

    @PUT("produkty/{id}")
    suspend fun aktualizujProdukt(@Path("id") id: Int, @Body produkt: Produkt): Produkt

    @DELETE("produkty/{id}")
    suspend fun usunProdukt(@Path("id") id: Int)
}
```

**Wyjaśnienie:**
- `@GET("produkty")` – pobiera listę produktów.
- `@GET("produkt")` z `@Query("id")` – pobiera produkt o konkretnym ID (np. `/produkt?id=5`).
- `@POST("produkty")` z `@Body` – wysyła nowy produkt do serwera.
- `@PUT("produkty/{id}")` z `@Path` i `@Body` – aktualizuje produkt o danym ID.
- `@DELETE("produkty/{id}")` z `@Path` – usuwa produkt o danym ID.

### Zwracanie `Call<T>` w interfejsie Retrofit

Oprócz funkcji typu `suspend fun` (dla korutyn), w Retrofit możesz zadeklarować metody, które zwracają obiekt `Call<T>`. Pozwala to na asynchroniczne lub synchroniczne wykonanie zapytania bez  użycia korutyn. 

**Przykład interfejsu z Call:**
```kotlin
import retrofit2.Call
import retrofit2.http.GET
import retrofit2.http.Query

interface ApiService {
    @GET("produkty")
    fun getProdukty(): Call<List<Produkt>>

    @GET("produkt")
    fun getProduktById(@Query("id") id: Int): Call<Produkt>
}
```

**Wywołanie zapytania z użyciem Call:**
```kotlin
val call = api.getProdukty()
call.enqueue(object : Callback<List<Produkt>> {
    override fun onResponse(call: Call<List<Produkt>>, response: Response<List<Produkt>>) {
        if (response.isSuccessful) {
            val produkty = response.body()
            // obsługa danych
        
        }
    }
    override fun onFailure(call: Call<List<Produkt>>, t: Throwable) {
        // obsługa błędu
    }
})
```

**Najważniejsze cechy Call:**
- `Call<T>` pozwala na ręczne zarządzanie wywołaniem (możesz anulować, ponowić, wykonać synchronicznie lub asynchronicznie).
- Użycie `enqueue()` wykonuje zapytanie asynchronicznie (w tle).
- Użycie `execute()` wykonuje zapytanie synchronicznie (blokuje wątek – niezalecane w UI!).


---

### Tworzenie instancji Retrofit z konwerterem Moshi

Aby korzystać z Retrofit w aplikacji, musisz utworzyć instancję Retrofit i wskazać:
- **adres bazowy API** (`baseUrl`),
- **konwerter JSON** (np. Moshi lub Gson),
- (opcjonalnie) dodatkowe ustawienia, np. dotyczące klienta OkHttp.

**Przykład tworzenia instancji Retrofit z Moshi:**
```kotlin
import retrofit2.Retrofit
import retrofit2.converter.moshi.MoshiConverterFactory
import com.squareup.moshi.Moshi

// Tworzenie instancji Moshi (możesz dodać własne adaptery, jeśli potrzebujesz)
val moshi = Moshi.Builder().build()

// Tworzenie instancji Retrofit
val retrofit = Retrofit.Builder()
    .baseUrl("https://twoje-api.pl/api/") // adres bazowy API
    .addConverterFactory(MoshiConverterFactory.create(moshi)) // konwerter JSON
    .build()

// Utworzenie implementacji interfejsu API
val api = retrofit.create(ApiService::class.java)
```

**Wskazówki:**
- `baseUrl` musi kończyć się ukośnikiem `/`.
- Możesz dodać własnego klienta OkHttp, np. z interceptorami do logowania lub obsługi tokenów:
  ```kotlin
  import okhttp3.OkHttpClient
  val client = OkHttpClient.Builder()
      .addInterceptor(MyInterceptor())
      .build()

  val retrofit = Retrofit.Builder()
      .baseUrl("https://twoje-api.pl/api/")
      .addConverterFactory(MoshiConverterFactory.create(moshi))
      .client(client)
      .build()
  ```
- Instancję Retrofit najlepiej przechowywać jako singleton (np. w obiekcie lub klasie Application), aby nie tworzyć jej wielokrotnie.

**Dlaczego warto korzystać z Moshi?**
- Moshi jest szybki, bezpieczny i dobrze wspiera Kotlin (np. typy nullable, data class, adnotacje).
- Pozwala łatwo mapować pola JSON na pola w modelu danych.


### Przykład użycia Retrofit w ViewModelu


Poniżej znajduje się przykładowy ViewModel, który korzysta z Retrofit (z konwerterem Moshi) oraz StateFlow do pobierania i obserwowania listy produktów. Instancja Retrofit powinna być singletonem (np. przekazywana przez konstruktor lub uzyskiwana z obiektu).

```kotlin
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.launch

class ProduktyViewModel(private val api: ApiService) : ViewModel() {

    private val _produkty = MutableStateFlow<List<Produkt>>(emptyList())
    val produkty: StateFlow<List<Produkt>> = _produkty

    private val _error = MutableStateFlow<String?>(null)
    val error: StateFlow<String?> = _error

    fun pobierzProdukty() {
        viewModelScope.launch {
            try {
                _produkty.value = api.getProdukty()
                _error.value = null
            } catch (e: Exception) {
                _error.value = "Błąd pobierania danych: ${e.message}"
            }
        }
    }
}
```

**Wskazówki:**
- Instancję `ApiService` najlepiej przekazywać do ViewModelu przez konstruktor.
- Do obsługi błędów możesz wykorzystać osobny StateFlow.
- Wszystkie operacje sieciowe wykonuj w korutynach (`viewModelScope.launch`) lub z wykorzystaniem `Call`.

---

## Dobre praktyki

- Używaj `suspend fun` w interfejsach API do współpracy z korutynami.
- Obsługuj wyjątki (np. brak internetu, błąd serwera).
- Przechowuj instancję Retrofit jako singleton.

---

**Więcej informacji:**  
- [Oficjalna dokumentacja Retrofit](https://square.github.io/retrofit/)
- [Przykłady użycia Retrofit](https://github.com/square/retrofit/tree/master/samples)
- [Oficjalna dokumentacja Moshi](https://github.com/square/moshi)


---

### 🧭 **Następny temat:** [Platforma Firebase](https://github.com/MarcinRod/AndroidLecture2025/blob/main/13%20Platforma%20Firebase.md)