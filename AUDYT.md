# Audyt migracji — aktualizacja

Źródło wejściowe: APK `ispina_poprawiona_1-aligned-debugsigned.apk`, wersja JS widoczna w `main_ui.js`: 1.82.17.

## Ustalenia
- Oryginał korzysta z rozbudowanego UI HTML/CSS/JS oraz mostu `window.IspinaNative`.
- Nowy projekt nie zawiera WebView i nie korzysta z tego mostu.
- Źródła danych zidentyfikowane w oryginale obejmują Open-Meteo, WordPress REST, GTFS, GTFS-RT, dane gminne, kamerę ZDW, PR1 i TVP3.

## Wykonane w tym etapie
1. GTFS: filtrowanie aktywnych usług przez `calendar.txt` + `calendar_dates.txt`.
2. GTFS: wyszukiwanie po nazwach przystanków z normalizacją polskich znaków.
3. GTFS: odjazdy z wybranego przystanku.
4. Planer: bezpośrednie połączenia i jedna przesiadka.
5. GTFS-RT JSON: parser `trip_update`, `route_id`, `trip_id`, `stop_time_update`, `delay`, `time`.
6. Odjazdy MLD: powiązanie planowanego kursu z opóźnieniem po `trip_id`.
7. Kamera: odświeżenie zastępuje poprzedni obraz zamiast go dokładać.
8. PR1: natywny `MediaPlayer` z bezpiecznym `stop/release`.
9. Pierwsza synchronizacja GTFS jest uruchamiana, gdy cache nie istnieje.

## Nadal do wykonania
- TVP3: natywne pobieranie konfiguracji/manifestu i odtwarzanie HLS z fallbackami jakości.
- pełna implementacja danych MLD na wszystkich ekranach i kierunkach;
- powiadomienia, ulubione, ustawienia i część diagnostyki;
- pełna migracja wyglądu Senior i planera;
- testy na emulatorze/telefonie;
- podpisany APK po uzyskaniu narzędzi build.

## Ograniczenie środowiska
Nie ma dostępnego Android SDK/Gradle. Nie wykonano fikcyjnego „builda”.

- v1.85: parser GTFS ma pamięć podręczną z unieważnianiem po aktualizacji; dodano aliasy Ispina I-IV i autouzupełnianie przystanków planera.

## ETAP 12 — powiadomienia natywne
- Dodano opcjonalne powiadomienie o najbliższym odjeździe z ulubionego przystanku.
- Harmonogram synchronizacji wywołuje kontrolę powiadomień po aktualizacji danych.
- Uprawnienie POST_NOTIFICATIONS jest żądane na Androidzie 13+.
- Ustawienie jest przechowywane lokalnie.


## ETAP 13 — audyt v9 → v1.88.1-native

### Istotna rozbieżność artefaktów
Dostarczony APK `ispina_poprawiona_1-aligned-debugsigned.apk` zawiera `assets/main_ui.js`, klasy `WebView/WebViewClient` oraz most `IspinaNative`; zasób JS raportuje wersję 1.82.17. Nie jest to więc binarny odpowiednik bieżącego projektu native 1.88.0. APK potraktowano jako referencję zachowania, nie jako wynik bieżącego buildu źródeł.

### Znalezione problemy
- brak importu `java.util.Locale` w `GtfsRepository`;
- `TvpRepository.findInJson()` używało niedozwolonych `return` wewnątrz expression body;
- `TvpRepository` miał również problem ze smart-castem zmiennej mutowanej w `runCatching`;
- aktualizacje GTFS mogły równolegle używać jednego pliku tymczasowego;
- wynik HTTP nie był walidowany przed zapisaniem;
- cache GTFS mógł zostać podmieniony bez sprawdzenia wymaganych plików feedu;
- harmonogram odpadów odwoływał się wyłącznie do roku 2026;
- parser GTFS-RT nie zerował opóźnienia, gdy kolejne zdarzenie jawnie podawało `delay=0`;
- planer wykorzystywał tylko GTFS, mimo obecności lokalnego `linia-gminna.json`;
- lokalny planer gminny musi uwzględniać wszystkie wystąpienia powtarzających się przystanków;
- aplikacja automatycznie prosiła o POST_NOTIFICATIONS już przy starcie, niezależnie od włączenia funkcji;
- przycisk systemowego Back nie miał nawigacji między ekranami aplikacji.

### Wykonane poprawki
- wersja projektu: 1.88.1-native / versionCode 18801;
- serializacja aktualizacji GTFS + walidacja ZIP (`stops.txt`, `trips.txt`, `stop_times.txt`);
- poprawiona obsługa HTTP i disconnect dla pobrań;
- dynamiczne wyszukiwanie najbliższego terminu odbioru z dostępnych lat;
- dodane wyszukiwanie połączeń z `linia-gminna.json`;
- poprawione GTFS-RT delay reset;
- poprawione zachowanie Back;
- żądanie powiadomień przeniesione do ustawienia funkcji;
- poprawiona obsługa TVP JSON i HTTP.

### Walidacja wykonana w tym środowisku
- `GtfsRepository.kt` skompilowany osobno z minimalnymi stubami Androida — sukces, tylko ostrzeżenie o deprecacji `java.net.URL`;
- `ScheduleLive.kt` skompilowany osobno z minimalnym stubem `org.json` — sukces;
- `NativeCore.kt` skompilowany osobno z minimalnymi stubami Androida/JSON — sukces;
- `TvpRepository.kt` skompilowany osobno z minimalnymi stubami Androida/JSON — sukces;
- zweryfikowano strukturę i spójność `linia-gminna.json` oraz `waste-schedule.json`;
- wykonano statyczny skan sekretów i uprawnień — brak znalezionych kluczy/sekretów; manifest zawiera tylko uprawnienia wymagane przez aktualne funkcje.

### Nadal nieweryfikowalne tutaj
Brak Android SDK, Gradle/Gradle Wrapper, emulatora i narzędzi podpisywania uniemożliwia wykonanie pełnego buildu APK, instalacji i testu end-to-end na urządzeniu. Dostarczony APK jest binarnie starszą wersją hybrydową, więc nie może służyć jako dowód działania zmian 1.88.1-native.


## ETAP 14 — dodatkowa stabilizacja
- odjazdy GTFS: wyszukiwanie bieżącego dnia + kolejnych 7 dni; poprawione kursy po północy;
- powiadomienia: poprawne wyliczanie czasu dla kursu następnego dnia;
- lifecycle MainActivity: callbacki asynchroniczne nie aktualizują już UI po zniszczeniu Activity;
- MediaPlayer: ochrona przed spóźnionym callbackiem `onPrepared`/`onCompletion`/`onError` po zatrzymaniu lub zmianie strumienia;
- SyncReceiver: użycie `applicationContext` i gwarantowane `finish()` dla `goAsync()`.

Pełny build Android nadal wymaga środowiska z Android SDK i Gradle.
