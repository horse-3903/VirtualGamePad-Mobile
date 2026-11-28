<div align="center">

# AirDeck-Mobile: developer guide

Build, architecture and settings notes for the Android app.

![Kotlin](https://img.shields.io/badge/Kotlin-2.3-7F52FF?style=flat-square&logo=kotlin&logoColor=white)
![Jetpack Compose](https://img.shields.io/badge/Jetpack%20Compose-Material%203-4285F4?style=flat-square&logo=jetpackcompose&logoColor=white)
![Gradle](https://img.shields.io/badge/Gradle-KTS-02303A?style=flat-square&logo=gradle&logoColor=white)

[Back to README](../README.md) | [PC server](https://github.com/horse-3903/AirDeck-PC)

</div>

---

## Tech Stack

![Kotlin](https://img.shields.io/badge/Kotlin-7F52FF?style=for-the-badge&logo=kotlin&logoColor=white)
![Jetpack Compose](https://img.shields.io/badge/Jetpack%20Compose-4285F4?style=for-the-badge&logo=jetpackcompose&logoColor=white)
![Material 3](https://img.shields.io/badge/Material%203-757575?style=for-the-badge&logo=materialdesign&logoColor=white)
![Android](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=white)
![Gradle](https://img.shields.io/badge/Gradle-02303A?style=for-the-badge&logo=gradle&logoColor=white)

The app also uses DataStore for settings, CameraX with ZXing for QR scanning, and kotlinx.serialization for layout files. The wire format is Colfer, from the `VGP_Data_Exchange` submodule.

---

## Getting Started

### Prerequisites

- Android Studio, or JDK 17 or newer
- Android SDK platform 36 (the app targets 34 and needs Android 8.0+)
- Git with submodule support
- A running [PC server](https://github.com/horse-3903/AirDeck-PC) on the same network

### Installation

```bash
git clone --recurse-submodules https://github.com/horse-3903/AirDeck-Mobile.git
cd AirDeck-Mobile
./gradlew assembleDebug
```

On Windows, use `.\gradlew.bat assembleDebug` and set `JAVA_HOME` to a JDK first. Android Studio's bundled runtime works: `C:\Program Files\Android\Android Studio\jbr`. The APK lands in `app/build/outputs/apk/debug/app-debug.apk`.

### Run locally

```bash
adb install -r app/build/outputs/apk/debug/app-debug.apk
adb shell monkey -p io.github.horse3903.airdeck -c android.intent.category.LAUNCHER 1
```

The Android emulator reaches the host PC at `10.0.2.2`, so enter `10.0.2.2:<port>`. Force landscape with `adb shell settings put system user_rotation 1`.

---

## Settings

Pages are Appearance, Layouts, Controls (Sticks, D-pad, Triggers, Haptics), Display and Connection. Every Controls setting can be overridden per layout.

| Setting | Default | Range |
|---|---|---|
| Colour scheme | System | System, light, dark |
| Gamepad style | PlayStation | Xbox, PlayStation, Joy-Con |
| Control opacity | 100% | 20 to 100% |
| Edge glow, strength | On, 100% | On or off, 10 to 200% |
| Full screen, keep screen awake | On | On or off, each |
| Snap stick to touch | On | On or off |
| Stick dead zone | 5% | 0 to 40% |
| Stick sensitivity | 100% | 50 to 150% |
| Hold to click, hold time | On, 450 ms | On or off, 200 to 1500 ms |
| Invert stick vertical | Off | On or off, per stick |
| One direction at a time | Off | On or off |
| Diagonal zone | 38% | 20 to 60% |
| Haptic feedback | On | On or off |
| Vibration strength, length | 70%, 20 ms | 5 to 100%, 5 to 80 ms |
| Update interval | 80 ms | 20 to 200 ms |
| Reconnect automatically, keep trying for | On, 120 s | On or off, 10 to 600 s |
| Confirm before disconnecting | On | On or off |

---

## Project Structure

```text
AirDeck-Mobile/
├── app/src/main/java/io/github/horse3903/airdeck/
│   ├── data/                 # settings, defaults, curves, suggested layouts, layout import
│   ├── network/              # connection view model, foreground service, diagnostics
│   └── ui/
│       ├── composables/      # sticks, D-pad, buttons, triggers, badge, dialogs
│       ├── screens/          # main menu, connect, gamepad, settings, layout editor
│       ├── components/       # QR scanner
│       ├── theme/            # colours, shapes, typography
│       └── utils/            # haptics, orientation lock
├── app/src/main/res/         # strings, icons, drawables
├── VGP_Data_Exchange/        # Colfer wire protocol (submodule)
├── fastlane/metadata/        # store listing text
├── gradle/libs.versions.toml # dependency versions
└── signing.properties.template
```

---

## Deployment

Releases are GitHub pre-releases with a debug-signed APK and a `SHA256SUMS.txt`. A signed release build needs a keystore.

```bash
cp signing.properties.template signing.properties
./gradlew assembleRelease
```

```bash
gh release create v0.6.0 AirDeck-Mobile-0.6.0-debug.apk SHA256SUMS.txt \
  --repo horse-3903/AirDeck-Mobile --prerelease --notes-file notes.md
```

---

## Environment Variables

| Variable | Default | Description |
|---|---|---|
| `JAVA_HOME` | none | JDK used by Gradle. Android Studio's bundled `jbr` works. |
| `ANDROID_HOME` | `%LOCALAPPDATA%\Android\Sdk` on Windows | Android SDK location. |
| `STORE_FILE` | none | Keystore path, from `signing.properties`. |
| `STORE_PASSWORD` | none | Keystore password, from `signing.properties`. |
| `KEY_ALIAS` | none | Signing key alias, from `signing.properties`. |
| `KEY_PASSWORD` | none | Signing key password, from `signing.properties`. |
