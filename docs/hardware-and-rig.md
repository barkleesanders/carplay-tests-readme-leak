# Hardware and test rig

Everything below is drawn from the leaked measurement reference: the
author's own development setup, and the equipment and constraints the test
flows presuppose.

## The author's development setup (verbatim facts)

- **Xcode:** build `Meadow27A224` ("Meadow" = Xcode codename)
- **iOS SDK:** `Rave24A387` ("Rave" = iOS 27 codename; build 24A387)
- **Test app:** CPTA (CarPlay Tests) **running on a physical iPhone** on
  build 24A387
- **CarPlay Simulator:** version **4.223** (macOS side)

A note in the TC-2a discussion says the stats-buffer bug "reproduces more
reliably on **iPad**", so iPads are also part of the test fleet.

## The vehicle side

- A **CarPlay Ultra–capable vehicle / head unit** ("accessory") running
  display plug-ins for at least two displays named in the data:
  **`Center_Display`** (infotainment) and **`Cluster_Display`**
  (instrument cluster).
- The accessory donates statistics in two streams: **DPStats** (per
  display, from the Display plug-in) and **CommStats** (session-wide, from
  the Comm plug-in).
- **The accessory's stats buffer holds only ~10 seconds of events**, and
  nothing is transmitted until the iPhone connects and issues a Statistics
  Request (observed interval: 10 s). Events stamped at plug-in creation
  (`DPCreated`) therefore typically expire before the phone ever sees them —
  the single most consequential hardware constraint in the document.
- The vehicle provides a **built-in (OEM) UI** alongside CarPlay, reached
  in testing through **PartnerConfigs** (the "Car" icon); TC-10 measures
  leaving it and returning to CarPlay Ultra.
- Vehicle-state hardware is in scope for the unimplemented tests: state
  **characteristics** whose updates can exceed ~65 Hz, and write →
  update-notify latency/timeouts (the `com.apple.cafd.*` keys).

## The test procedures as physical actions

- **Pairing (TC-1):** device not yet paired; measurements captured during
  the pairing flow itself.
- **Reconnection (TC-2):** vehicle **warm-booted**; tester confirms the
  iPhone is disconnected, taps the app's **Door Open** button to arm
  observation *before* reconnecting, physically re-enters, and starts the
  vehicle.
- **Transports:** the connection-attempt and connection-success events are
  recorded **only on Bluetooth**; the session itself also involves AirPlay
  connection events. A **Digital Car Key (DCK)** unlock signpost is under
  investigation (with the Car Keys test team, CKTA) as a future replacement
  for the manual Door Open tap.
- **Camera rig (proposed):** for TC-10, an undecided alternative to
  signpost timing is **TTCV — a high-FPS external camera** aimed at the
  displays, which would ground-truth the "≤500 ms, no black transitions,
  all displays simultaneous" requirement instead of trusting the
  accessory's self-reported timestamps.
