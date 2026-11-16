# ADR 0001 — Android TV-first Kotlin foundation

Status: **Proposed**. Confirm concrete versions during IPTV-01.

## Context
VeylTV targets a constrained Fire TV Stick 4K with hardware remote and Android API 30. It needs live playback, large XMLTV data sets and provider-dependent archive semantics. Development takes place on Windows and via Codex.

## Decision
- Kotlin Android app, first a single `:app` Gradle module with domain/data/player/ui package boundaries.
- Compose for TV, D-pad-first interaction; no phone-only UI assumptions.
- Media3 ExoPlayer as playback implementation; UI depends only on playback interface.
- Room for normalized channels/programmes/mappings and DataStore for simple app preferences.
- OkHttp and streaming parsers for M3U/XMLTV GZIP; WorkManager for best-effort periodic refresh.
- ViewModels expose StateFlow; use coroutines and repository interfaces.
- No backend, paid services, Google Play Services dependency, or bundled channel content.
- Package/application ID: `com.cisiur.veyltv` (subject to build checks).
- minSdk proposal: 28; test target API 30. Choose stable compatible compileSdk/AGP/Kotlin/KSP/Compose/Media3/Room versions based on current published compatibility, record verified matrix in this ADR.
- IPTV-01 only creates stubs for external integrations; later tasks own network/playing.

## Consequences
Better separation and testability, at cost of more initial interfaces. Catch-up cannot be asserted functional from M3U schema alone. Fire OS hardware testing remains mandatory after desktop build.

## Open items for IPTV-01
Record chosen exact versions and toolchain, DI mechanism, at-rest source URL protection strategy, player ownership, and actual commands executed.
