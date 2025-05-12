# System intencji (Intent) w aplikacjach Android

**Intencje** (ang. *Intents*) to jeden z podstawowych mechanizmów komunikacji w Androidzie. Pozwalają na:

- uruchamianie nowych aktywności (ekranów) lub usług,
- przekazywanie danych między komponentami aplikacji,
- wywoływanie funkcji systemowych (np. otwarcie przeglądarki, wysłanie e-maila),
- komunikację między różnymi aplikacjami.

---

## Rodzaje intencji

- **Explicit Intent** – wskazuje dokładnie, który komponent (np. aktywność) ma zostać uruchomiony. Używasz jej, gdy znasz nazwę klasy docelowej.
  ```kotlin
  val intent = Intent(context, SzczegolyActivity::class.java)
  context.startActivity(intent)
  ```

- **Implicit Intent** – opisuje ogólną akcję (np. „zrób zdjęcie”, „wyślij e-mail”), a system Android sam wybiera odpowiednią aplikację lub komponent, który może obsłużyć tę akcję.
  ```kotlin
  val intent = Intent(Intent.ACTION_VIEW, Uri.parse("https://android.com"))
  context.startActivity(intent)
  ```

---
## Przekazywanie danych z intencjami

Intencje pozwalają nie tylko uruchamiać inne komponenty, ale także przekazywać do nich dane. Najczęściej używa się do tego tzw. **extras** – czyli dodatkowych informacji dołączanych do intencji.

### Przekazywanie danych do aktywności

**Dodawanie danych do intencji:**
```kotlin
val intent = Intent(context, SzczegolyActivity::class.java)
intent.putExtra("id", 123)
intent.putExtra("nazwa", "Produkt X")
context.startActivity(intent)
```

**Odbieranie danych w aktywności:**
```kotlin
class SzczegolyActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val id = intent.getIntExtra("id", 0)
        val nazwa = intent.getStringExtra("nazwa")
        // ...użyj danych
    }
}
```

### Przekazywanie danych do innych aplikacji

Możesz przekazywać dane do innych aplikacji, np. wysyłając tekst lub obraz:

```kotlin
val intent = Intent(Intent.ACTION_SEND)
intent.type = "text/plain"
intent.putExtra(Intent.EXTRA_TEXT, "To jest wiadomość!")
context.startActivity(Intent.createChooser(intent, "Wybierz aplikację"))
```

### Przekazywanie złożonych obiektów

Aby przekazać obiekt, musi on implementować interfejs `Serializable` lub `Parcelable`:

```kotlin
val intent = Intent(context, SzczegolyActivity::class.java)
intent.putExtra("produkt", produkt) // gdzie produkt : Parcelable
context.startActivity(intent)
```
W aktywności odbierasz:
```kotlin
val produkt = intent.getParcelableExtra<Produkt>("produkt")
```


### **Podsumowanie:**  
- Do przekazywania prostych danych używaj `putExtra` i odpowiednich metod odbioru (`getIntExtra`, `getStringExtra` itd.).
- Do przekazywania obiektów używaj `Parcelable` lub `Serializable`.
- Przekazywanie danych przez intencje działa zarówno wewnątrz aplikacji, jak i między różnymi aplikacjami.
  
--- 

### System filtrów intencji (Intent Filters)

**Intent Filter** to mechanizm, dzięki któremu aplikacja może zadeklarować, jakie akcje, dane lub kategorie jest w stanie obsłużyć. Filtry intencji są definiowane w pliku `AndroidManifest.xml` i pozwalają systemowi Android wybrać odpowiednią aplikację lub komponent do obsługi danej intencji.

**Jak to działa?**
- Gdy wysyłasz implicit intent, system przeszukuje wszystkie zainstalowane aplikacje i ich filtry intencji.
- Jeśli znajdzie komponent (np. aktywność), który pasuje do akcji, typu danych lub kategorii, wyświetli użytkownikowi listę wyboru lub uruchomi odpowiednią aplikację.

**Przykład filtra intencji w AndroidManifest.xml:**
```xml
<activity android:name=".SzczegolyActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:scheme="https" android:host="android.com" />
    </intent-filter>
</activity>
```

**Co można filtrować?**
- **Akcje** (np. `android.intent.action.SEND`)
- **Kategorie** (np. `android.intent.category.DEFAULT`)
- **Typy danych** (np. `image/*`, `text/plain`)
- **Schematy i hosty URI** (np. `https://android.com`)


---

## Przykład użycia intencji

**Uruchomienie nowej aktywności z przekazaniem danych:**
```kotlin
val intent = Intent(context, SzczegolyActivity::class.java)
intent.putExtra("id", 123)
context.startActivity(intent)
```

**Wywołanie akcji systemowej (np. otwarcie strony):**
```kotlin
val intent = Intent(Intent.ACTION_VIEW, Uri.parse("https://android.com"))
context.startActivity(intent)
```

---

## Intencje a Navigation Compose

W tradycyjnych aplikacjach Android przechodzenie między ekranami (aktywnościami) odbywa się za pomocą intencji. W Jetpack Compose, zamiast intencji, do nawigacji **wewnątrz jednej aktywności** używasz Navigation Compose i `NavController`.

- **Navigation Compose** zarządza przejściami między composable (ekranami) w ramach jednej aktywności.
- **Intencje** są nadal używane do:
  - uruchamiania innych aktywności (np. przejście do ekranu logowania w innej aplikacji),
  - wywoływania funkcji systemowych,
  - komunikacji między aplikacjami.

**Przykład: przejście do innej aktywności z poziomu composable:**
```kotlin
val context = LocalContext.current
Button(onClick = {
    val intent = Intent(context, SzczegolyActivity::class.java)
    intent.putExtra("id", 123)
    context.startActivity(intent)
}) {
    Text("Otwórz szczegóły (nowa aktywność)")
}
```

**Przykład: nawigacja w Compose (bez intencji):**
```kotlin
Button(onClick = { navController.navigate("szczegoly/123") }) {
    Text("Przejdź do szczegółów (w Compose)")
}
```

---

## Podsumowanie

- **Intencje** służą do komunikacji między aktywnościami, usługami i aplikacjami.
- **Navigation Compose** obsługuje nawigację między ekranami w ramach jednej aktywności.
- W nowoczesnych aplikacjach Compose oba mechanizmy często współistnieją: Navigation Compose do nawigacji wewnętrznej, intencje do komunikacji z systemem i innymi aplikacjami.
---
### 🧭 **Następny temat:** [Manifest i pozwolenia](https://github.com/MarcinRod/AndroidLecture2025/blob/main/08%20Manifest%20i%20pozwolenia.md)