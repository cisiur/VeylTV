# Catch-up formats and provider verification

Catch-up is **not** derived from EPG alone. XMLTV supplies historical programme start and stop times; archives are served by the IPTV provider and may be unavailable or unseekable.

## M3U evidence to parse
- `catchup` (`default`, `append`, `shift`, `flussonic`, `xc`, and unknown/custom)
- `catchup-days` (advertised archive length, not proof of operation)
- `catchup-source` (provider URL template when supplied)
- `timeshift` / `tvg-shift` and provider-specific fields
- `#EXTVLCOPT` and other metadata for required HTTP headers, without logging values

## Adapter result
`CatchupResolution = Playable(PlaybackRequest) | Unsupported | NeedsProviderProfile | OutsideArchiveWindow | InvalidProgramme`

`PlaybackRequest` carries URI, optional headers, UTC start, duration and seek policy. Never manufacture a URL from a guessed provider pattern.

## Candidate adapter families (design, NOT validated implementations)
| Family | General idea | Caution |
|---|---|---|
| default/template | Substitute programme timestamps in supplied `catchup-source` | Syntax varies by provider |
| append | Append time/duration query elements | Separator and timestamp semantics vary |
| shift | Provider-specific time shift parameters | Not universal |
| flussonic | Server archive URL path/time convention | Confirm against actual allowed M3U metadata |
| xc | Xtream-compatible stream/archive URL conventions | M3U compatibility does not imply Xtream API support |

## Validation plan for IPTV-04
1. Obtain a synthetic or redacted single-channel `#EXTINF` sample retaining field names and safe template shape (mask account/hostname/token).
2. Confirm archive support and window through legitimate provider metadata.
3. Unit test UTC/local DST boundaries and duration calculations.
4. Test live and historical requests against user-authorized service, without adding secret URLs to repository.
5. Verify seek and pause from actual Media3 timeline. Disable unsupported controls and report reason.

Never infer archive support from `catchup-days` alone. Never bypass DRM, login, geo restrictions, or paywalls.
