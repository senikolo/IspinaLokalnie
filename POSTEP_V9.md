# Ispina Lokalnie Native — v9

## Zmiany względem v8
- Przywrócono synchronizację po powrocie aplikacji na pierwszy plan, zgodnie z zachowaniem opisanym w raporcie dla starej aplikacji.
- Synchronizacja foreground jest ograniczona do maksymalnie jednej próby na 15 minut, więc wielokrotne wejście/wyjście z aplikacji nie generuje niepotrzebnych pobrań.
- Po udanej synchronizacji foreground sprawdzane są również powiadomienia o najbliższym odjeździe.
- Wersja aplikacji: 1.88.0-native (versionCode 18800).

## Stan builda
Projekt jest skonfigurowany pod Android Gradle Plugin 8.7.3, Kotlin 2.0.21, compileSdk 35 i Java 17.
W bieżącym środowisku nie ma jednak Android SDK/Gradle, więc APK nie zostało jeszcze skompilowane ani przetestowane na urządzeniu.
