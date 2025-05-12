# AndroidManifest.xml i system pozwoleń w Androidzie

## AndroidManifest.xml – do czego służy?

Plik **AndroidManifest.xml** to jeden z najważniejszych plików w każdej aplikacji Android. Znajduje się w katalogu głównym projektu (`app/src/main/AndroidManifest.xml`) i pełni kilka kluczowych funkcji:

- **Deklaruje komponenty aplikacji** – takie jak aktywności (`<activity>`), serwisy (`<service>`), odbiorniki (`<receiver>`) i dostawcy treści (`<provider>`).
- **Określa uprawnienia (permissions)** – czyli do jakich zasobów systemowych aplikacja chce mieć dostęp (np. Internet, aparat, lokalizacja).
- **Definiuje filtry intencji (intent filters)** – pozwalające na obsługę określonych akcji lub danych przez komponenty aplikacji.
- **Ustala podstawowe informacje o aplikacji** – takie jak nazwa, ikona, wersja, pakiet.
- **Deklaruje biblioteki i funkcje specjalne** – np. obsługę kamer, Bluetooth, NFC.

> **Uwaga:** Minimalna i docelowa wersja systemu (`minSdkVersion`, `targetSdkVersion`) są obecnie deklarowane w pliku `build.gradle`, a nie w AndroidManifest.xml.

**Przykładowy fragment AndroidManifest.xml:**
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp">

    <application
        android:label="Moja Aplikacja"
        android:icon="@mipmap/ic_launcher">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

    <!-- Uprawnienia -->
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

---

## System pozwoleń (permissions) w Androidzie

Aby aplikacja mogła korzystać z niektórych funkcji systemowych lub danych użytkownika (np. aparatu, lokalizacji, kontaktów), musi zadeklarować odpowiednie **pozwolenia** w pliku AndroidManifest.xml.

### Deklarowanie pozwoleń

Dodaj odpowiedni wpis `<uses-permission>` do pliku manifestu, np.:
```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

### Pozwolenia zwykłe i niebezpieczne

- **Pozwolenia zwykłe** (normal permissions) – są automatycznie przyznawane podczas instalacji (np. dostęp do Internetu).
- **Pozwolenia niebezpieczne** (dangerous permissions) – wymagają dodatkowej zgody użytkownika w trakcie działania aplikacji (np. lokalizacja, kontakty, SMS).

### Prośba o pozwolenie w czasie działania (runtime permissions)

Od Androida 6.0 (API 23) użytkownik musi wyrazić zgodę na niebezpieczne pozwolenia podczas korzystania z aplikacji.

**Aktualne podejście (Kotlin, Jetpack Activity Result API):**

Najlepiej korzystać z nowoczesnego API `ActivityResultContracts`, które jest prostsze i bezpieczniejsze niż stare `onRequestPermissionsResult`.

**Przykład:**
```kotlin
// W composable lub aktywności:
val launcher = rememberLauncherForActivityResult(
    contract = ActivityResultContracts.RequestPermission()
) { isGranted: Boolean ->
    if (isGranted) {
        // Pozwolenie przyznane
    } else {
        // Pozwolenie odrzucone
    }
}

// Wywołanie prośby o pozwolenie, np. po kliknięciu przycisku:
Button(onClick = {
    launcher.launch(Manifest.permission.CAMERA)
}) {
    Text("Poproś o dostęp do kamery")
}
```

**W przypadku wielu pozwoleń:**
```kotlin
val launcher = rememberLauncherForActivityResult(
    contract = ActivityResultContracts.RequestMultiplePermissions()
) { permissions ->
    val cameraGranted = permissions[Manifest.permission.CAMERA] ?: false
    val locationGranted = permissions[Manifest.permission.ACCESS_FINE_LOCATION] ?: false
    // Obsłuż odpowiedzi
}

launcher.launch(arrayOf(
    Manifest.permission.CAMERA,
    Manifest.permission.ACCESS_FINE_LOCATION
))
```


---

## Podsumowanie

- **AndroidManifest.xml** to centralny plik konfiguracyjny aplikacji – deklarujesz w nim komponenty, uprawnienia i inne kluczowe informacje.
- **Pozwolenia** chronią prywatność użytkownika – aplikacja musi poprosić o dostęp do wrażliwych danych i funkcji.
- Od Androida 6.0 część pozwoleń wymaga zgody użytkownika w trakcie działania aplikacji (runtime permissions).



## Więcej informacji:
- [Permissions overview – Android Developers](https://developer.android.com/guide/topics/permissions/overview)
- [Request app permissions – Android Developers](https://developer.android.com/training/permissions/requesting)
---
### 🧭 **Następny temat:** [Architetura aplikacji](https://github.com/MarcinRod/AndroidLecture2025/blob/main/09%20Architektura%20aplikacji.md)