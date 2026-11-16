# VeylTV — specyfikacja techniczna (v1.1, 2026-11-16)

## 1. Product and target
Independent, TV-remote-controlled IPTV application for Android TV and Fire OS. Device baseline: Fire TV Stick 4K (Android 11 / API 30). Proposed minSdk 28, no mandatory Google Play Services. Kotlin, Compose for TV, Media3 ExoPlayer, Room, DataStore, OkHttp, WorkManager. Exact compatible stable versions determined and recorded in IPTV-01; avoid version pin assumptions before verifying.

The app provides no channels or subscriptions. User supplies a lawful M3U/M3U8 playlist URL; default EPG is `https://epg.ovh/plar.gz`, configurable. Polish interface; original branding and visuals. No server backend or paid API.

## 2. Product requirements
| ID | Feature | Milestone |
|---|---|---|
| FR-01 | Enter/edit/delete a single HTTP(S) playlist URL with validation | IPTV-01 |
| FR-02 | Import EXTM3U/EXTINF channels, groups, logos, TVG metadata | IPTV-02 |
| FR-03 | Fullscreen live playback, retry, channel switching, audio/subtitle tracks | IPTV-02 |
| FR-04 | Stream XMLTV from compressed EPG, map channels, show virtualized timeline | IPTV-03 |
| FR-05 | Look back at least 3 days in EPG when feed offers data | IPTV-03 |
| FR-06 | Provider-dependent catch-up, pause/seek where supported, return to Live | IPTV-04 |
| FR-07 | Playlist refresh ~24h, EPG refresh ~12h; cache-first with manual refresh | IPTV-05 |
| FR-08 | Favorites, groups, hidden channels, recent channels | IPTV-05 |
| FR-09 | Device performance, large data handling, packaging and diagnostics | IPTV-06 |

Non-goals for V1: server-side recording/PVR, multiview, cloud profiles, Xtream API authentication as a separate source type, proprietary/DRM bypass.

## 3. Functional behavior

### Playlist M3U
- HTTP(S) URL with redirects, timeout and failures; keep last valid import on error.
- BOM, LF/CRLF, extended `#EXTINF` with quoted/unquoted `tvg-id`, `tvg-name`, `tvg-logo`, `group-title`, `catchup`, `catchup-days`, `catchup-source`, `timeshift`.
- Support relevant `#EXTVLCOPT` / `#KODIPROP` header hints where securely applicable. Never expose authorization fields in logs.
- Distinguish playlist URL from stream URL; no query-string normalization that invalidates signed tokens.
- Stable internal channel identity must survive list reorder; preserve user favorites.
- Import in bounded-memory, background IO; partial invalid rows produce diagnostics, not full crash.

### Playback
- One lifecycle-managed Media3 player per active playback session, avoid duplicate decoders on constrained Fire TV.
- Formats best effort HLS, MPEG-TS progressive and DASH as supported by Media3/player device; no promise that all codecs/DRM work.
- Playback states Idle, Preparing, Buffering, Ready, Error, Released. Meaningful retry with limit/backoff.
- Fullscreen channel info and D-pad navigation; stream and codec failure messages without leaking source URL.
- Respect audio focus and Activity lifecycle. Release player resources.

### EPG XMLTV
- Default `https://epg.ovh/plar.gz`; verify real feed structure/retention at implementation, and handle source being down.
- Stream GZIP decompression and SAX/XmlPullParser (never load full XML DOM).
- Parse `channel id`, multilingual `display-name`, `programme start/stop/channel`, `title`, `desc`, `category`, icons; tolerate missing optional fields.
- XMLTV time with +0100/+0200/Z offsets must normalize to UTC Instant; UI uses local zone.
- Match first exact tvg-id, then uniquely normalized display name, then manual overrides. Ambiguous unmatched channels stay unmapped; do not guess.
- Query only visible channel rows/time windows, virtualize guide. Show current-time marker and day navigation into past.
- History metadata is not archive media: archive plays only if IPTV provider supports it.

