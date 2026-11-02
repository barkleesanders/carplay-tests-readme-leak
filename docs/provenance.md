# Provenance — how the update was identified and verified

## The posts

| Post | Author | Time | Content |
|------|--------|------|---------|
| X `2115951859122790596` | Aaron (@aaronp613) | 2026-10-29 16:40 PDT (23:40 UTC) | "Oopsie, someone at Apple left a README.md in a recent app update" + Finder screenshot of `README.md` |
| X `2116106281614258231` | Leo (@piccionenter) | 2026-10-30 02:53 PDT | "Even apple got sloppy lmao" (reply in the same thread) |

The screenshot (see `evidence/aaronp613-screenshot.jpg`) shows the document's
opening lines:

```
# PerformanceMetricsUtility — Measurement Reference

My setup during development:
Xcode Meadow27A224
iOS SDK: Rave24A387
CPTA running on a iPhone on 24A387
CarPlay Simulator 4.223
```

## Identification chain

1. `PerformanceMetricsUtility` appears nowhere on the public web — the file
   only exists inside a shipped binary, so the candidate set was Apple's own
   apps updated that week.
2. The screenshot's context is entirely CarPlay performance measurement
   ("CPTA", CarPlay Simulator). CPTA is the **CarPlay Tests App**.
3. **Decisive link:** AppleDB (github.com/littlebyteorg/appledb, maintained
   by Aaron) commit `23c76ba426e822a9a5c0204af412d5afa2bdb025` —
   *"Create 2.19.6.json"*, file `osFiles/Software/CarPlay Tests/2.19.6.json`,
   committed **2026-10-29T23:03Z — 37 minutes before Aaron's post**. AppleDB
   lists CarPlay Tests **2.19.6** as released 2026-10-29.
4. The actual 2.19.6 IPA was downloaded from Apple's App Store with the
   account holder's own Apple ID (via `ipatool`), using App Store external
   version ID `892305836`. The document was extracted from the unmodified IPA
   and its opening lines match the screenshot exactly.
5. Cleanup check: the same framework in **2.19.7** (external ID `894226649`,
   released 2026-10-30) contains only the binary and plists — the README and
   its 13 images were removed.

## The app

- **Name:** CarPlay Tests (binary/app name `AudioTests.app`)
- **Bundle ID:** `com.apple.carplaytests`
- **App Store ID:** 1568604307
- **Audience:** MFi licensees — "test and validate audio, video, performance
  and other key requirements needed to successfully certify your CarPlay
  implementation"
- **2.19.6 Info.plist:** `CFBundleShortVersionString 2.19.6`,
  `CFBundleVersion 219616`, `MinimumOSVersion 26.0`

## Hashes (SHA-256)

| Artifact | SHA-256 | Size |
|----------|---------|------|
| CarPlay Tests 2.19.6 IPA | `1fd1e7957907ce37fac998d5f312ef0b42a29f8e30ed9b50610e1cd2a1a3d2e7` | 85,204,159 B |
| CarPlay Tests 2.19.7 IPA | `ae91737fa21d9d8b5cb29490ad46a47b73d147e0561ed21daff7a144b2970ee0` | 84,430,135 B |
| Extracted `README.md` (from 2.19.6) | `47608ce3b2951d8d874c75c5027416b7a06e74832d8381a1ee7057ba045c279a` | 23,801 B, 535 lines |
| Aaron's screenshot | `84663d37a5a8fb8c6ac75a654e784d6325d1cf171790ee4c942eeb32d18e0909` | 42,338 B |

The README's internal file timestamp in the IPA is 2026-09-21 14:46, and App
Store version metadata lists 2.19.6's release date as 2026-09-21T21:46:28Z —
the build sat for ~5 weeks before it was publicly catalogued on Oct 29.

## Verify it yourself

1. Obtain CarPlay Tests 2.19.6 from the App Store version history (e.g. with
   [`ipatool`](https://github.com/majd/ipatool):
   `ipatool download -b com.apple.carplaytests --external-version-id 892305836`).
   You need an Apple ID that has "purchased" (downloaded) the free app.
2. Check the IPA hash against the table above.
3. Unzip a *copy* and read
   `Payload/AudioTests.app/Frameworks/PerformanceMetricsUtility.framework/README.md`.
4. Hash it: it must equal `47608ce3…279a` above.
5. For the negative control, do the same with 2.19.7 (external ID
   `894226649`): the framework no longer contains any `README.md`.

*Recovery performed 2026-11-01 by Nova for Barklee Sanders; the source files
are additionally backed up in the owner's Google Drive with this same note.*
