# Komponent nawigacji w Jetpack Compose

Jetpack Compose oferuje nowoczesny sposób zarządzania nawigacją w aplikacjach Android dzięki bibliotece **Navigation Compose**. Pozwala ona w prosty sposób przechodzić między ekranami (tzw. composable destinations) oraz przekazywać dane pomiędzy nimi.

---
## Stos nawigacji (Back Stack) w Navigation Compose

Navigation Compose zarządza tzw. **stosem nawigacji** (back stack), czyli listą ekranów, które użytkownik odwiedził. Dzięki temu możesz łatwo obsługiwać powrót do poprzednich ekranów oraz kontrolować historię nawigacji.

### Jak działa stos?

- Każde przejście do nowego ekranu dodaje nową destynację na szczyt stosu.
- Powrót usuwa ostatni ekran ze stosu i pokazuje poprzedni.
- Możesz usuwać wiele ekranów naraz lub czyścić stos do wybranej destynacji.


>**Wskazówka:**  
Zrozumienie działania stosu jest kluczowe dla poprawnej obsługi nawigacji i przewidywalnego zachowania przycisku "wstecz" w aplikacji.

---

## Podstawowe elementy nawigacji


- **NavHost**  
  To specjalny composable, który zarządza całą nawigacją w aplikacji. Określasz w nim, jaka jest trasa początkowa (`startDestination`) oraz definiujesz wszystkie możliwe ekrany (destinations), do których można przejść. NavHost samodzielnie wyświetla odpowiedni composable w zależności od aktualnej destynacji.

  **Przykład:**
  ```kotlin
  NavHost(navController = navController, startDestination = "ekranA") {
      composable("ekranA") { EkranA(navController) }
      composable("ekranB") { EkranB(navController) }
  }
  ```

- **NavController**  
  To obiekt, który steruje nawigacją. Umożliwia przechodzenie między ekranami, cofanie się do poprzednich ekranów oraz przekazywanie argumentów. Tworzysz go zwykle za pomocą funkcji `rememberNavController()`.


  **Przykład:**
  ```kotlin
  val navController = rememberNavController()
  ```

- **Composable destinations**  

  To funkcje composable, które są traktowane jako "ekrany" w nawigacji. Każda destynacja jest identyfikowana przez unikalny klucz (np. "ekranA", "ekranB"). W definicji NavHost przypisujesz każdemu kluczowi odpowiednią funkcję composable.

  **Przykład:**
  ```kotlin
  composable("ekranA") { EkranA(navController) }
  composable("ekranB") { EkranB(navController) }
  ```

**Podsumowanie:**  
NavHost zarządza trasami, NavController steruje przejściami, a composable destinations to Twoje ekrany. Dzięki temu nawigacja w Compose jest przejrzysta, deklaratywna i łatwa do rozbudowy.

---

## Przykład podstawowej konfiguracji

1. **Dodaj zależność w pliku build.gradle:**
   ```groovy
   implementation "androidx.navigation:navigation-compose:2.7.7"
   ```

2. **Tworzenie NavController i NavHost:**
   ```kotlin
   val navController = rememberNavController()
   NavHost(navController = navController, startDestination = "ekranA") {
       composable("ekranA") { EkranA(navController) }
       composable("ekranB") { EkranB(navController) }
   }
   ```

3. **Przechodzenie między ekranami:**
   ```kotlin
   // W EkranA
   Button(onClick = { navController.navigate("ekranB") }) {
       Text("Przejdź do ekranu B")
   }
   ```

---
## Przechodzenie między ekranami – podstawowe metody NavController

Do nawigacji między ekranami w Jetpack Compose służy obiekt `NavController`. Oto najważniejsze metody, które warto znać:

### 1. Przechodzenie do innego ekranu

Aby przejść do innej destynacji, użyj metody `navigate`:

```kotlin
navController.navigate("ekranB")
```


### 2. Powrót do poprzedniego ekranu

Aby wrócić do poprzedniego ekranu (poprzedniej destynacji na stosie), użyj:

```kotlin
navController.popBackStack()
```

### 3. Powrót do konkretnej destynacji

Możesz wrócić do konkretnego ekranu (np. do ekranu głównego), usuwając wszystkie ekrany powyżej:

