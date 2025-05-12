# 🚀 Podstawy języka Kotlin dla Androida

Kotlin to nowoczesny, statycznie typowany język programowania, oficjalnie wspierany przez Google do tworzenia aplikacji na Androida. Jest czytelny, zwięzły i w pełni interoperacyjny z Javą.

---

## 🔹 Najważniejsze cechy Kotlin

- **Zwięzłość** – mniej kodu niż w Javie, brak zbędnych getterów/setterów.
- **Bezpieczeństwo względem nulli** – system typów zapobiega wielu błędom z `null`.
- **Funkcje wyższego rzędu i lambdy** – łatwe przekazywanie funkcji jako parametrów.
- **Rozszerzenia** – możliwość dodawania nowych funkcji do istniejących klas.
- **Współpraca z Javą** – możesz używać bibliotek Java i stopniowo migrować kod.

---

## 📦 Podstawowa składnia Kotlin

### Zmienne i stałe

W Kotlinie do przechowywania danych używasz dwóch słów kluczowych: `val` i `var`.

- **val** – służy do deklarowania stałych (wartość nie może być zmieniona po przypisaniu). Odpowiada to zmiennym tylko do odczytu (readonly).
- **var** – służy do deklarowania zmiennych (wartość można zmieniać dowolnie w trakcie działania programu).

**Przykłady:**

  ```kotlin
    val pi = 3.14           // stała, nie można przypisać nowej wartości
    var licznik = 0         // zmienna, można zmieniać wartość

    licznik = 5             // OK
    // pi = 3.1415          // Błąd kompilacji!
  ```

- Typ zmiennej jest zazwyczaj domniemywany automatycznie, ale możesz go podać jawnie:
  ```kotlin
  val nazwa: String = "Android"
  var wiek: Int = 20
  ```

- Możesz zadeklarować zmienną bez inicjalizacji, ale wtedy musisz podać typ:
  ```kotlin
  var wynik: Int
  wynik = 42
  ```

**Dobre praktyki:**
- Używaj `val` zawsze, gdy nie musisz zmieniać wartości – kod jest wtedy bezpieczniejszy i bardziej czytelny.
- Używaj `var` tylko wtedy, gdy wartość zmiennej faktycznie się zmienia.

Zmienne i stałe w Kotlinie są podstawą do pracy z danymi w aplikacjach Android.

---

## 🔹 Podstawowe typy danych

- `Int`, `Long`, `Double`, `Float` – liczby
- `Boolean` – wartości logiczne (`true`/`false`)
- `String` – tekst
- `Char` – pojedynczy znak
- `List`, `MutableList`, `Set`, `Map` – kolekcje

```kotlin
val liczba: Int = 42
val tekst: String = "Hello"
val lista: List<String> = listOf("A", "B", "C")
```

---

## 🔸 Wyrażenia warunkowe

- **if / else** – jak w Javie, ale może być też wyrażeniem (zwraca wartość)
- **when** – rozbudowana wersja switch

```kotlin
val x = 10
val wynik = if (x > 5) "duże" else "małe"

when (x) {
    1 -> println("jeden")
    in 2..10 -> println("od 2 do 10")
    else -> println("inne")
}
```

---

## 🔸 Obsługa stringów

- **Łączenie:** Możesz łączyć napisy za pomocą operatora `+`:
  ```kotlin
  val imie = "Jan"
  val powitanie = "Cześć, " + imie + "!"
  ```

- **Interpolacja:** Najczęściej używany sposób – wstawianie wartości zmiennych bezpośrednio do tekstu za pomocą `$`:
  ```kotlin
  val imie = "Jan"
  val powitanie = "Cześć, $imie!"
  val wiek = 20
  val info = "Masz ${wiek + 1} lat"
  ```

- **Wielolinijkowe stringi:** Użyj potrójnych cudzysłowów `""" ... """` do tworzenia tekstów zawierających wiele linii lub znaki specjalne bez konieczności ich uciekania:
  ```kotlin
  val wielolinijkowy = """
      To jest
      tekst w kilku liniach
      Znak tabulacji:	<- tutaj
  """.trimIndent()
  ```

