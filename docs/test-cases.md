# The PerformanceMetricsUtility test suite

What follows is an original summary of the test cases, thresholds, and
methods described in Apple's internal measurement reference — the document
that documents how the CarPlay Tests app ("CPTA") measures a vehicle's
CarPlay Ultra performance. It is not a reproduction of the document.

## Measurement model (applies to most tests)

- The vehicle ("accessory") donates statistics through per-display
  **Display Plug-in stats (`DPStats`)** and session-wide **CommStats**,
  consumed on the iPhone via `CARVehicleStatisticsSession` /
  `VehicleStatisticsObserver`.
- Two SDK generations donate differently:
  - **Luck SDK** (prior): precomputed durations (`AssetLoad_Ms`,
    `AssetForeground_Us`).
  - **Rave SDK** (iOS 27): begin/end absolute timestamps on the
    **AirTunesNTP clock** (1 s = 2³² ticks — not nanoseconds), which the
    utility subtracts. Reading them as nanoseconds understates results
    ~4.3×.
- The code does not branch on an SDK flag; per display it prefers the Rave
  timestamp pair and falls back to the Luck duration.

## Test cases

### TC-1 — Asset loading & foregrounding (pairing)
- **Target:** load + foreground assets on all displays **≤ 100 ms** total
  per display (`totalMs = assetLoadMs + assetForegroundUs/1000`).
- Captured during first pairing; a metric counts as complete only when both
  load and foreground values are present.
- Worked examples in the source show a real `Center_Display` capture at
  ≈135.8 ms — **a failing result**, included in the doc as an example.

### TC-2 — Reconnection bundle (device already paired, vehicle warm-booted)
Tester confirms the phone is disconnected, taps **Door Open** in the app to
arm observation, re-enters, and starts the vehicle.

- **TC-2a — Local UI presentation time.** Resume/present CarPlay Ultra local
  UI as fast as the built-in UI. Measured `DPCreated → OutputStreamFirstFrame`
  (LuckG builds and later). **No pass/fail threshold exists yet** — the
  built-in baseline is undefined.
  - ⚠️ Known flaw: `DPCreated` fires at display plug-in creation, *before*
    the iPhone's first Statistics Request; the accessory's stats buffer
    holds only ~10 s, so the timestamp usually rolls off and never reaches
    the phone. Reproduces more reliably on iPad. Documented as a known SDK
    limitation requiring an accessory-side caching feature.
- **TC-2b — Asset loading & foregrounding (reconnection).** Identical model
  and ≤100 ms target to TC-1.
- **TC-2c — Reconnection beginning time.** Door Open tap →
  `iAPProfileConnectionAttempt`; **target ≤ 2 s**. The end event is recorded
  **only on Bluetooth** — on non-BT reconnects the metric cannot be computed
  (and what to display in that case is undecided). Future: a Digital Car Key
  (DCK) unlock signpost may replace the manual tap.
  - ⚠️ To verify: the code reads `sessions.first` from the private
    connection-history store and assumes most-recent-first ordering; the
    ordering contract is unverified, so a stale session would produce
    negative or garbage deltas.
- **TC-2d — Total reconnection time.** `iAPProfileConnectionSuccess` →
  `AP_SIGNPOST_CAR_SENT_STATISTICS`; **target ≤ 3 s**. Start event is
  Bluetooth-only, same session-ordering caveat as TC-2c, plus an unverified
  assumption that the end signpost is CFAbsoluteTime (seconds since 2001).
- **TC-2e — Video setup time.** Vehicle must present the video stream UI
  **≤ 100 ms** after a setup request; measured `DPCreated →
  InputStreamFirstFrame-iPhoneVideo`. Values currently come out "very high"
  — `DPCreated` precedes the actual setup request, so the measured window
  is wider than the requirement intends; the correct start signal is still
  being determined. Same ~10 s buffer roll-off problem as TC-2a.
- **Not implemented:** TC-2f (Vehicle State Configuration), TC-2g (Mandatory
  Vehicle State Initiation), TC-2i (Optional Vehicle State Characteristic
  Setup). TC-2h needs no dedicated test (covered by reconnection tests).

### TC-4 — Vehicle state signal quality — *not implemented*
Documented from internal SDK guidance. Key:
`com.apple.cafd.vehicleStateHighFrequencyUpdate` — characteristics updating
above ~65 Hz within a rolling 1-second window are the signal-quality concern.

### TC-5 — Vehicle state characteristic change time — *not implemented*
Keys: `com.apple.cafd.vehicleStateWriteLatency` (write → updateNotify
samples, ms) and `com.apple.cafd.vehicleStateWriteTimeout` (writes with no
matching updateNotify). The doc notes the TC-4/TC-5 key assignment is
*inferred* and unconfirmed, and the keys are not yet in `StatsKeys.swift`.

### TC-10 — Built-in → CarPlay Ultra time
- **Requirement:** from the built-in UI, tapping the CarPlay icon must bring
  CarPlay Ultra to **all screens within 500 ms**, with **no transitions to
  black** and **all displays transitioning simultaneously**.
- Events: `RequestUI` (Comm plug-in, `CommStats`, one session-wide) and
  `ReadyToReveal` (Display plug-in, `DPStats`, per display) — recurring
  events that can each fire multiple times per session, paired by
  first-`RequestUI`-then-first-`ReadyToReveal`-at/after-it. Not donated
  before LuckG.
- **Measurement approach undecided:** signposts (current implementation) vs.
  **TTCV — a high-FPS external camera** pointed at the screens.
- Built-in UI is reached via **PartnerConfigs** (the "Car" icon).

### TC-13b — Tick rate & tick duration
- The vehicle must tick all display plug-ins at a consistent frame rate
  synchronized with the physical display's refresh (vsync), and declare a
  matching CarPlay Ultra stream rate.
- Data: `DPStats[].statsData[]` `tickRate` / `tickDuration` histograms
  (`"labels:counts"`, five bins, aggregated over the whole session; parser
  keeps the latest snapshot per display).
- **Pass/fail:** the 95th-percentile tick-rate bin must reach the top
  histogram bin (within 1%). Duration is informational only.
- ⚠️ To verify: the bin labels (`0/10/31/52/60`) are shared by rate and
  duration and do not behave like raw millisecond values for duration —
  bin semantics are to be confirmed on a real drive before trusting the
  pass/fail.
- A separate Core Analytics path aggregates the same histograms into an
  `LR_stats` field on the CarPlayApp `sessionEnded` event.

## Source layout named in the document

| Area | Files |
|------|-------|
| TC-2 metrics | `KPIMeasurements/KPIMeasurementsViewModel.swift`, `KPIMeasurementsView.swift` |
| Connection events | `CarPlayConnectionHistory/` — `CRCarPlayConnectionHistoryPrivate`, backed by `CARConnectionTimeStore.fetchRecentSessions` |
| Raw stat keys | `Common/StatsKeys.swift` |
| Time base | `Common/AirTunesNTP.swift` |
| TC-10 | `KPIMeasurements/BuiltInToCarPlayViewModel.swift`, `BuiltInToCarPlayView.swift` |
| TC-13b | `KPIMeasurements/TickRateViewModel.swift`, `TickRateView.swift` |
