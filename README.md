# The CarPlay Tests README Leak — CarPlay Tests 2.19.6

In October 2026, Apple shipped an internal engineering document inside a public
App Store app. This repo documents what was in it, how it was identified and
verified, and what it reveals about Apple's CarPlay Ultra testing program —
in original analysis, with cryptographic hashes so anyone can verify the
artifact against their own copy.

> **What is not in this repo:** Apple's verbatim `README.md` and the app
> binaries (IPAs) are Apple's copyrighted material and are not redistributed
> here. Instead, this repo publishes the SHA-256 hashes of the exact files
> and [instructions to extract the document yourself](docs/provenance.md)
> from the same App Store version. A hash match is stronger evidence than a
> re-uploaded copy, and it keeps this record from disappearing in a takedown.

## What happened

- **2026-10-29 16:40 PDT** — Aaron (@aaronp613) posted on X: *"Oopsie, someone
  at Apple left a README.md in a recent app update"*, with a Finder screenshot
  of a file headed `# PerformanceMetricsUtility — Measurement Reference`.
- **The update was Apple's CarPlay Tests 2.19.6** (`com.apple.carplaytests`),
  Apple's MFi-licensee test app. The document lived at
  `Payload/AudioTests.app/Frameworks/PerformanceMetricsUtility.framework/README.md`
  inside the app bundle — 23,801 bytes, 535 lines.
- **2026-10-30** — Apple shipped CarPlay Tests **2.19.7**, which removed the
  README *and* all 13 of its referenced images from the framework. The
  information below survives because the 2.19.6 artifact was captured and
  hashed before it vanished.

## Why this is useful

- **Preservation.** Apple has already scrubbed the file from the current
  release. Without a hashed, timestamped public record, the episode becomes
  unverifiable hearsay within one news cycle.
- **Independent verification.** The hashes in [provenance](docs/provenance.md)
  let any researcher, journalist, or developer who obtains CarPlay Tests
  2.19.6 confirm byte-for-byte that they are looking at the same document
  this analysis describes.
- **MFi developers and accessory makers** get a consolidated, readable map of
  the performance metrics Apple measures for CarPlay Ultra — thresholds,
  test cases, and known measurement problems — in one place, instead of
  scattered screenshots.
- **Interoperability and repair research.** The document maps the private
  statistics interfaces between a vehicle and an iPhone (stat streams, keys,
  signposts) — the kind of interface knowledge interoperability work runs on.
- **Public-interest reporting.** It is a primary-source record of an
  unreleased platform's (iOS 27 "Rave" / CarPlay Ultra) engineering state:
  its targets, its bugs, and its unfinished work, in Apple's own framing.

## What's documented here

| Doc | Contents |
|-----|----------|
| [docs/provenance.md](docs/provenance.md) | Identification chain, timestamps, SHA-256 hashes, how to extract and verify the original yourself |
| [docs/test-cases.md](docs/test-cases.md) | The TC-1 → TC-13b test suite: requirements, thresholds, measurement methods, known flaws |
| [docs/radars.md](docs/radars.md) | All 10 internal Apple Radar references, what each tracks, and the filing pattern |
| [docs/hardware-and-rig.md](docs/hardware-and-rig.md) | The dev setup and vehicle test rig the document describes |
| [docs/what-it-exposes.md](docs/what-it-exposes.md) | Calibrated impact analysis: who can use this, for what — and what it cannot do |
| [docs/people.md](docs/people.md) | The two Apple employees named in the document, from public professional sources only |

## Evidence

- `evidence/aaronp613-screenshot.jpg` — the screenshot from the original
  X post (1074×330), the starting point of the identification.

## License / status of the material

The analysis, tables, and text in this repo are original work, released for
research and reporting use. Apple's document and binaries remain Apple's
property; they are described and hashed here, not reproduced. If you hold
rights in the underlying material and want something addressed, open an
issue.