- **Podstawowe operacje na stringach:**
  - `length` – długość tekstu: `val dlugosc = tekst.length`
  - `uppercase()`, `lowercase()` – zmiana wielkości liter: `tekst.uppercase()`
  - `substring()` – wycinanie fragmentu: `tekst.substring(0, 3)`
  - `replace()` – zamiana fragmentu: `tekst.replace("a", "b")`
  - `contains()` – sprawdzenie, czy tekst zawiera podciąg: `tekst.contains("kot")`
  - `split()` – dzielenie na części: `"a,b,c".split(",")`
  - `trim()` – usuwanie białych znaków z początku i końca: `tekst.trim()`

- **Porównywanie stringów:**
  - Standardowe porównanie: `a == b`
  - Porównanie bez rozróżniania wielkości liter: `a.equals(b, ignoreCase = true)`

- **Konwersja innych typów do stringa:**
  ```kotlin
  val liczba = 123
  val tekst = liczba.toString()
  ```

---

## 🔸 Pętle

W Kotlinie można korzystać z różnych rodzajów pętli, a także z wygodnych zakresów (range):

- **Pętla for z zakresem:**
  ```kotlin
  for (i in 1..5) { // od 1 do 5 włącznie
      println(i)
  }
  for (i in 5 downTo 1) { // od 5 do 1 w dół
      println(i)
  }
  for (i in 1 until 5) { // od 1 do 4 (5 wyłączone)
      println(i)
  }
  for (i in 0..10 step 2) { // co 2
      println(i)
  }
  ```

- **Pętla for po kolekcji:**
  ```kotlin
  val lista = listOf("A", "B", "C")
  for (element in lista) {
      println(element)
  }
  ```

- **Pętla while:**
  ```kotlin
  var licznik = 0
  while (licznik < 3) {
      println(licznik)
      licznik++
  }
  ```

### Zakresy (range)

- tworzone za pomocą operatora `..` (np. `1..10`).
- Można użyć `downTo`, `until`, `step` do sterowania zakresem.
- Zakresy są często wykorzystywane w pętlach, ale także w warunkach (`if (x in 1..10)`).

**Przykład użycia zakresu w warunku:**
```kotlin
val wiek = 18
if (wiek in 13..19) {
    println("Nastolatek")
}
```
---


### Funkcje

Funkcje w Kotlinie są podstawowym sposobem organizacji kodu. Pozwalają na wielokrotne użycie logiki, przekazywanie parametrów i zwracanie wartości.

```kotlin
fun dodaj(a: Int, b: Int): Int {
    return a + b
}

// Funkcja jako wyrażenie (skrótowa forma):
fun powitaj(imie: String) = "Cześć, $imie!"
```

#### Rodzaje argumentów funkcji

- **Argumenty domyślne:** Możesz przypisać domyślną wartość do parametru.
  ```kotlin
  fun powitanie(imie: String = "Gość") {
      println("Cześć, $imie!")
  }
  powitanie() // Cześć, Gość!
  powitanie("Anna") // Cześć, Anna!
  ```

- **Argumenty nazwane:** Możesz przekazywać argumenty po nazwie, co zwiększa czytelność. Nie trzeba też przejmować się kolejnoscią argumentów. 
  ```kotlin
  fun zamow(nazwa: String, ilosc: Int, pilne: Boolean = false) { /* ... */ }
  zamow(nazwa = "Kawa", ilosc = 2, pilne = true)
  ```

- **Argumenty vararg:** Pozwalają przekazać dowolną liczbę argumentów tego samego typu.
  ```kotlin
  fun suma(vararg liczby: Int): Int = liczby.sum()
  val wynik = suma(1, 2, 3, 4) // 10
  ```

#### Nazewnictwo funkcji