```kotlin
navController.popBackStack("ekranA", inclusive = false)
```
- Jeśli `inclusive = true`, także ekran o nazwie `"ekranA"` zostanie usunięty ze stosu.

### 4. Usuwanie ekranu z historii po przejściu (singleTop)

Aby uniknąć duplikowania ekranów na stosie, możesz użyć opcji `launchSingleTop`:

```kotlin
navController.navigate("ekranA") {
    launchSingleTop = true
}
```

### 5. Przechodzenie z czyszczeniem stosu (np. po logowaniu)

Aby przejść do ekranu i usunąć wszystkie poprzednie ekrany (np. po zalogowaniu):

```kotlin
navController.navigate("ekranStartowy") {
    popUpTo("ekranLogowania") { inclusive = true }
}
```

---

**Podsumowanie:**  
- `navigate(route)` – przejście do ekranu
- `popBackStack()` – powrót do poprzedniego ekranu
- `popBackStack(route, inclusive)` – powrót do konkretnej destynacji
- Opcje nawigacji (`launchSingleTop`, `popUpTo`) pozwalają kontrolować stos nawigacji

Dzięki tym metodom możesz w pełni zarządzać przepływem użytkownika w aplikacji Compose.

---
### Jak najlepiej definiować destynacje nawigacji

Aby kod nawigacji był czytelny, bezpieczny i łatwy w utrzymaniu, warto unikać  ciągów znaków na sztywno wpisanych w kodzie w definicjach tras. Zamiast tego zaleca się korzystanie z:

#### 1. Enum class

Możesz zdefiniować wszystkie destynacje jako wartości typu enum:

```kotlin
enum class Destynacja(val route: String) {
    EkranA("ekranA"),
    EkranB("ekranB"),
    Szczegoly("szczegoly/{id}")
}

// Użycie:
NavHost(navController, startDestination = Destynacja.EkranA.route) {
    composable(Destynacja.EkranA.route) { EkranA(navController) }
    composable(Destynacja.EkranB.route) { EkranB(navController) }
    composable(Destynacja.Szczegoly.route) { /* ... */ }
}
```

#### 2. Sealed class (bardziej elastyczne)

Sealed class pozwala na przekazywanie argumentów i generowanie tras dynamicznie:

```kotlin
sealed class Destynacja(val route: String) {
    object EkranA : Destynacja("ekranA")
    object EkranB : Destynacja("ekranB")
    data class Szczegoly(val id: String) : Destynacja("szczegoly/$id") {
        companion object {
            const val routePattern = "szczegoly/{id}"
        }
    }
}

// Użycie:
NavHost(navController, startDestination = Destynacja.EkranA.route) {
    composable(Destynacja.EkranA.route) { EkranA(navController) }
    composable(Destynacja.EkranB.route) { EkranB(navController) }
    composable(Destynacja.Szczegoly.routePattern) { backStackEntry ->
        val id = backStackEntry.arguments?.getString("id") ?: ""
        SzczegolyEkran(id)
    }
}

// Przejście z argumentem:
navController.navigate(Destynacja.Szczegoly("123").route)
```

#### Zalety takiego podejścia

- **Bezpieczeństwo typów** – mniejsze ryzyko literówek i błędów w trasach.
- **Łatwiejsza rozbudowa** – dodanie nowej destynacji to tylko nowy obiekt w enum/sealed class.
- **Lepsza czytelność** – wszystkie trasy są zebrane w jednym miejscu.

To podejście jest szczególnie polecane w większych projektach Compose.
## Przekazywanie argumentów

W Navigation Compose możesz przekazywać argumenty do destynacji i odczytywać je w sposób typowany za pomocą funkcji `navArgument`. Dzięki temu możesz np. wymusić typ argumentu lub ustawić wartość domyślną.

Możesz przekazywać dane między ekranami na dwa sposoby:

### 1. Argumenty w ścieżce (path arguments)

Argumenty przekazywane w ścieżce są częścią trasy, np. `"szczegoly/{id}"`. Są one wymagane i muszą być zawsze podane podczas nawigacji.

