# 📁 Struktura projektu Android Studio i skrypty Gradle

Tworząc aplikację w Android Studio, projekt składa się z wielu katalogów i plików, które pełnią określone role. W tym rozdziale omówimy:

- najważniejsze elementy struktury projektu,
- podstawy konfiguracji przy użyciu Gradle,
- Android Studio jako środowisko deweloperskie.

---

### 🛠️ Android Studio – podstawy

**Android Studio** to oficjalne IDE (zintegrowane środowisko programistyczne) do tworzenia aplikacji na Androida, oparte na IntelliJ IDEA od JetBrains.

#### 🔑 Główne cechy Android Studio:

- Wsparcie dla **Jetpack Compose**, **ViewModel**, **LiveData**, **Room**, itp.
- Zaawansowane **narzędzia do debugowania** (Logcat, Debugger, Profiler).
- **Emulator** do testowania aplikacji bez fizycznego urządzenia.
- **Compose Preview** – podgląd komponentów UI bez uruchamiania aplikacji.
- Automatyczne **aktualizacje bibliotek i SDK**.
- Integracja z **Firebase**, **Git**, **Gradle**.
- **Wtyczki** do obsługi wielu języków i technologii.


#### 📈 Najważniejsze panele

- **Project** – struktura katalogów i plików.
- **Editor** – główny edytor kodu i layoutów.
- **Logcat** – logi systemowe i z aplikacji.
- **Build** – status kompilacji.
- **Device Manager** – zarządzanie emulatorami i urządzeniami fizycznymi.

---
## 🔍 Wybrane szczegóły projektu w Android Studio

W tej sekcji omówimy kilka ważnych aspektów pracy w Android Studio, które często pojawiają się na początku nauki tworzenia aplikacji mobilnych:

- Nazwa paczki (package name)
- Widoki projektu w Android Studio vs rzeczywista struktura na dysku
- Debugowanie i Logcat
- Emulator Androida

---

### 🏷️ Nazwa paczki (Package Name)

**Nazwa paczki** to unikalny identyfikator Twojej aplikacji w systemie Android. Jest ona zapisywana w pliku `AndroidManifest.xml` oraz w `build.gradle` jako `applicationId`.

#### 📌 Przykład:

```kotlin
// app/build.gradle
defaultConfig {
    applicationId "com.example.myapp"
    ...
}
```

- **`com.example.myapp`** – to przykład konwencji "odwróconej domeny".
- **Nazwa paczki ≠ struktura katalogów**, ale zwykle są ze sobą zgodne dla czytelności.
- Nazwa paczki wpływa m.in. na:
  - miejsce instalacji aplikacji na urządzeniu,
  - identyfikację aplikacji w Google Play,
  - generowanie pliku `BuildConfig` i kluczy dla Firebase.

> 📌 Uwaga: Zmiana `applicationId` w `build.gradle` pozwala na publikację innej wersji aplikacji bez kolizji z poprzednią.

---

### 🗂️ Widoki projektu: Android Studio vs system plików

Android Studio oferuje różne **widoki projektu**, aby ułatwić pracę:

#### 📁 Widok **Android**

- Grupuje pliki logicznie (np. wszystkie layouty razem).
- Ukrywa zbędne katalogi (`build`, `intermediates`).
- Najczęściej używany przez początkujących i w codziennej pracy.

#### 🗃️ Widok **Project**

- Pokazuje **rzeczywistą strukturę katalogów na dysku**.
- Przydatny przy pracy z plikami `gradle`, konfiguracjami CI/CD itp.
- Ułatwia poruszanie się po projekcie w systemie plików.



#### 📂 Struktura katalogów projektu

Po utworzeniu nowego projektu w Android Studio, domyślna struktura na dysku wygląda mniej więcej tak:

```
MyApp/
├── app/
│   ├── build.gradle
│   ├── src/
│   │   ├── androidTest/   
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── com/example/myapp/
│   │   │   │       └── MainActivity.kt
│   │   │   ├── res/
│   │   │   │   ├── layout/
│   │   │   │   ├── values/
│   │   │   │   └── drawable/
│   │   │   └── AndroidManifest.xml
│   │   └── test/         
├── build.gradle
├── settings.gradle
├── gradle.properties
└── gradle/
```

W widoku Android, przykładowa struktura dla nowego projektu jest następująca:

