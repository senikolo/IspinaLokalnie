# Postęp — v1.88.2-native

## Zakres tej iteracji

- ograniczenie tworzenia ad-hoc `Thread` w `MainActivity` do kontrolowanego `ExecutorService` powiązanego z lifecycle Activity;
- anulowanie prac UI przez generację wyniku dla listy odjazdów i planera, aby starsza odpowiedź sieciowa/obliczeniowa nie nadpisywała nowszego widoku;
- bezpieczne zamykanie executorów przy `onDestroy`;
- komunikat błędu zamiast nieskończonego `Ładowanie…` dla wiadomości na ekranie Start;
- bezpieczne dekodowanie obrazu kamery;
- planer GTFS przeszukuje również kolejne dni i informuje, gdy połączenie jest jutro/później;
- lokalna linia gminna również przeszukuje następne dni, w tym przejście przez niedzielę do kolejnego dnia roboczego;
- aktualizacja GTFS ma kopię poprzedniego cache i możliwość jego odtworzenia, jeśli podmiana pliku się nie powiedzie;
- TVP nie przechowuje referencji do Activity w tle — używany jest `applicationContext`;
- podniesiono `versionCode` do 18802 i `versionName` do `1.88.2-native`.

## Walidacja

- wszystkie pliki Kotlin przechodzą heurystyczną kontrolę nawiasów;
- `linia-gminna.json` i `waste-schedule.json` są poprawnymi JSON;
- skan źródeł nie wykazał kluczy API, haseł, tokenów ani prywatnych kluczy;
- nie znaleziono TODO/FIXME/placeholderów w źródłach;
- `kotlinc` analizował kod parsera bez błędów składniowych; pozostałe komunikaty wynikają z braku klas Android/`org.json` poza pełnym środowiskiem Android;
- pełny Gradle/Android build, instalacja i test urządzeniowy nadal wymagają Android SDK/build-tools.