- Nazwy funkcji powinny być czasownikami w trybie oznajmującym, np. `dodaj`, `wyslijEmail`, `pobierzDane`.
- Funkcje zwracające wartość logiczną często zaczynają się od `is`, `has`, `can`, np. `isActive()`, `hasPermission()`, `canEdit()`.
- Nazwy powinny być krótkie, ale opisowe.

---

### Funkcje lambda


Funkcje lambda to krótkie, anonimowe funkcje, które możesz przypisać do zmiennej lub przekazać jako argument do innej funkcji.
- **Podstawowa składnia funkcji lambda**:

    ```kotlin
    { parametry -> ciało }
    { x: Int -> x*2 }
    ```

- **Lambda bez parametrów:**
  ```kotlin
  val powitanie = { println("Cześć!") }
  powitanie() // wypisze: Cześć!
  ```

- **Lambda z jednym parametrem:**
  ```kotlin
  val podwoj = { x: Int -> x * 2 }
  println(podwoj(5)) // wypisze: 10
  ```

- **Lambda jako argument funkcji:**
  ```kotlin
  fun wykonaj(akcja: () -> Unit) {
      akcja()
  }

  wykonaj { println("To jest lambda!") }
  ```

### Kolekcje

Kotlin oferuje wygodne i bezpieczne kolekcje, które są szeroko wykorzystywane w aplikacjach Android. Najczęściej używane typy kolekcji to:

- **List** – lista tylko do odczytu (niemutowalna)
- **MutableList** – lista, którą można modyfikować (dodawać, usuwać elementy)
- **Set** / **MutableSet** – zbiór unikalnych elementów
- **Map** / **MutableMap** – para klucz-wartość

**Przykłady deklaracji:**
```kotlin
val lista = listOf("A", "B", "C")                // niemutowalna lista
val mutableList = mutableListOf(1, 2, 3)         // mutowalna lista
val zbior = setOf("kot", "pies", "kot")          // zbiór (duplikaty ignorowane)
val mapa = mapOf("klucz" to 1, "drugi" to 2)     // mapa tylko do odczytu
val mutableMap = mutableMapOf("a" to 1, "b" to 2)
```

**Podstawowe operacje na kolekcjach:**
- Odczyt elementu z listy: `lista[0]`
- Dodanie elementu do mutowalnej listy: `mutableList.add(4)`
- Usunięcie elementu: `mutableList.remove(2)`
- Sprawdzenie, czy lista zawiera element: `lista.contains("A")`
- Iteracja po kolekcji:
  ```kotlin
  for (element in lista) {
      println(element)
  }
  ```

**Wyrażenia lambda i kolekcje:**
Kotlin pozwala na wygodne przetwarzanie kolekcji za pomocą funkcji wyższego rzędu i lambd, np.:
```kotlin
val liczby = listOf(1, 2, 3, 4, 5)
val parzyste = liczby.filter { it % 2 == 0 }      // [2, 4]
val podwojone = liczby.map { it * 2 }             // [2, 4, 6, 8, 10]
val suma = liczby.sum()                           // 15
```

**Przykład praktyczny:**
```kotlin
val produkty = listOf("Kawa", "Herbata", "Sok")
produkty.forEach { produkt ->
    println("Produkt: $produkt")
}
```

**Wskazówki:**
- Domyślnie kolekcje są niemutowalne - jeśli musisz modyfikować kolekcję, użyj typu `MutableList`, `MutableSet` lub `MutableMap`.


### Null safety

Null safety to jedna z najważniejszych cech języka Kotlin, która pomaga unikać błędów związanych z wartością `null` (tzw. NullPointerException).

- **Domyślnie zmienne nie mogą być nullem:**
  ```kotlin
  var tekst: String = "Hello"
  tekst = null // Błąd kompilacji!
  ```

- **Aby zmienna mogła przyjmować wartość null, dodaj znak zapytania do typu:**
  ```kotlin
  var tekst: String? = null
  tekst = "Cześć"
  ```

- **Bezpieczne wywołanie (safe call operator `?.`):**
  Pozwala wywołać metodę lub uzyskać właściwość tylko, jeśli obiekt nie jest nullem.
  ```kotlin
  println(tekst?.length) // jeśli tekst == null, wynik to null, nie ma błędu
  ```

