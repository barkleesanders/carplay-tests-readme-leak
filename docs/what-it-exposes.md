# What this actually exposes — a calibrated assessment

## What it is

An internal Apple engineering reference for the utility that measures a
vehicle's CarPlay Ultra performance, shipped — apparently by accident —
inside a public App Store app (CarPlay Tests 2.19.6) that MFi licensees
download. It was removed, with its images, in the next release (2.19.7).

## Who can use it, and for what

**Carmakers and Tier-1 suppliers.** CarPlay Ultra certification is a gate
these companies must pass. The document is Apple's own measuring stick:
exact thresholds, the stat keys read, and the formulas applied. A supplier
can replicate the measurement on its own bench and predict pass/fail
before Apple tests — removing months of blind iteration.

**Gaming the measurement — because the document admits where it is bent.**
- The `DPCreated` timestamp usually rolls off the accessory's ~10 s stats
  buffer before the iPhone requests stats, so TC-2a and TC-2e often
  *cannot be measured at all*.
- The reconnection code reads `sessions.first` and assumes
  most-recent-first ordering; the document flags the ordering contract as
  unverified.
- TC-4, TC-5, TC-2f, TC-2g and TC-2i are **not implemented** — vehicle-state
  signal quality and write latency are currently unenforced.
- TC-10's measurement method is undecided, and TC-2a has **no threshold**
  because the built-in baseline is undefined.

**Competitors** (Android Automotive, Tier-1 platform teams) get Apple's
Ultra targets and pain points without reverse-engineering shipped cars:
the 3 s reconnection budget, the 500 ms simultaneous no-black-frame
built-in→Ultra requirement, vsync-matched tick requirements, and the
state of the iOS 27 ("Rave") vehicle-state API surface.

**Social engineers.** Real employee names, ten real internal Radar
numbers, real source file names, internal documentation locations, and
platform codenames (Rave, Luck, Meadow) are exactly the raw material that
makes a spear-phish to an Apple engineer or an MFi partner look
legitimate. This may be the most practically dangerous use.

**Security researchers.** The document is a map, not a weapon: it names
the private classes and the trust boundary — the vehicle, an untrusted
accessory, donates the timestamps the iPhone parses. That directs fuzzing
effort, but contains no vulnerability itself.

**Journalists and historians.** A primary source on an unreleased
platform's engineering state — targets, bugs, and unfinished work in
Apple's own framing, preserved with hashes and dates before the artifact
was scrubbed.

## What it does *not* expose

- No source code beyond small illustrative snippets of a *test utility*.
- No cryptographic keys, certificates, signing material, or user data.
- No exploitable vulnerability, and nothing that enables faking
  certification results, bypassing MFi licensing, or compromising a phone
  or a vehicle.
- The headline thresholds themselves were plausibly already known to
  licensees under NDA. The leak's real content is everything around them:
  Apple's internal doubts, known bugs, unimplemented tests, and reasoning.

**Bottom line:** low direct security impact; medium competitive and
trade-secret impact; high embarrassment. The risk runs to Apple and its
certification program's integrity — not to iPhone owners.
