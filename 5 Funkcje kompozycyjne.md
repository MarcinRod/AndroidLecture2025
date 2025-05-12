# 🧩 Funkcje kompozycyjne w Jetpack Compose

## 🔹 Co to są funkcje kompozycyjne?

**Funkcje kompozycyjne** (ang. *composable functions*) to podstawowy budulec interfejsu użytkownika w Jetpack Compose. Są to funkcje w języku Kotlin oznaczone adnotacją `@Composable`, które opisują, jak powinien wyglądać fragment UI.

---

## 🏷️ Nazewnictwo funkcji kompozycyjnych

- Funkcje kompozycyjne powinny mieć nazwy w stylu **PascalCase** (każde słowo z wielkiej litery, bez podkreśleń), np. `UserCard`, `LoginScreen`, `Greeting`.
- Nazwa funkcji powinna jasno wskazywać, **co dana funkcja wyświetla lub realizuje** – unikaj nazw ogólnych typu `MyComposable` czy `TestFunction`.
- Jeśli funkcja kompozycyjna reprezentuje cały ekran, zakończ jej nazwę słowem `Screen`, np. `ProfileScreen`, `SettingsScreen`.
- Dla mniejszych, wielokrotnego użytku elementów stosuj nazwy opisujące ich rolę, np. `UserAvatar`, `ProductItem`, `ErrorMessage`.
- Jeśli funkcja jest **prywatna dla pliku** (nie powinna być używana poza plikiem), można dodać prefix `_`, np. `_UserAvatar`. To konwencja Compose, która sygnalizuje, że funkcja jest "wewnętrzna".
- Unikaj skrótów i nieczytelnych nazw – kod Compose powinien być samoopisujący się.
- Funkcje kompozycyjne nie powinny mieć czasowników w trybie rozkazującym (np. `ShowUser`), lecz raczej rzeczowniki lub rzeczowniki z przymiotnikami (`UserCard`, `ErrorDialog`).

**Przykłady dobrego nazewnictwa:**
- `LoginScreen`
- `ProfileHeader`
- `ProductListItem`
- `_UserAvatar` (funkcja prywatna)
- `ErrorSnackbar`

**Przykłady złego nazewnictwa:**
- `doLoginComposable`
- `myComposable1`
- `ShowProfile`
- `testFun`

Dobre nazewnictwo ułatwia czytanie, testowanie i ponowne wykorzystanie kodu w większych projektach Compose.

---

## 🧱 Rola modyfikatora (`Modifier`)

- **Modifier** to specjalny obiekt w Compose, który pozwala modyfikować wygląd, rozmiar, pozycję i zachowanie elementów UI.
- Modifier jest przekazywany jako parametr do większości composable i umożliwia "nakładanie" wielu efektów w łańcuchu wywołań.
- Każdy composable przyjmujący `Modifier` powinien mieć go jako pierwszy parametr domyślny, np.:
  ```kotlin
  @Composable
  fun MyButton(
      onClick: () -> Unit,
      modifier: Modifier = Modifier
  ) {
      Button(onClick = onClick, modifier = modifier) {
          Text("Kliknij mnie")
      }
  }
  ```
- Dzięki temu możesz łatwo łączyć modyfikatory i przekazywać je z zewnątrz, np.:
  ```kotlin
  MyButton(
      onClick = { /* ... */ },
      modifier = Modifier
          .padding(16.dp)
          .fillMaxWidth()
  )
  ```

### ✨ Najczęściej używane funkcje Modifiera

- `padding(...)` – dodaje odstęp wewnętrzny wokół elementu.
- `fillMaxWidth()`, `fillMaxHeight()`, `fillMaxSize()` – rozciąga element do maksymalnej szerokości/wysokości/rozmiaru rodzica.
- `size(...)`, `width(...)`, `height(...)` – ustawia rozmiar elementu.
- `background(color)` – ustawia tło elementu.
- `clickable { ... }` – obsługuje kliknięcia na dowolnym elemencie.
- `weight(...)` – rozdziela przestrzeń w Row/Column proporcjonalnie.
- `offset(...)` – przesuwa element względem jego pozycji.
- `align(...)` – ustawia wyrównanie w kontenerze (np. w Box).
- `wrapContentWidth()`, `wrapContentHeight()` – dopasowuje rozmiar do zawartości.