### Catch-up and archive
- Contract: `CatchupResolver.resolve(channel, programme, now): CatchupResolution` where result is Playable(PlaybackRequest), Unsupported, NeedsProviderProfile, OutsideArchiveWindow, or InvalidProgramme.
- Recognize M3U attributes `catchup`, `catchup-days`, `catchup-source`, `timeshift`. Adapter families may include template/default, append, shift, Flussonic, Xtream-compatible URL shapes, but only after synthetic and provider-verified tests.
- Never invent provider-specific URL conventions. A known archive-window marker alone does not establish playability.
- Calculate start in UTC, duration from programme stop-start, timezone/correction per adapter; test DST transitions.
- UI archive overlay includes channel, programme title/date, badge "Archiwum", progress/time markers, pause/play, seeks (+/-10 or +/-30 seconds where seekable), and explicit "Na żywo" action.
- Remote: OK reveals controls; Left/Right seek when progress focused and supported, Up/Down navigates controls; Back hides overlay then returns to guide; Live returns same channel's live stream. Final exact focus mapping in `docs/UX_ARCHIVE.md`.
- Seek/pause capabilities must follow real Media3 timeline, not inferred solely from M3U metadata. Unsupported controls disabled with explanation.
- Archive retention defaults to three days only as UI policy; actual provider window wins.

### Refresh
- WorkManager periodic EPG ~12h and M3U ~24h (best effort, Android may defer).
- Freshness check at app resume; cache shown immediately; foreground manual refresh.
- Unique work, exponential retry and network constraints, atomic DB updates / last-good-data retention.
- Don’t write huge XML to persistent storage when streaming is sufficient; bound memory and DB size.

## 4. Data contracts (initial model)
- PlaylistSource: sourceId, displayName, sourceUrl [sensitive], lastAttempt, lastSuccess, syncStatus.
- Channel: channelId, sourceId, tvgId, name, normalizedName, logoUrl, group, streamUrl [sensitive], sortOrder, catchupMetadata.
- EpgChannel: xmltvId, displayNames.
- EpgProgramme: programmeId, xmltvChannelId, startUtc, stopUtc, title, description, category, icon.
- ChannelEpgMapping: channelId, xmltvId, method(EXACT_ID/NAME/MANUAL), confidence.
- PlaybackRequest: channelId, mode(LIVE/ARCHIVE), uri [sensitive], startUtc?, durationSeconds?, headers [sensitive].
- PlaybackCapabilities: canPause, canSeek, seekWindowStart/end.
- RefreshState: kind, lastAttempt, lastSuccess, itemCount, errorCode.

Room indexes: programme(xmltvChannelId,startUtc), channel(sourceId,tvgId); enforce migrations and transactional imports. Distinguish source records from user preferences to preserve favorites across imports.

## 5. Security / privacy
- Source URLs often contain username/token in URL. Treat as credentials in logs, crash reporting, screenshots, diagnostics and exports.
- No bundled provider stream URLs, no proprietary TiviMate logos/layout assets. Synthetic fixtures only.
- Use Android private storage; consider encrypted at-rest approach compatible with Fire OS and document trade-offs; never log raw sensitive URLs.
- No remote analytics, accounts, or backend by default. Do not weaken TLS verification.
- Minimal permissions: INTERNET, ACCESS_NETWORK_STATE and manifest TV launcher; no touchscreen requirement.

## 6. Acceptance across V1
- Tested at API 30 on real Fire TV Stick for boot, D-pad focus and playback.
- Unit tests for M3U syntax/error cases, XMLTV timezone/DST, provider adapter templates, redaction, refresh rollback.
- Room/HTTP integration tests and controlled fake streams where possible.
- Build, unit tests and Android lint in CI; use documented compatible stable dependencies.
- Graceful error UI and no user secrets in repository/logs.
- Each milestone documents out-of-scope features, exact validations and known blockers.

## 7. Primary references
- Android TV: https://developer.android.com/training/tv
- Compose for TV: https://developer.android.com/training/tv/playback/compose
- Media3: https://developer.android.com/media/media3/exoplayer
- EPG source: https://epg.ovh/
- Android WorkManager: https://developer.android.com/topic/libraries/architecture/workmanager
- Kodi IPTV Simple (format design reference; check license before reuse): https://github.com/kodi-pvr/pvr.iptvsimple
