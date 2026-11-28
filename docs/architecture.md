# Architecture

AirDeck for Android is a Kotlin and Jetpack Compose app, package `io.github.horse3903.airdeck`, min SDK 26. It draws the controller, shapes touch into gamepad state and sends it to the PC server.

## Components

| Part | Where | Job |
|---|---|---|
| Connection | `network/ConnectionHub.kt`, `ConnectionViewModel.kt` | A process-wide view model so the connection outlives the activity. Sends `VGPHELLO`, parses `VGPSLOT` and `VGPRUMBLE`, detects when the PC closes. |
| Service | `network/ConnectionService.kt` | Foreground service with Wi-Fi and partial wake locks and a notification showing state and controller number. |
| Controller screen | `ui/screens/Gamepad.kt` | Hosts the controller, the send loop, reconnect, tilt sensor and rumble. |
| Drawing | `ui/composables/Gamepad.kt` | `DrawGamepad` lays out every control in a padded box and draws the slot glow. |
| Controls | `ui/composables/` | `AnalogStick`, `Dpad`, `Trigger`, buttons, `ConnectionBadge`, dialogs. |
| Layout editor | `ui/screens/GamepadCustomization.kt` | Draws the real controller at screen size and hit-tests each control's reported bounds. |
| Settings | `data/AppPrefs.kt`, `SettingsRepository.kt` | DataStore-backed preferences and per-layout behaviour overrides. |
| Layout format | `data/ConfigImport.kt`, `LayoutGroups.kt`, `SuggestedLayouts.kt` | Parse and write layouts, groups, and the suggested sets. |
| Curves | `data/Curve.kt`, `StickUsage.kt` | Response curves and usage histograms. |

## Connection

The view model keeps a command queue and a socket. After connect it sends the hello line, then a reading about every 80 ms. A reader thread parses `VGPSLOT` and `VGPRUMBLE`. Back from the controller leaves to the main page and keeps the connection. A lost connection retries for the configured time, showing the hourglass, then leaves. See the [protocol](https://github.com/horse-3903/AirDeck/blob/main/docs/protocol.md).

## Layout model

- A component's offset is a multiple of `baseDp`, the screen height in dp, anchored to the padded box. Offsets go to plus or minus 1.5.
- Rectangular controls are stretched by laying out at the new size (`reflow` in `EditableComponent.kt`), so text keeps its size.
- Controls come in groups that only exist while editing, stored as `layout_groups`.
- Layouts export as JSON version 2 with controls, groups and an optional `behaviour` object. See [layouts](https://github.com/horse-3903/AirDeck/blob/main/docs/layouts.md#sharing-text).

## Settings and behaviour

Each setting is a global default. A layout can override it. `AppPrefs.withBehaviour` and the providers in `MainActivity` give every composable the effective value through `LocalAppPrefs`, `LocalStickSnap` and `LocalDpadSingle`. Overrides are stored as whole numbers (on is 1, choices are positions, curves are flattened with `Curve.toInts`). Editing the locked Default turns it into an unsaved layout.

## Sticks, D-pad and triggers

- **Sticks** with snap listen in a rounded rectangle and follow the finger. Floating sticks take the first touch as their centre and reset in a `finally` block so a cancelled gesture cannot stick.
- **D-pad** is one canvas with a single touch area. Several fingers each add a direction.
- **Triggers** apply their response curve in `write`.

## Rumble and haptics

`HapticUtils.gameRumble` plays a repeating waveform of 1 second segments, so a rumble lasts until the next message. Strength 0 cancels. Never use a fixed-length one-shot, because the PC does not resend.

## Gamepad styles

`GamepadStyle` has `XBOX`, `PLAYSTATION` and `NINTENDO`. It is stored by ordinal, so only append. Every `when` over it is exhaustive, so adding a style fails to compile until all are handled.