**Przykład łączenia modyfikatorów:**
```kotlin
Text(
    text = "Przykład",
    modifier = Modifier
        .padding(8.dp)
        .background(Color.LightGray)
        .fillMaxWidth()
        .clickable { /* obsługa kliknięcia */ }
)
```

> **Wskazówka:**  
> Kolejność wywołań ma znaczenie – najpierw wykonywany jest pierwszy modyfikator z łańcucha, potem kolejne.

Modyfikatory pozwalają budować elastyczne, responsywne i interaktywne UI w Compose bez konieczności dziedziczenia po widokach czy stosowania złożonych układów.

---

## 🔄 Wynoszenie stanu (State Hoisting)

- **State hoisting** to wzorzec polegający na przekazywaniu stanu i funkcji do jego zmiany z composable nadrzędnego do podrzędnego.
- Dzięki temu composable są bardziej uniwersalne, łatwiejsze do testowania i ponownego użycia, ponieważ nie zarządzają swoim stanem wewnętrznie, lecz otrzymują go z zewnątrz.
- Wynoszenie stanu pozwala na lepszą kontrolę nad logiką aplikacji i ułatwia zarządzanie cyklem życia stanu (np. resetowanie, zapisywanie, synchronizację z ViewModel).

### Jak to wygląda w praktyce?

Zamiast:

```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text("Kliknięto: $count")
    }
}
```

Stosujemy wynoszenie stanu:

```kotlin
@Composable
fun Counter(count: Int, onIncrement: () -> Unit) {
    Button(onClick = onIncrement) {
        Text("Kliknięto: $count")
    }
}

@Composable
fun CounterScreen() {
    var count by remember { mutableStateOf(0) }
    Counter(count = count, onIncrement = { count++ })
}
```

- Funkcja `Counter` nie zarządza już swoim stanem – otrzymuje go jako parametr oraz przyjmuje funkcję do jego zmiany.
- Funkcja nadrzędna (`CounterScreen`) zarządza stanem i przekazuje go do composable podrzędnego.

### Zalety state hoistingu

- **Reużywalność:** Ten sam composable można wykorzystać w różnych miejscach z różnym stanem.
- **Testowalność:** Łatwiej testować composable, które nie mają własnego stanu.
- **Przewidywalność:** Stan jest zarządzany w jednym miejscu, co ułatwia debugowanie i utrzymanie kodu.
- **Integracja z ViewModel:** Wyniesiony stan łatwo powiązać z ViewModel lub innym źródłem danych.

### Kiedy NIE wynosić stanu?

- Jeśli stan jest **czysto lokalny** i nie wpływa na inne elementy UI, można go przechowywać wewnątrz composable (np. rozwinięcie menu, lokalny efekt animacji).

---

