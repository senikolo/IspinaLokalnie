# Ispina Lokalnie — Native

Projekt migracji aplikacji Ispina Lokalnie z hybrydowego interfejsu WebView/JS do natywnego Androida (Kotlin + Android SDK).

## Aktualny zakres
- natywny ekran startowy, odjazdy, planer, więcej, Senior i ustawienia;
- pogoda i WordPress z lokalnym cache offline;
- harmonogram odpadów i linia gminna z danych lokalnych;
- natywny cache GTFS z bezpieczną podmianą pliku po udanym pobraniu;
- filtrowanie GTFS według `calendar.txt` i `calendar_dates.txt`;
- wyszukiwanie połączeń bezpośrednich i z jedną przesiadką;
- natywna lista odjazdów MLD dla wybranego przystanku;
- natywny parser GTFS-RT JSON i przypinanie opóźnienia do `trip_id`;
- radio PR1 przez natywny `MediaPlayer` oraz zatrzymywanie/zwalnianie odtwarzacza;
- kamera ZDW z odświeżaniem bez dokładania kolejnych obrazów do ekranu;
- automatyczna synchronizacja po uruchomieniu i po starcie urządzenia.

## Świadomie niedokończone
- pełna zgodność wizualna 1:1 z rozbudowanym CSS oryginału;
- pełny natywny odtwarzacz TVP3 z mechanizmem wyboru wariantu jakości;
- pełny zestaw alertów/powiadomień i ulubionych z oryginału;
- końcowa kompilacja APK w tym środowisku.

## Budowanie
Do rzeczywistego builda potrzebne jest Android SDK + Gradle/Android Gradle Plugin. W środowisku roboczym nie ma obecnie `gradle`, `sdkmanager` ani `android.jar`, więc nie deklaruję niezweryfikowanego APK.

### v1.86.0-native
Dodano opcjonalne natywne powiadomienia o najbliższym odjeździe z wybranego przystanku. Powiadomienie jest generowane tylko dla kursu mieszczącego się w oknie 1–15 minut.


### v1.88.2-native — stabilizacja lifecycle i planera
- poprawiono błędy kompilacji w `GtfsRepository` i `TvpRepository`;
- aktualizacja GTFS jest serializowana i waliduje ZIP przed podmianą cache;
- błędy HTTP są traktowane jako nieudane pobranie zamiast jako poprawna odpowiedź;
- harmonogram odpadów nie jest już zakodowany wyłącznie dla 2026;
- planer korzysta również z lokalnego `linia-gminna.json`, z uwzględnieniem powtarzających się przystanków;
- poprawiono reset opóźnienia w parserze GTFS-RT;
- cofanie z ekranów podrzędnych wraca na Start;
- uprawnienie powiadomień jest żądane dopiero po włączeniu tej funkcji w Ustawieniach;
- kamera, pogoda/news i TVP mają jawniejszą obsługę błędów HTTP.

Build produkcyjny nadal wymaga Android SDK/AGP/Gradle oraz konfiguracji podpisywania poza tym środowiskiem.
