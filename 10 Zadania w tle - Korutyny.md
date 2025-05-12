# Praca w tle w aplikacjach Android (Jetpack Compose i korutyny)

W nowoczesnych aplikacjach Android, także tych tworzonych z użyciem Jetpack Compose, wiele operacji (np. pobieranie danych z sieci, zapisywanie do bazy, długotrwałe obliczenia) powinno być wykonywanych **w tle**, aby nie blokować głównego wątku UI.

## Wątek UI (Main Thread) w Androidzie

Wszystkie operacje związane z wyświetlaniem interfejsu użytkownika (UI) w Androidzie muszą być wykonywane na **wątku głównym** (tzw. **UI thread** lub **Main thread**). Oznacza to, że:

- Renderowanie widoków, obsługa kliknięć, animacje i aktualizacje stanu UI muszą odbywać się na tym samym wątku.
- Jeśli wykonasz długotrwałą operację (np. pobieranie danych z sieci, zapis do bazy) na wątku głównym, aplikacja przestanie reagować, co może prowadzić do błędu **ANR (Application Not Responding)**.
  
---

## Dlaczego praca w tle jest ważna?

- **Nie blokujesz interfejsu użytkownika** – aplikacja pozostaje responsywna.
- **Unikasz błędów ANR (Application Not Responding)** – system może zamknąć aplikację, jeśli operacje trwają zbyt długo na głównym wątku.
- **Lepsze doświadczenie użytkownika** – np. ładowanie danych z sieci nie zatrzymuje animacji czy przewijania.

---

## Korutyny

W Jetpack Compose najczęściej do realizacji pracy w tle używa się **korutyn** (Kotlin Coroutines):

- Korutyny pozwalają łatwo uruchamiać zadania asynchroniczne.
- Są lekkie i dobrze integrują się z architekturą Compose oraz ViewModel.

## Elementy systemu korutyn

### Funkcje suspend

- Funkcje oznaczone słowem kluczowym `suspend` mogą być wywoływane tylko z korutyny lub innej funkcji suspend.
- Pozwalają na wykonywanie operacji asynchronicznych w sposób sekwencyjny, bez blokowania wątku.

**Przykład:**
```kotlin
suspend fun fetchDataFromNetwork(): String {
    // pobieranie danych z sieci
    return "Dane"
}
```

### Scope (zakres korutyny)

- **Scope** zarządza korutynami – określa, do jakiej części aplikacji są one przypisane i kiedy powinny zostać anulowane.
- Najczęściej używane scope w Compose:
  - **viewModelScope** – korutyny są powiązane z ViewModelem i są anulowane, gdy ViewModel jest usuwany.
  - **lifecycleScope** – korutyny są powiązane z cyklem życia komponentu (np. aktywności).
  - **rememberCoroutineScope** – scope powiązany z composable, zarządza korutynami w kontekście composable.


### Dispatchers (wybór wątku)

- **Dispatchers** określają, na jakim wątku wykonywana jest korutyna:
  - `Dispatchers.Main` – główny wątek UI (domyślny w Compose i ViewModelu).
  - `Dispatchers.IO` – wątek do operacji wejścia/wyjścia (np. sieć, pliki, baza).
  - `Dispatchers.Default` – wątek do ciężkich obliczeń.
- Możesz przełączać się między dispatcherami za pomocą `withContext`.

**Podsumowanie:**
- Funkcje suspend pozwalają pisać asynchroniczny kod w prosty sposób.
- Scope zarządza cyklem życia korutyn i ich anulowaniem.
- Dispatchers wybierają odpowiedni wątek do zadania.

  
## Uruchamianie korutyn

Aby wykonać zadanie w tle, musisz uruchomić korutynę w odpowiednim scope. Najczęściej używane sposoby:

### `launch`

- Najpopularniejsza funkcja do uruchamiania korutyn.
- Używana do zadań, które nie muszą zwracać wyniku.
- Najczęściej wywoływana w `viewModelScope`, `lifecycleScope` lub `rememberCoroutineScope`.

**Przykład w ViewModelu:**
```kotlin
viewModelScope.launch {
    val data = fetchDataFromNetwork()
    // aktualizacja stanu UI
}
```