- **Operator Elvis (`?:`):**
  Pozwala ustawić wartość domyślną, gdy zmienna jest nullem.
  ```kotlin
  val dlugosc = tekst?.length ?: 0 // jeśli tekst == null, dlugosc = 0
  ```

- **Wymuszenie nie-null (`!!`):**
  Rzuca wyjątek, jeśli zmienna jest nullem (używaj tylko, gdy masz pewność, że nie będzie null).
  ```kotlin
  val dlugosc = tekst!!.length // jeśli tekst == null, aplikacja się wykrzaczy
  ```

- **Bezpieczne rzutowanie (`as?`):**
  Zwraca null, jeśli rzutowanie się nie powiedzie.
  ```kotlin
  val liczba: Int? = "123".toIntOrNull()
  ```

- **Przykład praktyczny:**
  ```kotlin
  fun pokazDlugosc(tekst: String?) {
      println("Długość: ${tekst?.length ?: "brak tekstu"}")
  }
  pokazDlugosc("Kotlin") // Długość: 6
  pokazDlugosc(null)     // Długość: brak tekstu
  ```

**Wskazówki:**
- Staraj się unikać zmiennych nullable, jeśli nie jest to konieczne.
- Null safety sprawia, że kod jest bezpieczniejszy i łatwiejszy w utrzymaniu, co jest szczególnie ważne w aplikacjach Android.

---

### Klasy

W Kotlinie klasy są podstawowym sposobem definiowania własnych typów danych.

#### Nazewnictwo klas

- Nazwy klas w Kotlinie powinny być w stylu **PascalCase** – każde słowo zaczyna się wielką literą, bez podkreśleń, np. `User`, `MainActivity`, `ProductItem`.
- Nazwa klasy powinna jasno opisywać, co reprezentuje dana klasa (np. `User`, `Order`, `LoginViewModel`).
- Dla klas danych (`data class`) stosuj te same zasady – nazwa powinna być rzeczownikiem.
- Unikaj skrótów i nieczytelnych nazw – kod powinien być samoopisujący się.
- Dla obiektów singleton (`object`) i companion objectów również stosuj PascalCase, np. `Logger`, `DatabaseHelper`.

**Przykłady dobrego nazewnictwa:**
```kotlin
class UserProfile
data class ProductItem(val name: String, val price: Double)
object NetworkManager
```
**Przykłady złego nazewnictwa:**
```kotlin
class userprofile
class product_item
object networkmanager
```
Dobre nazewnictwo klas ułatwia czytanie, testowanie i utrzymanie kodu w większych projektach Android.

- **Zwykła klasa:**
  ```kotlin
  class Osoba(val imie: String, var wiek: Int)
  val jan = Osoba("Jan", 30)
  println(jan.imie) // Jan
  jan.wiek = 31
  ```

- **Klasa z metodami:**
  ```kotlin
  class Kalkulator {
      fun dodaj(a: Int, b: Int): Int = a + b
  }
  val k = Kalkulator()
  println(k.dodaj(2, 3)) // 5
  ```
#### Konstruktor klasy

W Kotlinie konstruktor to specjalna funkcja, która służy do tworzenia obiektów danej klasy. Najczęściej używa się **konstruktora głównego** (primary constructor), który definiuje się bezpośrednio w nagłówku klasy:

```kotlin
class Osoba(val imie: String, var wiek: Int)
```
- Parametry konstruktora mogą być od razu właściwościami klasy (z `val` lub `var`), jak w przykładzie powyżej.
- Tworzenie obiektu:  
  ```kotlin
  val jan = Osoba("Jan", 30)
  ```

Możesz też zdefiniować **konstruktor dodatkowy** (secondary constructor), jeśli potrzebujesz innego sposobu tworzenia obiektu:

```kotlin
class Osoba(val imie: String) {
    var wiek: Int = 0

    constructor(imie: String, wiek: Int) : this(imie) {
        this.wiek = wiek
    }
}
```

