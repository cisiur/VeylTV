# Archive player UX (design for IPTV-04)

## Layout (16:9, landscape TV)
- Fullscreen video behind a bottom translucent panel. Panel must preserve readable contrast and not permanently cover subtitles.
- Top-left within overlay: channel logo/name, programme title and broadcast date in local time.
- A distinct **ARCHIWUM** status label; no misleading LIVE status.
- Progress bar for selected programme with elapsed/remaining time and broadcast start/end; live edge for rolling archive if provider supports.
- Central controls: Play/Pause; Seek -10s / +10s (optional -30/+30 configurable); **Na żywo** action distinct and always reachable; optional EPG action.
- During seeking show preview time in timeline; only commit seek when capability allows.
- Unavailable archive: explain whether outside 3-day window, source missing, or provider unsupported.

## D-pad behavior
- OK shows/hides overlay when focus is not on a button; OK activates focused button.
- Left/Right: navigate focused controls; while seekbar focused, step through time; show value before seek.
- Up/Down: move between seekbar and buttons or EPG contextual panel.
- Back with overlay: hides it. Back without overlay: return to Guide preserving channel/time focus.
- Live button: clear archive mode, open the same channel's live stream, reset timeline state.
- Long-press / media keys are enhancements, not requirements. No gesture-only interaction.

## State machine
Idle → LoadingArchive → PlayingArchive ↔ PausedArchive ↔ SeekingArchive → ErrorArchive; transition to LoadingLive → PlayingLive. Show buffering separate from pause. Player capability flags come from Media3 timeline/session, not guessed M3U metadata.

## Accessibility
Minimum comfortable focus targets (48dp+), large readable text at 1080p/4K, distinct selected and focused states, remote-only test, safe margins (overscan), keyboard focus restoration after Guide.

## Empty/partial states
No EPG match: cannot choose program from guide. Programme in past but unavailable: disabled or explanation. Seek not supported: disabled controls, no simulated success. Live stream failure after transition: clear error with retry.