> **Więcej o state hoisting:**  
> [State hoisting – oficjalny poradnik](https://developer.android.com/jetpack/compose/state#hoisting)
---
## 📦 Podstawowe kontenery i często używane elementy

W Compose kontenery służą do organizowania i układania elementów UI względem siebie. Oto najważniejsze z nich wraz z często wykorzystywanymi parametrami:
- **Column** – układa elementy pionowo.
- **Row** – układa elementy poziomo.
- **Box** – nakłada elementy na siebie (pozycjonowanie).
- **LazyColumn** – lista przewijana pionowo (odpowiednik RecyclerView).
- **LazyRow** – lista przewijana poziomo.
- **Card** – kontener z zaokrąglonymi rogami i cieniem.
- **Surface** – podstawowy kontener z tłem i efektem podniesienia.
- **Spacer** – element do tworzenia odstępów.
- **Scaffold** – kontener do budowy typowych układów aplikacji (np. z paskiem górnym, dolnym, FAB).
  
### Przykłady
- **Column**
  - Układa elementy pionowo, jeden pod drugim.
  - Najczęstsze parametry:
    - `modifier` – modyfikator dla całej kolumny (np. rozmiar, padding).
    - `verticalArrangement` – sposób rozmieszczenia elementów w pionie (np. `Arrangement.SpaceBetween`, `Arrangement.Center`).
    - `horizontalAlignment` – wyrównanie elementów w poziomie (np. `Alignment.CenterHorizontally`).
  - Przykład:
    ```kotlin
    Column(
        modifier = Modifier.fillMaxWidth(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text("Pierwszy")
        Text("Drugi")
    }
    ```

- **Row**
  - Układa elementy poziomo, jeden obok drugiego.
  - Najczęstsze parametry:
    - `modifier`
    - `horizontalArrangement` – rozmieszczenie elementów w poziomie.
    - `verticalAlignment` – wyrównanie elementów w pionie.
  - Przykład:
    ```kotlin
    Row(
        modifier = Modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically
    ) {
        Button(onClick = {}) { Text("OK") }
        Button(onClick = {}) { Text("Anuluj") }
    }
    ```

- **Box**
  - Pozwala nakładać elementy na siebie (pozycjonowanie względem siebie).
  - Najczęstsze parametry:
    - `modifier`
    - `contentAlignment` – domyślne wyrównanie wszystkich dzieci (np. `Alignment.Center`).
  - Przykład:
    ```kotlin
    Box(
        modifier = Modifier.size(100.dp),
        contentAlignment = Alignment.BottomEnd
    ) {
        Image(painter, contentDescription = null)
        Icon(Icons.Default.Favorite, contentDescription = null)
    }
    ```

- **LazyColumn**
  - Lista przewijana pionowo (odpowiednik RecyclerView).
  - Najczęstsze parametry:
    - `modifier`
    - `contentPadding` – padding wewnątrz listy.
    - `verticalArrangement`
    - `horizontalAlignment`
  - Przykład:
    ```kotlin
    LazyColumn(
        modifier = Modifier.fillMaxSize(),
        contentPadding = PaddingValues(16.dp)
    ) {
        items(100) { index ->
            Text("Element #$index")
        }
    }
    ```

- **LazyRow**
  - Lista przewijana poziomo.
  - Najczęstsze parametry:
    - `modifier`
    - `contentPadding`
    - `horizontalArrangement`
    - `verticalAlignment`
  - Przykład:
    ```kotlin
    LazyRow(
        contentPadding = PaddingValues(horizontal = 8.dp)
    ) {
        items(10) { index ->
            Card(modifier = Modifier.size(80.dp)) {
                Text("Karta $index")
            }
        }
    }
    ```

- **Card**
  - Kontener z zaokrąglonymi rogami i cieniem.
  - Najczęstsze parametry:
    - `modifier`
    - `shape` – kształt karty (np. RoundedCornerShape).
    - `elevation` – cień pod kartą.
    - `backgroundColor`
  - Przykład:
    ```kotlin
    Card(
        modifier = Modifier.padding(8.dp),
        shape = RoundedCornerShape(16.dp),
        elevation = CardDefaults.cardElevation(8.dp)
    ) {
        Text("To jest karta")
    }
    ```

- **Surface**
  - Podstawowy kontener z tłem i efektem podniesienia.
  - Najczęstsze parametry:
    - `modifier`
    - `color`
    - `shape`
    - `shadowElevation`
  - Przykład:
    ```kotlin
    Surface(
        color = Color.White,
        shape = RoundedCornerShape(8.dp),
        shadowElevation = 4.dp
    ) {
        Text("Zawartość")
    }
    ```

- **Spacer**
  - Element do tworzenia odstępów między innymi elementami.
  - Najczęstsze parametry:
    - `modifier` (najczęściej `Modifier.height(...)` lub `Modifier.width(...)`)
  - Przykład:
    ```kotlin
    Spacer(modifier = Modifier.height(16.dp))
    ```

- **Scaffold**
  - Scaffold` to kontener, który ułatwia budowanie standardowych układów aplikacji zgodnych z Material Design. 
  - Pozwala łatwo dodać pasek górny (`TopAppBar`), dolny (`BottomAppBar`), przycisk FAB, szufladę nawigacyjną i inne elementy.

```kotlin
@Composable
fun MainScreen() {
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Tytuł aplikacji") }
            )
        },
        floatingActionButton = {
            FloatingActionButton(onClick = { /* akcja */ }) {
                Icon(Icons.Default.Add, contentDescription = "Dodaj")
            }
        },
        bottomBar = {
            BottomAppBar {
                Text("Dolny pasek")
            }
        }
    ) { innerPadding ->
        // Główna zawartość ekranu
        Column(modifier = Modifier.padding(innerPadding)) {
            Text("Witaj w aplikacji!")
        }
    }
}
```

**Często używane parametry Scaffold:**
- `topBar` – pasek górny (np. `TopAppBar`)
- `bottomBar` – pasek dolny (np. `BottomAppBar`)
- `floatingActionButton` – przycisk FAB
- `drawerContent` – zawartość szuflady nawigacyjnej
- `snackbarHost` – obsługa snackbarów
- `content` – główna zawartość ekranu (przyjmuje padding od Scaffolda)
---


## ⭐ Często używane elementy UI

W Jetpack Compose znajdziesz wiele gotowych composable, które pozwalają szybko budować nowoczesne interfejsy użytkownika. Oto najważniejsze i najczęściej wykorzystywane z nich:

- **Text**
  - Służy do wyświetlania tekstu.
  - Najważniejsze parametry:
    - `text` – wyświetlany tekst.
    - `modifier` – modyfikator wyglądu i położenia.
    - `style` – styl tekstu (np. `MaterialTheme.typography.bodyLarge`).
    - `color` – kolor tekstu.
    - `maxLines`, `overflow` – kontrola liczby linii i zachowania przy przepełnieniu.
  - Przykład:
    ```kotlin
    Text(
        text = "Witaj w Compose!",
        style = MaterialTheme.typography.titleLarge,
        color = Color.Blue,
        maxLines = 1,
        overflow = TextOverflow.Ellipsis
    )
    ```

- **Button**
  - Klasyczny przycisk.
  - Najważniejsze parametry:
    - `onClick` – obsługa kliknięcia.
    - `modifier`
    - `enabled` – czy przycisk jest aktywny.
    - `content` – zawartość przycisku (np. `Text`, `Icon`).
  - Przykład:
    ```kotlin
    Button(onClick = { /* akcja */ }) {
        Text("Zatwierdź")
    }
    ```

- **OutlinedButton**, **IconButton**
  - Warianty przycisków: z obramowaniem lub tylko z ikoną.
  - Przykład:
    ```kotlin
    OutlinedButton(onClick = { }) { Text("Anuluj") }
    IconButton(onClick = { }) { Icon(Icons.Default.Favorite, contentDescription = null) }
    ```

- **TextField**
  - Pole do wprowadzania tekstu.
  - Najważniejsze parametry:
    - `value` – aktualna wartość tekstu.
    - `onValueChange` – reakcja na zmianę tekstu.
    - `label` – etykieta pola.
    - `placeholder` – podpowiedź.
    - `singleLine` – czy pole jest jednoliniowe.
    - `modifier`
  - Przykład:
    ```kotlin
    var text by remember { mutableStateOf("") }
    TextField(
        value = text,
        onValueChange = { text = it },
        label = { Text("Imię") },
        singleLine = true
    )
    ```

- **Checkbox**, **Switch**, **RadioButton**
  - Elementy wyboru.
  - Najważniejsze parametry:
    - `checked` – stan zaznaczenia.
    - `onCheckedChange` – reakcja na zmianę stanu.
    - `modifier`
  - Przykład:
    ```kotlin
    var checked by remember { mutableStateOf(false) }
    Checkbox(checked = checked, onCheckedChange = { checked = it })
    Switch(checked = checked, onCheckedChange = { checked = it })
    RadioButton(selected = checked, onClick = { checked = !checked })
    ```

- **Image**, **Icon**
  - Wyświetlanie obrazów i ikon.
  - Najważniejsze parametry:
    - `painter` (dla Image), `imageVector` (dla Icon)
    - `contentDescription` – opis dla dostępności.
    - `modifier`
    - `contentScale` – sposób skalowania obrazu.
  - Przykład:
    ```kotlin
    Image(
        painter = painterResource(id = R.drawable.avatar),
        contentDescription = "Avatar",
        modifier = Modifier.size(64.dp),
        contentScale = ContentScale.Crop
    )
    Icon(Icons.Default.Home, contentDescription = "Strona główna")
    ```

- **Divider**
  - Linia podziału, np. między elementami listy.
  - Najważniejsze parametry:
    - `modifier`
    - `color`
    - `thickness`
  - Przykład:
    ```kotlin
    Divider(color = Color.Gray, thickness = 1.dp)
    ```


---

## 👁️ Podgląd funkcji kompozycyjnych (Preview)

Jetpack Compose umożliwia szybki podgląd UI bez uruchamiania aplikacji na emulatorze lub urządzeniu.

- Użyj adnotacji `@Preview` nad funkcją kompozycyjną (lub jej wywołaniem).
- Funkcja podglądu musi być bezparametrowa lub mieć wartości domyślne.

**Przykład:**
```kotlin
@Preview(showBackground = true, name = "Podgląd powitania", widthDp = 320)
@Composable
fun GreetingPreview() {
    Greeting(name = "Compose")
}
```
- Podgląd pojawi się w panelu "Design" w Android Studio.

### Najważniejsze parametry adnotacji `@Preview`

- **`name`** – nazwa podglądu wyświetlana w Android Studio (przydatne, gdy masz kilka podglądów).
- **`showBackground`** – czy pokazać tło wokół composable (domyślnie `false`). Ustaw na `true`, by lepiej widzieć kształt i marginesy elementu.
- **`backgroundColor`** – kolor tła podglądu w formacie ARGB (np. `0xFFFF0000` dla czerwonego). Działa tylko, gdy `showBackground = true`.
- **`widthDp`** i **`heightDp`** – wymusza rozmiar podglądu w dp (przydatne do testowania responsywności).
- **`group`** – pozwala grupować kilka podglądów razem (np. różne warianty kolorystyczne).
- **`uiMode`** – pozwala wymusić tryb jasny/ciemny (`Configuration.UI_MODE_NIGHT_YES` lub `NO`).
- **`locale`** – pozwala wymusić język/region (np. `"pl"` dla polskiego, `"en"` dla angielskiego).
- **`fontScale`** – pozwala przetestować UI przy różnych rozmiarach czcionek (np. `1.5f`).

**Przykłady:**
```kotlin
@Preview(name = "Tryb ciemny", uiMode = Configuration.UI_MODE_NIGHT_YES, showBackground = true)
@Composable
fun DarkModePreview() {
    MyScreen()
}