- Konstruktor dodatkowy używa słowa kluczowego `constructor` i zawsze musi wywołać konstruktor główny (`: this(...)`).

**Ważne:**
- Jeśli klasa nie ma żadnych właściwości ani metod, możesz pominąć nawiasy:
  ```kotlin
  class Pusta
  ```
- Jeśli chcesz wykonać dodatkową logikę podczas tworzenia obiektu, możesz użyć bloku `init`:
  ```kotlin
  class Osoba(val imie: String) {
      init {
          println("Tworzę osobę o imieniu $imie")
      }
  }
  ```

### Dziedziczenie

W Kotlinie możesz tworzyć hierarchie klas i korzystać z dziedziczenia, aby ponownie wykorzystywać kod i rozszerzać funkcjonalność.

- Domyślnie każda klasa w Kotlinie jest **finalna** (nie można po niej dziedziczyć). Aby umożliwić dziedziczenie, użyj słowa kluczowego `open` przy definicji klasy i metod.

**Przykład dziedziczenia:**
```kotlin
open class Zwierze(val imie: String) {
    open fun dajGlos() {
        println("Zwierzę wydaje dźwięk")
    }
}

class Pies(imie: String) : Zwierze(imie) {
    override fun dajGlos() {
        println("Hau hau!")
    }
}

val pies = Pies("Reksio")
pies.dajGlos() // Hau hau!
```

- **open** – pozwala na dziedziczenie po klasie lub nadpisywanie metody.
- **override** – służy do nadpisywania metod z klasy bazowej.
- Konstruktor klasy pochodnej wywołuje konstruktor klasy bazowej za pomocą `: Bazowa(...)`.

**Interfejsy a dziedziczenie:**


Interfejsy w Kotlinie definiuje się za pomocą słowa kluczowego `interface`. Interfejs może zawierać deklaracje metod (bez implementacji) oraz domyślne implementacje metod.

- Klasa może implementować dowolną liczbę interfejsów (w przeciwieństwie do dziedziczenia po klasie, które jest pojedyncze).
- Interfejsy są często używane do definiowania kontraktów, które muszą być spełnione przez klasy (np. obsługa kliknięć, komunikacja między komponentami).

**Przykład prostego interfejsu:**
```kotlin
interface Klikalne {
    fun klik()
}

class Przycisk : Klikalne {
    override fun klik() {
        println("Kliknięto przycisk")
    }
}

val przycisk = Przycisk()
przycisk.klik() // Kliknięto przycisk
```

**Interfejs z domyślną implementacją:**
```kotlin
interface Powitalne {
    fun powitanie(imie: String) {
        println("Cześć, $imie!")
    }
}

class Uzytkownik : Powitalne

val user = Uzytkownik()
user.powitanie("Anna") // Cześć, Anna!
```

**Implementacja wielu interfejsów:**
```kotlin
interface Klikalne { fun klik() }
interface Latajace { fun lec() }

class SuperPtak : Klikalne, Latajace {
    override fun klik() { println("Ptak kliknięty") }
    override fun lec() { println("Ptak leci") }
}
```

**Ważne cechy interfejsów w Kotlinie:**
- Interfejs może mieć właściwości (bez stanu), np. `val nazwa: String`
- Interfejs nie może przechowywać stanu (nie może mieć pól z wartościami)
- Możesz implementować wiele interfejsów w jednej klasie

Interfejsy są szeroko wykorzystywane w Androidzie, np. do obsługi zdarzeń (kliknięcia, callbacki), komunikacji między fragmentami, adapterami itp.

## Klasy specjalne

- **Data class** – specjalny typ klasy do przechowywania danych. Automatycznie generuje metody `equals()`, `hashCode()`, `toString()`, `copy()` i `componentN()`:
  ```kotlin
  data class Produkt(val nazwa: String, val cena: Double)
  val kawa = Produkt("Kawa", 12.99)
  println(kawa) // Produkt(nazwa=Kawa, cena=12.99)
  val nowaKawa = kawa.copy(cena = 10.99)
  ```

