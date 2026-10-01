# Build / Verification Status — Ispina Lokalnie 1.88.2-native

## Verified in this environment
- After the additional stability pass, `GtfsRepository.kt` and `NativeCore.kt` + `ScheduleLive.kt` compile with the available JVM/Android/JSON stubs; only stub/deprecation warnings remain.
- Source tree inspected end-to-end.
- Existing APK inspected; it is a hybrid/WebView artifact with `assets/main_ui.js` 1.82.17 and is not the binary output of this native source tree.
- `GtfsRepository.kt` compiled standalone with Android Context stubs.
- `NativeCore.kt` compiled standalone with Android/network/JSON stubs.
- `ScheduleLive.kt` compiled standalone with JSON stubs.
- `TvpRepository.kt` compiled standalone with Android/JSON stubs.
- Static scan: no TODO/FIXME markers, no empty catch blocks, no obvious API-key/password/private-key literals.
- Local JSON assets validated structurally; all gminna route/offset arrays have matching lengths.

## Not verified
A full Android build, APK install, instrumentation tests, emulator/device tests, and release signing could not be executed because this environment does not contain Android SDK/Gradle/build-tools/signing credentials.

## Additional stability pass
- GTFS departures now search the current day and following 7 days, so late-evening use can find the next-day service.
- Departure notifications account for a next-day departure instead of calculating a negative interval.
- MainActivity UI callbacks are ignored after Activity destruction to reduce stale lifecycle updates.
- MediaPlayer callbacks are guarded against late callbacks after `stop()`/replacement.
- SyncReceiver now uses the application context and always calls `finish()` in `finally`.

## Release gate before production
1. Build with the project's AGP 8.7.3 / Kotlin 2.0.21 / compileSdk 35 toolchain.
2. Install on at least Android 8/11/13/14/15 test devices/emulators.
3. Exercise cold start, offline start, GTFS refresh, MLD live refresh, planner, gminna planner, notifications, camera, PR1, TVP3 fallback, rotation/recreation where applicable, and reboot scheduling.
4. Run `lint` and unit/instrumentation tests.
5. Configure release signing outside the repository and produce a release APK/AAB.


## Iteracja 1.88.2
- ExecutorService w MainActivity zamiast ad-hoc Thread dla operacji GTFS/planera.
- Ochrona przed starymi wynikami po zmianie przystanku/zapytania.
- Planer GTFS i linia gminna szukają kolejnych dni.
- Aktualizacja GTFS ma backup/rollback cache.
- Bezpieczne dekodowanie obrazu kamery.
- TVP używa applicationContext w pracy w tle.