**Przykład:**
```kotlin
NavHost(navController, startDestination = "szczegoly/{id}") {
    composable(
        route = "szczegoly/{id}",
        arguments = listOf(navArgument("id") { type = NavType.IntType })
    ) { backStackEntry ->
        val id = backStackEntry.arguments?.getInt("id") ?: 0
        SzczegolyEkran(id)
    }
}

// Przejście z argumentem:
navController.navigate("szczegoly/123")
```

### 2. Argumenty jako query (opcjonalne, po znaku `?`)

Argumenty query są przekazywane po znaku zapytania w ścieżce, np. `"ekranB?imie={imie}"`. Mogą być opcjonalne i mieć wartości domyślne.

**Przykład:**
```kotlin
NavHost(navController, startDestination = "ekranA") {
    composable(
        route = "ekranB?imie={imie}",
        arguments = listOf(navArgument("imie") {
            type = NavType.StringType
            defaultValue = "Gość"
            nullable = true
        })
    ) { backStackEntry ->
        val imie = backStackEntry.arguments?.getString("imie")
        EkranB(imie)
    }
}

// Przejście bez argumentu:
navController.navigate("ekranB")
// Przejście z argumentem:
navController.navigate("ekranB?imie=Anna")
```


### navArguments w ścieżkach zdefiniowanych przez enum lub sealed class

Najlepszą praktyką jest rozdzielenie **wzorca trasy** (pattern, np. `"szczegoly/{id}"` lub `"ekranB?imie={imie}"`) od **konkretnej trasy** (np. `"szczegoly/123"` lub `"ekranB?imie=Anna"`). Dzięki temu możesz korzystać z typowanego przekazywania argumentów (`navArgument`) i zachować bezpieczeństwo typów.

#### Przykład z sealed class – argument w ścieżce (path)

```kotlin
sealed class Destynacja(val route: String) {
    object EkranA : Destynacja("ekranA")
    object EkranB : Destynacja("ekranB")
    object Szczegoly : Destynacja("szczegoly/{id}") {
        fun createRoute(id: String) = "szczegoly/$id"
        const val ARG_ID = "id"
    }
}
```

**Definicja NavHost z navArgument:**
```kotlin
NavHost(navController, startDestination = Destynacja.EkranA.route) {
    composable(Destynacja.EkranA.route) { EkranA(navController) }
    composable(Destynacja.EkranB.route) { EkranB(navController) }
    composable(
        route = Destynacja.Szczegoly.route,
        arguments = listOf(navArgument(Destynacja.Szczegoly.ARG_ID) { type = NavType.StringType })
    ) { backStackEntry ->
        val id = backStackEntry.arguments?.getString(Destynacja.Szczegoly.ARG_ID) ?: ""
        SzczegolyEkran(id)
    }
}
```

**Nawigacja z argumentem:**
```kotlin
navController.navigate(Destynacja.Szczegoly.createRoute("123"))
```

---

#### Przykład z sealed class – argument jako query (opcjonalny)

```kotlin
sealed class Destynacja(val route: String) {
    object EkranA : Destynacja("ekranA")
    object EkranB : Destynacja("ekranB?imie={imie}") {
        fun createRoute(imie: String?) =
            if (imie != null) "ekranB?imie=$imie" else "ekranB"
        const val ARG_IMIE = "imie"
    }
}
```

**Definicja NavHost z navArgument:**
```kotlin
NavHost(navController, startDestination = Destynacja.EkranA.route) {
    composable(Destynacja.EkranA.route) { EkranA(navController) }
    composable(
        route = Destynacja.EkranB.route,
        arguments = listOf(navArgument(Destynacja.EkranB.ARG_IMIE) {
            type = NavType.StringType
            defaultValue = "Gość"
            nullable = true
        })
    ) { backStackEntry ->
        val imie = backStackEntry.arguments?.getString(Destynacja.EkranB.ARG_IMIE)
        EkranB(imie)
    }
}
```

**Nawigacja z argumentem lub bez:**
```kotlin
navController.navigate(Destynacja.EkranB.createRoute("Anna")) // ekranB?imie=Anna
navController.navigate(Destynacja.EkranB.createRoute(null))   // ekranB
```


Dzięki temu podejściu możesz wygodnie i bezpiecznie przekazywać zarówno argumenty wymagane (w ścieżce), jak i opcjonalne (query) w trasach zdefiniowanych przez enum lub sealed class.


---

