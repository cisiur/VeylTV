# Fire TV Stick 4K smoke testing

Reference Fire TV device: Fire OS based on Android 11 / API 30. No touch input, no Google Play Services dependency.

## Tooling
```powershell
adb devices
# After IPTV-01 creates the project:
.\gradlew.bat testDebugUnitTest
.\gradlew.bat lintDebug
.\gradlew.bat assembleDebug
adb install -r .\app\build\outputs\apk\debug\app-debug.apk
adb shell monkey -p com.cisiur.veyltv 1
```

Make sure the package identifier matches the actual Gradle `applicationId` after Codex bootstrap. For wireless ADB, first connect with `adb connect <FIRE_TV_IP>:5555` if authorized on the device. Never store IP/credentials in repo.

## IPTV-01 manual checklist
- App visible in TV launcher and starts without requiring touch/mouse.
- Fresh install shows onboarding with focus on URL field / action.
- Remote keyboard input for a syntactically valid test URL works; no actual download at this stage.
- Malformed URL rejected; no crash or leak of entered token in error or logcat.
- Save returns Guide placeholder stating import comes in IPTV-02.
- Navigate Guide → Settings → edit EPG/M3U → Back; observe focus and persistence.
- Force-stop/reopen, verify saved settings persist.
- Delete source (confirmation) restores onboarding.
- Player placeholder does not claim to stream video.
- Test at 1080p and 4K scaling where possible, with TV overscan margins.

## Later milestones
- M3U import: valid fixtures, groups/order/logo, real network failure fallback.
- EPG: gzip parse, channel map, past day selection, DST.
- Catch-up: archive works only if provider supports it; pause/seek disabled if unsupported; go-live on same channel.
- Refresh: Wi-Fi outage/recovery, background policy and cache retention.

## Reporting
State device model, OS/API, APK build hash, ADB commands, pass/fail evidence and logcat excerpts with URL/token redaction.