```
MyApp (Android)
├── manifests
│   └── AndroidManifest.xml
├── java
│   ├── com.example.myapp
│   │   └── MainActivity.kt
│   └── (Testy i AndroidTest)
├── res
│   ├── drawable/       
│   ├── layout/         
│   ├── mipmap/          
│   ├── values/          
│   └── themes/           
├── Gradle Scripts
│   ├── build.gradle (Project: MyApp)
│   ├── build.gradle (Module: app)
│   ├── settings.gradle
│   └── gradle.properties

```
#### 📝 Opis niektórych elementów:

- **`manifests/AndroidManifest.xml`**  
  - Deklaruje podstawowe informacje o aplikacji: pakiet, aktywności, uprawnienia.

- **`java/com.example.myapp`**  
  - Główna przestrzeń kodu aplikacji. Tu znajdują się klasy i funkcje (np. `MainActivity.kt`, `Repository.kt`, `ViewModel.kt`).
  - Możesz tworzyć podfoldery według architektury: `ui`, `data`, `domain`, itp.

- **`res/`**  
  - Zasoby aplikacji – obrazy, kolory, teksty, style itp.

- **`Gradle Scripts/`**  
  - Skrypty konfiguracyjne:
    - `build.gradle (Project)` – konfiguracja ogólna projektu (np. wersje pluginów, repozytoria).
    - `build.gradle (Module)` – konfiguracja modułu aplikacji (`applicationId`, zależności, wersje).
   

> 💡 Widok "Android" nie pokazuje katalogów `build/`, `intermediates/`, `outputs/` – są one ukryte, aby nie przeszkadzały w codziennej pracy.

---

📌 Widok "Android" to najwygodniejszy sposób przeglądania projektu podczas codziennego programowania, ale czasem warto przełączyć się na widok "Project", by zobaczyć dokładny układ plików na dysku.

---


## 📱 Android Emulator

**Emulator Androida** pozwala uruchomić aplikację bez fizycznego urządzenia. Wszystkie urządzenia zarządzanie są przez `Device Manager`

### ✨ Zalety:

1. **Szybkie i wygodne testowanie**
   - Możliwość natychmiastowego uruchomienia aplikacji bez potrzeby podłączania fizycznego urządzenia.
  
2. **Wiele konfiguracji urządzeń**
   - Możliwość tworzenia wielu wirtualnych urządzeń (AVD – Android Virtual Devices) z różnymi wersjami systemu Android, rozmiarami ekranów, dpi, RAM, itp.
   - Przydatne do testowania responsywności UI na różnych typach urządzeń (np. telefon, tablet, foldable).

3. **Symulacja funkcji sprzętowych**
   - Możliwość symulowania:
     - lokalizacji GPS,
     - połączeń telefonicznych i SMS,
     - poziomu baterii,
     - warunków sieciowych (np. 2G, 3G, Wi-Fi),
     - czujników (akcelerometr, żyroskop, itp.),
     - aparatów (kamera frontowa i tylna),
     - rotacji ekranu.
   - Te funkcje często są trudne do przetestowania na jednym fizycznym urządzeniu.

4. **Lepsze logowanie i debugowanie**
   - Łatwa integracja z narzędziami Android Studio: Logcat, Debugger, Profiler.
   - Szybki dostęp do systemowych logów i możliwości inspekcji zasobów.

5. **Bezpieczeństwo i izolacja**
   - Emulator działa w odizolowanym środowisku – nie narażasz fizycznego telefonu na uszkodzenie lub przypadkowe nadpisanie danych.

6. **Brak potrzeby zakupu wielu urządzeń**
   - Pozwala testować aplikacje na różnych wersjach Androida (nawet starszych) i konfiguracjach sprzętowych bez inwestycji w realne telefony czy tablety.

7. **Szybkie resetowanie i czyszczenie danych**
   - Możliwość szybkiego przywrócenia emulatora do stanu początkowego – przydatne przy testach związanych z onboardingiem, uprawnieniami czy pierwszym uruchomieniem.


### ❌ Wady emulatora:

1. **Wysokie zużycie zasobów**
   - Emulator zużywa znaczną ilość pamięci RAM, CPU i przestrzeni dyskowej.
   - Na słabszych komputerach może znacząco obniżyć wydajność systemu.