- **enum class** – typ wyliczeniowy, pozwala zdefiniować ograniczony zbiór stałych wartości. Przydatny np. do reprezentowania stanów, typów, kategorii:
  ```kotlin
  enum class Status {
      ŁADOWANIE, SUKCES, BŁĄD
  }

  val status = Status.SUKCES
  when (status) {
      Status.ŁADOWANIE -> println("Trwa ładowanie...")
      Status.SUKCES -> println("Sukces!")
      Status.BŁĄD -> println("Wystąpił błąd")
  }
  ```
    Enum w Kotlinie może mieć własne właściwości, metody oraz konstruktory. Dzięki temu enumy mogą przechowywać dodatkowe dane i zachowanie.

    ```kotlin
    enum class StanZamowienia(val opis: String, val czyFinalny: Boolean) {
        NOWE("Nowe zamówienie", false),
        W_REALIZACJI("W realizacji", false),
        ZREALIZOWANE("Zrealizowane", true),
        ANULOWANE("Anulowane", true);
    }
    ```
- **object** – słowo kluczowe do tworzenia singletonów (pojedynczych instancji klas) lub obiektów anonimowych.

- **Singleton:**
  ```kotlin
  object Logger {
      fun log(msg: String) {
          println("LOG: $msg")
      }
  }
  Logger.log("Start aplikacji")
  ```

- **Obiekt anonimowy** – przydatny np. do implementacji interfejsów „w locie”:
  ```kotlin
  val klikacz = object : Klikalne {
      override fun klik() {
          println("Kliknięto anonimowy obiekt")
      }
  }
  klikacz.klik()
  ```

- **object companion** – obiekt towarzyszący w klasie, pozwala na tworzenie statycznych metod/pól:
  ```kotlin
  class Uzytkownik(val imie: String) {
      companion object {
          fun utworzAnonimowego() = Uzytkownik("Anonim")
      }
  }
  val anonim = Uzytkownik.utworzAnonimowego()
  ```

---

## 🔸 Funkcje i właściwości rozszerzające

Funkcje i właściwości rozszerzające (extension functions, extension properties) pozwalają dodać nowe metody lub właściwości do istniejących klas – nawet tych, których nie możesz modyfikować (np. klasy z bibliotek lub Javy). 

### Funkcje rozszerzające

- Definiujesz je poza klasą, poprzedzając nazwą typu, który rozszerzasz:
  ```kotlin
  fun String.odwroc(): String = this.reversed()

  val tekst = "Kotlin"
  println(tekst.odwroc()) // "niltok"
  ```

- Możesz rozszerzać dowolny typ, także klasy Androida:
  ```kotlin
  fun Context.toast(msg: String) =
      Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()
  ```

- Funkcje rozszerzające mają dostęp do publicznych metod i właściwości rozszerzanego typu przez słowo kluczowe `this`.

- Funkcje rozszerzające nie modyfikują oryginalnej klasy

### Właściwości rozszerzające

- Pozwalają dodać „pseudo-właściwości” do istniejących klas:
  ```kotlin
  val String.odwrocony: String
      get() = this.reversed()

  println("Android".odwrocony) // "diordnA"
  ```

- Właściwości rozszerzające nie mogą mieć stanu (nie możesz zadeklarować pola), tylko getter.




**Podsumowanie:**
- Funkcje i właściwości rozszerzające poprawiają czytelność kodu i pozwalają pisać bardziej idiomatyczne API.
- Nie modyfikują oryginalnych klas – są bezpieczne i wygodne.

---

## 📚 Dokumentacja i materiały

- [Oficjalna dokumentacja Kotlin](https://kotlinlang.org/docs/home.html)
- [Kotlin for Android Developers](https://developer.android.com/kotlin)
- [Podstawy Kotlin na Android Developers](https://developer.android.com/kotlin/learn)
---
### 🧭 **Następny temat:** [Android Studio](2%20Android%20Studio.md
)