**Przykład w composable z własnym scope:**
```kotlin
val coroutineScope = rememberCoroutineScope()
Button(onClick = {
    coroutineScope.launch {
        // zadanie w tle, np. zapis do bazy
    }
}) {
    Text("Zapisz")
}
```

### `async`

- Używana, gdy chcesz uruchomić zadanie równolegle i otrzymać wynik jako `Deferred`.
- Pozwala na wykonywanie wielu zadań równolegle i pobranie wyników za pomocą `await()`.

**Przykład:**
```kotlin
viewModelScope.launch {
    val deferred1 = async { fetchData1() }
    val deferred2 = async { fetchData2() }
    val result1 = deferred1.await()
    val result2 = deferred2.await()
    // użyj obu wyników
}
```

### `LaunchedEffect`

- Specjalna funkcja kompozycyjna w Compose, który uruchamia korutynę przy starcie  lub po zmianie klucza.
- Idealny do inicjalizacji danych lub reagowania na zmiany parametrów.

**Przykład:**
```kotlin
@Composable
fun MyScreen(viewModel: MyViewModel = viewModel()) {
    LaunchedEffect(Unit) {
        viewModel.loadData()
    }
    // UI...
}
```
`LaunchedEffect` uruchamia korutynę, gdy composable pojawia się na ekranie **lub** gdy zmieni się wartość klucza (key). Jeśli jako klucz podasz `Unit`, efekt wykona się tylko raz przy pierwszym wyświetleniu composable. Jeśli podasz inną wartość (np. identyfikator, stan), efekt wykona się za każdym razem, gdy ta wartość się zmieni.

**Przykład:**
```kotlin
@Composable
fun UserScreen(userId: String, viewModel: UserViewModel = viewModel()) {
    LaunchedEffect(userId) {
        viewModel.loadUser(userId)
    }
    // UI...
}
```
W tym przykładzie:
- Jeśli `userId` się zmieni (np. użytkownik wybierze inny profil), `LaunchedEffect` ponownie uruchomi korutynę i załaduje dane nowego użytkownika.
- Gdybyś użył `Unit` jako klucza, efekt wykonałby się tylko raz, niezależnie od zmian `userId`.

---

## Praca w tle a długotrwałe zadania

- Do bardzo długich zadań (np. synchronizacja w tle, powiadomienia) używaj **WorkManager** lub **Foreground Service**.
- Korutyny są idealne do krótkich operacji powiązanych z życiem ekranu lub ViewModelu.

---

## Zalecenia i dobre praktyki  

- **Zawsze uruchamiaj korutyny w odpowiednim zakresie**  
  Dzięki temu korutyny są automatycznie anulowane, gdy komponent (ViewModel, composable, aktywność) przestaje istnieć. Zapobiega to wyciekom pamięci i niepotrzebnemu wykonywaniu zadań w tle.

- **Wybieraj odpowiedni dyspozytor**  
  Używaj `Dispatchers.IO` do operacji wejścia/wyjścia (sieć, pliki, baza), `Dispatchers.Default` do ciężkich obliczeń, a `Dispatchers.Main` do aktualizacji UI.

- **Nie blokuj wątku głównego**  
  Długotrwałe operacje zawsze wykonuj w tle (np. z użyciem `withContext(Dispatchers.IO)`).

- **Stosuj funkcje suspend do operacji asynchronicznych**  
  Pozwala to pisać czytelny, sekwencyjny kod bez blokowania wątków.

- **Używaj LaunchedEffect do inicjalizacji i reagowania na zmiany parametrów w composable**  
  Dzięki temu masz pewność, że zadanie wykona się tylko wtedy, gdy jest to potrzebne.

  
## Więcej informacji

- [Kotlin Coroutines – Android Developers](https://developer.android.com/kotlin/coroutines)
- [WorkManager – Android Developers](https://developer.android.com/topic/libraries/architecture/workmanager)
- [Praca w tle w Compose – dokumentacja](https://developer.android.com/jetpack/compose/side-effects#launchedeffect)

---

### 🧭 **Następny temat:** [Bazy danych - Room](https://github.com/MarcinRod/AndroidLecture2025/blob/main/11%20Bazy%20danych%20-%20Room.md)