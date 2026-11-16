# VeylTV

Open-source-oriented, independent IPTV player project for Android TV and Amazon Fire TV. **Documentation-first repository; the Android application is not implemented yet.**

## Scope

- Import IPTV playlists supplied by the user through M3U/M3U8 URL (no embedded content or playlists).
- Play live streams using AndroidX Media3 (when implemented in IPTV-02).
- Import XMLTV EPG, default source: `https://epg.ovh/plar.gz` (IPTV-03).
- Support provider-dependent catch-up archives (IPTV-04), with pause/seek when stream capabilities allow it.
- Automatically refresh playlist and EPG in later milestones (IPTV-05).
- Fully remote-controlled UX for Fire TV Stick 4K; Polish UI first.

## Start here

1. Read [Product and technical specification](docs/SPECYFIKACJA_TECHNICZNA.md).
2. Read [architecture](docs/ARCHITECTURE.md), [roadmap](docs/ROADMAP.md) and [catch-up formats](docs/CATCHUP_FORMATS.md).
3. For Codex, follow [AGENTS.md](AGENTS.md) and the active [IPTV-01 task](tasks/IPTV-01_BOOTSTRAP.md).
4. Device test plan: [TESTING_FIRETV.md](docs/TESTING_FIRETV.md).

## Current state

This repository starts with documentation and guardrails only. Codex will create the Gradle/Kotlin project in IPTV-01. There are no buildable sources yet; build and ADB commands become applicable after that milestone.

## Development environment

Windows 11 / PowerShell, Git, Android Studio or compatible JDK + Android SDK, ADB, Amazon Fire TV Stick 4K (Fire OS Android 11 / API 30 target device). Build instructions and exact version matrix will be recorded by Codex after selecting compatible stable AGP/Kotlin/Compose/Media3 libraries.

Clone:

```powershell
git clone https://github.com/cisiur/VeylTV.git
cd VeylTV
```

## Privacy and legal

No provider credentials or stream URLs belong in commits, issues or logs. No DRM circumvention, playlists bundled with the app, or third-party TiviMate branding/assets. Use only IPTV content you are authorized to access. Screens should remain clear about unsupported features until implemented.

## Status

- Documentation: bootstrap specification ready.
- IPTV-01: pending.
- IPTV-02 to IPTV-06: planned.
