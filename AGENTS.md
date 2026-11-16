# Instructions for Codex — VeylTV

This file applies to the whole repository.

## Mission
Build an original Polish-first IPTV app for Android TV / Fire OS. Target first device: Amazon Fire TV Stick 4K, Android API 30. Use Kotlin, Compose for TV, Media3, Room, DataStore, WorkManager in milestones defined in `docs/ROADMAP.md`.

## Source of truth
Read `docs/SPECYFIKACJA_TECHNICZNA.md`, `docs/ARCHITECTURE.md`, `docs/CATCHUP_FORMATS.md`, the active task in `tasks/`, and relevant ADRs before writing code. When conflicts occur, the active task defines milestone scope; document decisions in ADRs.

## Change discipline
- Inspect branch, HEAD, working tree, and existing files before modifications.
- Make only changes in the current task. No speculative implementation of later IPTV stages.
- Never overwrite user changes silently; don't force-push or rewrite history.
- Create unit tests for logic and run available build/lint/test tasks; report exact results and environment blockers.
- Do not claim UI or stream behavior tested on hardware unless an ADB test was actually performed.
- Include final git state, changed files, tests, limitations, and the next proposed task in the summary.
- Unless explicitly requested in the active Codex run, do not commit/push automatically; this instruction is subordinate to a direct user request to do so.

## Security and compliance
- No provider URLs containing tokens, credentials, private playlists, authorization headers, or real stream data in fixtures, commits, screenshots, telemetry, or logs.
- Use redacted synthetic test data. Default public EPG URL is allowed.
- Don't embed, distribute or scrape premium streams; no bypass of paywalls, authentication or DRM.
- Respect licenses and brand independence: no copied TiviMate code/assets.
- Prefer HTTPS, support HTTP only with explicit provider compatibility consideration; never disable TLS verification globally.
- Treat M3U URL as potentially secret; design secure persistence and UI masking rather than printing its full value.

## UX and architecture
- D-pad-first focus behavior; back navigation must be deterministic, visible focus ring always.
- Avoid Google Play Services dependency and touchscreen-only flow.
- Keep network, parsing, persistence, playback and UI separated. Prefer interfaces and testable pure logic.
- UTC for persisted programme times; local timezone only for display.
- Catch-up is explicitly unsupported until validated by provider metadata and actual playback capability; never fabricate working URLs.