## Zagnieżdżanie grafów nawigacji (Nested Navigation Graphs)

W większych aplikacjach Jetpack Compose często stosuje się **zagnieżdżone grafy nawigacji** (nested navigation graphs), aby lepiej organizować trasy i zarządzać złożonymi przepływami ekranów. Pozwala to na podział aplikacji na logiczne moduły (np. logowanie, główny ekran, ustawienia), z których każdy ma własny podgraf nawigacji.

### Jak zdefiniować zagnieżdżony graf?

Wewnątrz głównego `NavHost` możesz dodać kolejne grafy za pomocą funkcji `navigation`:

```kotlin
NavHost(navController, startDestination = "auth") {
    // Główny podgraf logowania
    navigation(startDestination = "login", route = "auth") {
        composable("login") { LoginScreen(navController) }
        composable("register") { RegisterScreen(navController) }
    }
    // Podgraf główny aplikacji
    navigation(startDestination = "home", route = "main") {
        composable("home") { HomeScreen(navController) }
        composable("settings") { SettingsScreen(navController) }
    }
}
```

### Przechodzenie między podgrafami

Aby przejść do ekranu w innym podgrafie, użyj pełnej ścieżki (np. `"main/home"` lub `"auth/login"`):

```kotlin
navController.navigate("main/home")
```

### Zalety zagnieżdżonych grafów

- **Lepsza organizacja kodu** – każdy moduł ma własny, czytelny podgraf.
- **Łatwiejsze zarządzanie przepływem** – np. możesz łatwo wyczyścić stos logowania po zalogowaniu i przejść do głównego grafu.
- **Możliwość ponownego użycia** – np. ten sam podgraf ustawień możesz użyć w różnych miejscach aplikacji.


**Więcej o zagnieżdżaniu grafów:**  
[Oficjalna dokumentacja – Nested Navigation Graphs](https://developer.android.com/jetpack/compose/navigation#nested-nav)

---

## Zalety Navigation Compose

- **Deklaratywne zarządzanie trasami i ekranami**  
  Wszystkie trasy i ekrany definiujesz w jednym miejscu, co ułatwia czytanie i utrzymanie kodu. Struktura nawigacji jest przejrzysta i łatwa do rozbudowy.

- **Łatwe przekazywanie argumentów**  
  Możesz przekazywać argumenty zarówno w ścieżce, jak i jako query, z pełnym wsparciem dla typów i wartości domyślnych dzięki `navArgument`.

- **Bezpieczeństwo typów i czytelność**  
  Dzięki enum i sealed class możesz unikać literówek i błędów w trasach, a także łatwo zarządzać rozwojem aplikacji.

- **Obsługa stosu nawigacji**  
  Navigation Compose automatycznie zarządza historią ekranów (back stack), co pozwala na intuicyjne korzystanie z przycisku "wstecz" i kontrolowanie przepływu użytkownika.

- **Integracja z animacjami i typowymi elementami nawigacji**  
  Łatwo połączysz nawigację z animacjami przejść, dolnym paskiem nawigacji (BottomNavigation), zakładkami (Tabs) czy boczną szufladą (Drawer).

- **Wsparcie dla zagnieżdżonych grafów**  
  Możesz organizować aplikację w logiczne moduły i zarządzać złożonymi przepływami ekranów dzięki nested navigation graphs.

- **Łatwa integracja z architekturą aplikacji**  
  Navigation Compose dobrze współpracuje z ViewModelami, State Hoisting i innymi wzorcami architektonicznymi Compose.

---

Navigation Compose to nowoczesny i zalecany sposób obsługi nawigacji w aplikacjach Jetpack Compose, który znacząco upraszcza zarządzanie przepływem ekranów i poprawia jakość kodu.

---

## Dokumentacja

- [Oficjalna dokumentacja Navigation Compose](https://developer.android.com/jetpack/compose/navigation)
- [Przykłady na GitHub](https://github.com/android/compose-samples/tree/main/NavigationAdvancedSample)

Navigation Compose to nowoczesny i zalecany sposób obsługi nawigacji w aplikacjach Jetpack Compose.

---
### 🧭 **Następny temat:** [Intencje](https://github.com/MarcinRod/AndroidLecture2025/blob/main/07%20Intencje.md)