2. **Wolniejsze działanie w porównaniu do urządzeń fizycznych**
   - Nawet przy użyciu akceleracji sprzętowej emulator może działać mniej płynnie.
   - Czas uruchamiania aplikacji bywa dłuższy.

3. **Problemy z obsługą funkcji sprzętowych**
   - Nie wszystkie komponenty sprzętowe są w pełni symulowane (np. NFC, Bluetooth, czujniki, kamera).
   - Działanie funkcji może różnić się od tego na prawdziwym urządzeniu.

---
## 🐞 Debugowanie i Logcat w Android Studio

Debugowanie to proces znajdowania i usuwania błędów (bugów) w kodzie. Android Studio oferuje bogaty zestaw narzędzi do debugowania aplikacji, z czego najważniejsze to **Logcat** oraz **debugger z system punktów przerwań (ang. *breakpoint*)**.

---

### 🧪 Debugowanie aplikacji w Android Studio

Android Studio pozwala debugować aplikację krok po kroku, zatrzymywać wykonywanie w konkretnych miejscach oraz przeglądać wartości zmiennych i stos wywołań.

#### 🔹 Breakpoint

- Breakpoint to punkt zatrzymania – miejsce, w którym debugger zatrzyma wykonywanie programu.
- Można go ustawić klikając w lewy margines edytora kodu przy danej linii.
- Umożliwia sprawdzenie stanu aplikacji w danym momencie: wartości zmiennych, wywołania metod itp.

#### 🔹 Debugger

- Aby uruchomić aplikację w trybie debugowania, kliknij przycisk z ikoną robaka ("Debug app").
- Po zatrzymaniu w punkcie przerwania możesz przeglądać stan zmiennych, rejestrów i stos wywołań.

---

## 📋 Logcat – analiza błędów i diagnostyka wyjątków

**Logcat** to najważniejsze narzędzie diagnostyczne Android Studio – umożliwia śledzenie logów systemowych oraz błędów aplikacji w czasie rzeczywistym. Dzięki niemu łatwo wykryjesz wyjątki, komunikaty błędów, ostrzeżenia czy działania systemowe.

---

### 🔹 Poziomy logowania

| Poziom | Znaczenie                   |
|--------|-----------------------------|
| `V`    | Verbose – wszystkie logi    |
| `D`    | Debug – logi deweloperskie  |
| `I`    | Info – ogólne informacje    |
| `W`    | Warn – ostrzeżenia          |
| `E`    | Error – błędy krytyczne     |
| `A`    | Assert – warunki krytyczne  |

---

### 🔹 Przykład logowania w Kotlinie:

```kotlin
import android.util.Log

Log.d("LoginScreen", "Kliknięto przycisk logowania")
Log.w("LoginScreen", "Brak połączenia z serwerem")
Log.e("LoginScreen", "Błąd autoryzacji", exception)
```

---

### 🧨 Przykład wyjątku (Exception) w Logcat

Załóżmy, że aplikacja rzuciła `NullPointerException`. W Logcat zobaczysz coś takiego:

```bash
E/AndroidRuntime: FATAL EXCEPTION: main
    Process: com.example.myapp, PID: 31548
    java.lang.NullPointerException: Attempt to invoke virtual method 'java.lang.String android.widget.TextView.getText()' on a null object reference
        at com.example.myapp.ui.MainActivity.onCreate(MainActivity.kt:25)
        at android.app.Activity.performCreate(Activity.java:7893)
        ...
```

---

### 🧠 Jak analizować wyjątek?

1. **Typ wyjątku**: `NullPointerException`  
   ➤ To oznacza, że aplikacja próbowała odwołać się do obiektu, który był `null`.

2. **Opis błędu**:
   ```
   Attempt to invoke virtual method 'java.lang.String android.widget.TextView.getText()' on a null object reference
   ```
   ➤ Wywołano `getText()` na niezainicjalizowanym obiekcie `TextView`.

3. **Lokalizacja błędu**:
   ```
   at com.example.myapp.ui.MainActivity.onCreate(MainActivity.kt:25)
   ```
   ➤ Błąd znajduje się w klasie `MainActivity`, linia 25.

---

