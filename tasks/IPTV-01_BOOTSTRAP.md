# CODEX TASK IPTV-01 — VeylTV foundation for Android TV / Fire TV

## Goal
Initialize a buildable Android TV Kotlin project in `cisiur/VeylTV`. Deliver app launcher, first-run M3U URL setup, Settings, guide/player placeholders, repository interfaces and tests. **Do not implement real IPTV import or playback yet.**

## Baseline and guardrails
1. Inspect branch, HEAD, existing README/docs, git status and any application files. Do not overwrite documentation without necessity.
2. Read `AGENTS.md`, `docs/SPECYFIKACJA_TECHNICZNA.md`, `docs/ARCHITECTURE.md`, `docs/UX_ARCHIVE.md`, `docs/ADR/0001-architecture.md`.
3. Target Amazon Fire TV Stick 4K, API 30; minSdk proposal 28. App id `com.cisiur.veyltv`.
4. Choose **compatible stable** AGP, Kotlin, Compose for TV, Media3, Room, DataStore, WorkManager dependencies; record exact matrix and reasons in ADR. Include Gradle Wrapper, version catalog, `.gitignore`. Prefer simple DI without excessive framework boilerplate.
5. Don't commit real M3U/stream secrets, keystores, local.properties, generated SDK/build files.

## Implement
### A. App skeleton
- Buildable project with `:app`, Kotlin, Compose TV, TV Material focus styling and theme; TV launcher `LEANBACK_LAUNCHER`, no touchscreen requirement.
- `AndroidManifest.xml`: INTERNET and ACCESS_NETWORK_STATE, suitable TV activity/launcher, original resources (including adequate icon/banner), API 30-compatible runtime behavior.
- Screens: Onboarding/Add playlist, Guide placeholder, Settings, Player placeholder.
- D-pad navigation with visible focus and deterministic Back; screen sizing for 1080p/4K; Polish copy.
- Avoid phone-only navigation dependency or Google Play Services.

### B. First-run user flow
- When no source saved: show onboarding.
- Fields: playlist display name optional, HTTP(S) M3U URL, action **Zapisz źródło**. Android TV keyboard must work.
- Validate nonblank scheme http/https + nonempty host, reject malformed input, no fetch yet.
- Persist one M3U URL plus source name using DataStore, with EPG URL default `https://epg.ovh/plar.gz`. Add edit/delete with confirmation in Settings.
- Because URLs may hold credentials, avoid exposing full values in log/error UI. Document persistence trade-offs. On saving, go to Guide placeholder labeled **Import kanałów będzie dostępny w IPTV-02**.
- Player placeholder states playback is not available until IPTV-02; do not fabricate a successful stream.

### C. Boundaries/models
- Interfaces: `PlaylistRepository`, `ChannelRepository`, `EpgRepository`, `CatchupResolver`, `PlaybackController`.
- Domain models: `PlaylistSource`, `Channel`, `EpgProgramme`, `CatchupCapability`, `PlaybackRequest`, `PlaybackCapabilities`.
- For IPTV-01, catch-up resolver returns explicit Unsupported and player stub does not play anything.
- Room schema scaffolding for channel/programme/mapping, migrations policy; avoid storing favorites keyed by mutable row index.
- ViewModels/StateFlow, UI separate from data access; proper coroutine/lifecycle ownership.
- Media3 dependencies may be included but no ExoPlayer network activity yet.

### D. Tests
- URL validation: http/https success; empty URL, empty hostname, invalid scheme, malformed input rejected.
- ViewModels: first-run, save, edit, delete, guide placeholder state; tests with in-memory repositories.
- Catch-up unsupported stub result and URL redaction utility test if implemented.
- UI navigation/focus checks (automated when feasible); provide physical Fire TV testing steps in `docs/TESTING_FIRETV.md`.

### E. Documentation updates
- Update README build/test/ADB instructions for actual project.
- Record chosen versions/toolchain/DI/persistence in ADR.
- Make code match the existing roadmap; do not remove spec.

## Strict non-goals
No real M3U/EPG HTTP downloads; no live video player, catch-up URL construction, periodic workers, analytics, advertisements, external service accounts, server, DRM workarounds or proprietary branding.

## Definition of done
- `gradlew assembleDebug`, `testDebugUnitTest`, `lintDebug` pass **if environment has SDK**; otherwise report blockers without pretending builds passed.
- Launchable TV APK with visible D-pad focus; onboarding persists one M3U source and editable EPG URL through app restart.
- Source deletion returns onboarding, and placeholders clearly announce unimplemented behavior.
- Tests included; docs accurate; no user credentials or streams committed; no network downloads initiated by app.

## Final report format
- Baseline/current branch, HEAD and working tree status.
- Files added/changed and architectural rationale.
- Exact build/lint/test commands + result or explicit blocker.
- Physical Fire TV test status and follow-up checklist.
- Known security concerns, input focus issues, persistence trade-offs.
- Recommended IPTV-02 scope.
- Avoid pushing without explicit authorization from the current Codex user instruction.
