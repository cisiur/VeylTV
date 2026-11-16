# VeylTV roadmap

Milestones are deliberately isolated so each creates a testable artifact.

| Stage | Main deliverable | Acceptance signal |
|---|---|---|
| IPTV-01 | Kotlin Android TV bootstrap, D-pad-first onboarding/settings, persisted source URL, Room skeleton, placeholder guide/player | Build, unit tests, remote navigation and settings persist |
| IPTV-02 | HTTP M3U importer, groups/channels, live Media3 playback | A valid legal playlist displays channels; live tune and channel switch work |
| IPTV-03 | GZIP/XMLTV import from epg.ovh, channel matching, virtualized timeline | Shows now/next, descriptions, historic schedule with timezone correctness |
| IPTV-04 | Catch-up adapter contract and tested provider templates, archive player with play/pause/seek/live | Eligible programme starts archive, returns live; unsupported states explicit |
| IPTV-05 | Background refresh, favorites, hidden groups, last-watched, manual refresh | Updates don't lose preferences or last-good cache |
| IPTV-06 | Fire TV optimization, malformed providers, robust UI, packaging / release documentation | Real Fire TV smoke + performance and integration regression |

Catch-up remains the greatest uncertainty: a generic M3U URL is insufficient to guarantee archive replay. Inspect redacted sample metadata during IPTV-04. Exact target dependency versions verified during IPTV-01, not guessed in advance.

## Definition of done for every stage
- Code + tests + clear docs and change report.
- No secrets / proprietary streams in repo.
- Build/lint/test results reported transparently.
- Stage boundaries maintained; future features disabled or marked as placeholders, never misleadingly "working".