@Preview(locale = "pl", showBackground = true, widthDp = 400)
@Composable
fun PolishPreview() {
    Greeting(name = "Użytkownik")
}
```
> **Wskazówka:**  
> Możesz mieć wiele podglądów dla jednej funkcji, by szybko sprawdzić różne warianty (np. tryb ciemny, różne języki, rozmiary ekranu).
> 
Podgląd w Compose znacznie przyspiesza pracę nad UI, pozwala testować różne warianty i szybciej wychwytywać błędy wizualne.


---

## 🧠 Dobre praktyki

- Dziel UI na małe, wielokrotnego użytku composable.
- Przekazuj stan i obsługę zdarzeń przez parametry (state hoisting).
- Zawsze przyjmuj `Modifier` jako pierwszy parametr domyślny.
- Korzystaj z podglądu (`@Preview`) do szybkiego testowania wyglądu.


---

## 📚 Dokumentacja

- [Oficjalna dokumentacja Jetpack Compose – Composables](https://developer.android.com/jetpack/compose/composables)
- [Compose Pathway – podstawy Compose](https://developer.android.com/jetpack/compose/tutorial)
- [Compose – Preview](https://developer.android.com/jetpack/compose/tooling/preview)
- [State hoisting – oficjalny poradnik](https://developer.android.com/jetpack/compose/state#hoisting)

---
### 🧭 **Następny temat:** [Komponent Nawigacji](6%20Komponent%20nawigacji.md)