### 🔧 Typowy kod, który mógł wywołać ten wyjątek:

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    val text = findViewById<TextView>(R.id.myTextView)
    val input = text.text.toString() // <- crash jeśli `text` to null
}
```
---

### 🔍 Filtrowanie Logcat:

Aby szybciej znaleźć błąd lub interesującą nas informacje w Logcat:
- Wybierz odpowiedni poziom logowania np. **Error**
- W filtrze wpisz nazwę aplikacji lub tag, np.: `com.example.myapp`, `LoginScreen` 


---

### 🧪 Debugging + Logcat = Pełna analiza

Po znalezieniu błędu w Logcat, warto:
- Ustawić **breakpoint** w podejrzanej linii
- Uruchomić aplikację w trybie debugowania i przeanalizować kod

---

📌 **Wskazówka**: Logcat działa najlepiej, gdy regularnie logujesz istotne informacje w kodzie – nie tylko błędy, ale również przebieg aplikacji (np. przejścia między ekranami, dane wejściowe użytkownika, odpowiedzi z API).


---

### 🧰 Inne narzędzia debugujące:

- **Layout Inspector** – do analizy struktury widoku w czasie rzeczywistym.
- **Network Profiler** – podgląd zapytań sieciowych.
- **Memory Profiler** – do monitorowania zużycia pamięci (szczególnie przy wyciekach pamięci).
- **CPU Profiler** – do analizowania działania aplikacji w czasie rzeczywistym.

---

## ⚙️ Gradle – system budowania

**Gradle** to system automatyzacji budowania, który Android Studio wykorzystuje do:

- kompilacji aplikacji,
- zarządzania zależnościami (np. biblioteki Jetpack),
- tworzenia różnych wersji (np. debug/release),
- uruchamiania testów,
- konfiguracji środowisk produkcyjnych i testowych.

### 🧠 Jak działa Gradle?

- Gradle analizuje pliki konfiguracyjne (`build.gradle`) i tworzy **graf zadań**.
- Wykonuje zadania w zależności od celu (np. `build`, `assembleDebug`).
- Pobiera zależności z repozytoriów takich jak **Google** czy **Maven Central**.
- Dzięki **kotlin dsl (`.gradle.kts`)** możliwa jest alternatywna konfiguracja w języku Kotlin.

---

## 📄 Przykładowe pliki Gradle

### 🔤 `build.gradle` (główny projekt)

Ten plik służy do konfigurowania ustawień globalnych dla wszystkich modułów projektu (np. aplikacji, bibliotek itp.).



```groovy
buildscript {
    ext {
        kotlin_version = '1.9.22'
    }
    repositories {
        google()
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:8.4.0'
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
}
```


### 🔤 `build.gradle` (moduł app)

Zawiera informacje dotyczące kompilacji Twojej aplikacji – m.in.:

- wtyczki (plugins),
- poziomy SDK,
- zależności (dependencies),
- opcje kompilacji i testów.

#### 📌 Przykład:

```groovy
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}

