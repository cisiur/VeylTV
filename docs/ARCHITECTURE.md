# VeylTV — architecture

## Approach
Begin with a single Gradle application module `:app`, structured in clean boundaries, later extract modules if profiling demands. Kotlin coroutines/Flow, MVVM + repository, explicit DI or Hilt (log decision in ADR). Never couple Composable UI with M3U/XMLTV parsing or URL construction.

```text
ui/onboarding, ui/guide, ui/player, ui/settings
          ↓ ViewModels / StateFlow
domain/usecase + domain/model
          ↓ interfaces
data/m3u + data/epg + data/catchup + data/db + data/preferences
          ↓
HTTP clients / parsers / Room / DataStore

playback/Media3Controller ↔ domain playback contracts
sync/WorkManager → domain refresh use-cases
```

## Packages (planned)
`app` (Application + navigation), `domain/model`, `domain/repository`, `domain/usecase`, `data/m3u`, `data/epg`, `data/catchup`, `data/db`, `data/preferences`, `playback`, `sync`, `ui/theme`, `ui/onboarding`, `ui/guide`, `ui/player`, `ui/settings`.

## Invariants
1. Channel and program identities are stable independent of visual positions.
2. Store all schedule times in UTC; convert only in UI.
3. Provider secret URL and headers are never emitted in plain logs or errors.
4. Sync commits complete, valid replacements; failure preserves last-good dataset.
5. Catch-up adapter either produces a validated request or an explicit unsupported result.
6. Player states reflect actual Media3 callbacks. Playback success cannot be simulated.
7. UI remains navigable with a D-pad and Back on physical Fire TV hardware.

## Source flows
- M3U source URL → conditional HTTP fetch → streaming M3U parse → validation → Room transaction → channels Flow → Guide.
- EPG URL → fetch/GZIP → streaming XMLTV parse → EPG tables → exact/mapped channels → Guide time window.
- Program selection → catch-up resolver & provider capability → PlaybackRequest → Media3 → playback state/timeline → Archive overlay.
- Live selection → Channel.streamUrl → Media3 → fullscreen player.

## Lifecycles
One owner of player, aligned with screen/session lifecycle; explicitly stop/release on exit. Background refresh on WorkManager independent of UI, with expiration check on resume. Cache-first startup.

## Decisions requiring ADR
Library version matrix, DI choice, Room schema and migrations, source URL at-rest protection, player lifecycle policy, TV field focus/keyboard behavior. ADR 0001 establishes initial baseline.
