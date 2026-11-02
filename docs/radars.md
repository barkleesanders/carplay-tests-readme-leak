# The Radars

Radar is Apple's internal bug/feature tracker. `rdar://` links resolve only
inside Apple, so none of these can be opened publicly — what is documented
here is what the leaked measurement reference itself says about each one.
Ten distinct radars are referenced.

| Radar | Subject (per the document) | Notes |
|-------|----------------------------|-------|
| `rdar://180319459` | Rave SDK splits each asset duration into begin/end absolute timestamps | The change that made the utility data-driven (timestamp pair preferred, duration fallback) |
| `rdar://160878215` | [In-Vehicle KPIs][TC-2a] Local UI Presentation Time | Quotes engineer Kelly Cole's trace: `DPCreated` fires at `DPDisplaySessionCreate`, ~13 s before the first stats payload, after rolling off the accessory's ~10 s buffer |
| `rdar://182233742` | "Cache stats events for iOS consumption on incoming iOS Statistics Request" | Filed by Kelly Cole — the requested accessory-side fix for the TC-2a/TC-2e roll-off |
| `rdar://160878427` | TC-1 / TC-2b asset loading & foregrounding | One CPTA view covers both pairing and reconnection |
| `rdar://160640182` | TC-2c reconnection beginning time | Open: what to display when `iAPProfileConnectionAttempt` is never sent (no Bluetooth) |
| `rdar://160874716` | [In-Vehicle KPIs][TC-2d] Total Reconnection Time | Open: no-Bluetooth display behaviour; whether the end signal should also consider `AP_SIGNPOST_MAINSCREEN_DISPLAYING` / `AP_SIGNPOST_ALTERNATESCREEN_[%d]_DISPLAYING` |
| `rdar://160878706` | TC-2e video setup time | Open: measured values "very high"; correct start signal undetermined |
| `rdar://160884515` | TC-10 built-in → CarPlay Ultra time | Open uncertainty #1: signposts vs. TTCV (high-FPS camera) measurement |
| `rdar://160888625` | Core Analytics aggregation of tick histograms into `LR_stats` on the CarPlayApp `sessionEnded` event | The analytics path TC-13b does not depend on |
| `rdar://160888793` | TC-13b tick rate & tick duration | Tracking radar for the histogram metrics |

## The filing pattern

Eight of the ten numbers sit in one tight block (`160640182`–`160888793`,
and within it a dense `16087…`–`16088…` cluster), consistent with a single
filing pass when the in-vehicle KPI test program was stood up. The two
outliers are later:

- `rdar://180319459` — the Rave SDK behaviour change the program then had
  to absorb, and
- `rdar://182233742` — the fix request that came out of hitting the
  stats-buffer limitation in practice.

Internal reference also made to Apple's CarPlay SDK statistics
documentation on its internal Confluence (`confluence.sd.apple.com`,
space `CAR`, "Current SDK Statistics") — external readers cannot resolve
these; they are recorded here only as evidence of where the canonical stat
definitions live inside Apple.