android {
    compileSdk 34

    defaultConfig {
        applicationId "com.example.myapp"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation 'androidx.core:core-ktx:1.13.0'
    implementation 'androidx.compose.ui:ui:1.6.4'
}
```
## 🧱 Sekcje pliku `build.gradle` modułu aplikacji (`app/`)

Plik `app/build.gradle` (lub `build.gradle.kts`) zawiera konfigurację konkretnego modułu – w przypadku Androida jest to najczęściej moduł aplikacji. Poniżej opisujemy kluczowe sekcje tego pliku.

---

### 🔌 `plugins`

Deklaruje wtyczki potrzebne do budowy tego modułu. Dla aplikacji Android i języka Kotlin będą to zazwyczaj:

```groovy
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}
```

---

### 🧱 `android`

To główna sekcja konfigurująca projekt Android. Wewnątrz niej znajdziesz kilka podsekcji:

```groovy
android {
    namespace 'com.example.myapp' // od Android Studio Flamingo zamiast applicationId
    compileSdk 34

    defaultConfig {
        applicationId "com.example.myapp"  // unikalny identyfikator aplikacji (package name)
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }

    buildTypes {
        release {
            minifyEnabled false  // Proguard/R8 – kompresja i obfuskacja
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
        debug {
            applicationIdSuffix ".debug" // aplikacja debug ma inną nazwę pakietu
            debuggable true
        }
    }

    buildFeatures {
        compose true  // aktywuje Jetpack Compose
    }

    composeOptions {
        kotlinCompilerExtensionVersion '1.6.4'
    }

    
}
```

#### 📌 Opis podsekcji:

| Sekcja                  | Opis |
|-------------------------|------|
| `namespace`             | Deklaruje przestrzeń nazw dla klas (zastępuje `applicationId` w nowszych projektach). |
| `compileSdk`            | Wersja SDK, z którą kompilujemy aplikację. |
| `defaultConfig`         | Domyślne ustawienia aplikacji (ID, wersja, SDK, testy). |
| `buildTypes`            | Definiuje typy kompilacji (debug, release). |
| `buildFeatures`         | Aktywuje wybrane funkcje np. Compose, ViewBinding, DataBinding. |
| `composeOptions`        | Ustawienia specyficzne dla Jetpack Compose. |

---

### 📦 `dependencies`

Sekcja zawierająca wszystkie zależności – biblioteki zewnętrzne, AndroidX, Compose, Retrofit, Firebase itd.

```groovy
dependencies {
    implementation 'androidx.core:core-ktx:1.13.0'
    implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.7.0'
    implementation 'androidx.compose.ui:ui:1.6.4'
    implementation 'com.google.dagger:hilt-android:2.51'

    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.5'
    debugImplementation 'androidx.compose.ui:ui-tooling:1.6.4'
}
```

#### Typy zależności:

| Typ                    | Znaczenie |
|------------------------|-----------|
| `implementation`       | Główna zależność – widoczna tylko w tym module. |
| `testImplementation`   | Zależności używane tylko podczas testów jednostkowych. |
| `androidTestImplementation` | Zależności do testów instrumentalnych. |
| `debugImplementation`  | Używane tylko w kompilacji debug (np. UI tooling). |

---

📌 Wskazówka: Android Studio automatycznie synchronizuje projekt po zmianach w plikach Gradle (jest to wymagane), gdyby jednak synchronizacja nie wykonała się automatycznie użyj przycisku **"Sync Now"**.


---

## 🧩 Plugins vs Dependencies – jaka jest różnica?

| Cecha              | Plugins (wtyczki)                                      | Dependencies (biblioteki)                                |
|--------------------|--------------------------------------------------------|-----------------------------------------------------------|
| Co to jest?         | Rozszerzenia Gradle, które modyfikują sposób działania kompilacji | Kody zewnętrzne, które dołączasz do aplikacji            |
| Przykład            | `com.android.application`, `kotlin-android`           | `androidx.compose.ui:ui`, `retrofit2:retrofit`            |
| Gdzie się je dodaje | Sekcja `plugins`                                      | Sekcja `dependencies`                                     |
| Funkcja             | Zmienia konfigurację projektu                          | Dodaje funkcjonalność do aplikacji (np. baza danych)   |

---
## 📦 Nowoczesne zarządzanie wersjami: `libs.versions.toml`

Od Gradle 7.0 można używać pliku `libs.versions.toml` do centralnego zarządzania wersjami bibliotek i pluginów.

### 🔧 Lokalizacja:

```
MyApp/
└── gradle/
    └── libs.versions.toml
```

### 📄 Przykładowa zawartość `libs.versions.toml`:

```toml
[versions]
kotlin = "1.9.22"
compose = "1.6.4"
hilt = "2.51"

[libraries]
kotlin-stdlib = { module = "org.jetbrains.kotlin:kotlin-stdlib", version.ref = "kotlin" }
compose-ui = { module = "androidx.compose.ui:ui", version.ref = "compose" }
hilt-android = { module = "com.google.dagger:hilt-android", version.ref = "hilt" }

[plugins]
android-application = { id = "com.android.application", version = "8.4.0" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
```

### 📄 build.gradle.kts z użyciem `libs.versions.toml`

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
}

dependencies {
    implementation(libs.kotlin.stdlib)
    implementation(libs.compose.ui)
}
```

---

✅ **Zalety `libs.versions.toml`**:
- centralne miejsce do zarządzania wersjami,
- brak duplikacji wersji między modułami,
- łatwa aktualizacja zależności.
---


✅ **Sprawdź oficjalną dokumentację** [Android Studio](https://developer.android.com/studio) i [Gradle](https://docs.gradle.org/current/userguide/userguide.html).


### 🧭 **Następny temat:** [Koncepcję aktywności i jej cykl życia](https://github.com/MarcinRod/AndroidLecture2025/blob/main/03%20Aktywność.md)
