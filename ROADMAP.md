# Roadmap

Planned work for AirDeck-Mobile, roughly in the order it will be built. Nothing here is a promise, and the order changes as things are tried.

## Done

- Layout library with previews, reordering, import and export, and layout switching from quick settings
- Game rumble on the phone
- Reconnect handling and the foreground service
- Joy-Con button style
- Floating sticks, response curves, tilt steering and per-layout settings
- Layouts can be exported from the app and imported in the browser controller, and the other way round

## Next

- **Trigger detents** - haptic ticks at configurable trigger thresholds, simulating resistance points.
- **AirDeck Sense: self-learning ergonomics** - learn thumb rest positions, missed buttons and stick bias, then suggest layout, hitbox, dead zone and curve changes between sessions. Opt in, local only.
- **AirDeck Sense: intent-aware touch** - estimate the intended control from the touch trajectory and landing history, and fall back to normal hit-testing when unsure.
- **Eyes-free gesture mode** - a controller with no fixed buttons where resting thumbs set the origins and swipes map to buttons.

## Later

- **Automatic game profiles** - switch layouts when the PC reports which game is running.
- **Split controller** - send input from this phone to a shared virtual controller together with another phone.
- **AirDeck Haptics engine** - virtual detents, stick boundaries and event-driven cues, mixed with game rumble and matched to the phone's actuator. Builds on trigger detents.
- **Haptic pattern editor** - build and share custom vibration patterns for buttons, triggers and the D-pad.
- **AirDeck Studio: visual input graph** - connect inputs, processing and outputs as nodes to build new controller behaviour.
- **Adaptive game-state layouts** - switch layout by gameplay context, such as on foot or driving.
- **One-handed and accessibility layouts** - ready-made presets for single-hand play.
- **Layout sharing** - a place to publish and download layouts.

The wider plan is in the [AirDeck roadmap](https://github.com/horse-3903/AirDeck/blob/main/ROADMAP.md).

## Known limits

- The server has no login yet, so pairing is open to anyone on the network.
