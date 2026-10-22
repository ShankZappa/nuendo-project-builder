# Technical architecture

## Architectural position

The application should be a native macOS desktop shell around a deterministic,
locally executed media-processing core. The UI owns user interaction; the core
owns parsing, timing, matching, validation and export. Neither layer modifies
source material.

```text
Native macOS UI
    |
    +-- project setup and presets
    +-- cancellable progress
    +-- timeline and conflict review
    +-- export approval
    |
Local processing core
    |
    +-- AAF adapter
    +-- FCPXML/FCPXMLD adapter
    +-- media metadata reader
    +-- BWF/iXML reader
    +-- LTC decoder
    +-- normalized timeline model
    +-- synchronization planner
    +-- AAF/package exporter
    +-- validation and provenance reporting
```

## Suggested implementation

### macOS application

- Swift with SwiftUI or AppKit; the choice is made during Phase 0.
- Sandboxed file access or security-scoped bookmarks for user-selected folders.
- No terminal window and no separately installed runtime.
- Developer ID signing and notarization before external distribution.

The existing DAW Project Cleaner proves that a native AppKit frontend plus a
bundled local backend is workable. Reuse its interaction patterns for folder
selection, progress, cancellation and report presentation, but do not couple
the two products' domain models.

### Processing core

Use a modular core with a stable JSON-compatible boundary during the prototype.
Python is acceptable for fast format research and can be bundled for macOS.
Once interchange and synchronization behavior is proven, benchmark whether the
long-term core should remain Python or move timing-critical components to Rust.

Candidate external components must receive a license and release-size review:

- an AAF SDK or library for reading and writing AAF;
- AVFoundation or a bundled, license-compatible FFmpeg build for media probing;
- a BWF/iXML parser;
- libltc or an equivalent tested LTC decoder.

No dependency is approved merely by appearing in this document.

## Normalized internal model

Adapters must not feed each other's native structures into the sync engine.
They convert all inputs into a common model:

```text
BuildProject
  sequence
    name
    start_time
    frame_rate
    timecode_mode
    tracks[]
      role / name / order
      edits[]
        timeline_range
        source_asset_id
        source_range
        enabled
  assets[]
    canonical_path
    digest
    duration
    video/audio properties
    embedded timecode sources[]
  recordings[]
    BWF/iXML metadata
    channels[]
    decoded LTC spans[]
  match_proposals[]
    edit_id
    recording/channel
    offset and handles
    evidence[]
    confidence
    conflicts[]
```

Canonical times use integer samples or rational seconds with an explicit rate.
Drop-frame is a display/counting rule and must remain explicit. Conversion at
adapter boundaries must be covered by golden tests.

## Timecode-source priority

1. Valid editorial source timecode plus matching BWF time reference/iXML.
2. Embedded media timecode track plus BWF time reference/iXML.
3. Continuous decoded LTC on a known channel.
4. User-confirmed slate/offset information.
5. Waveform correlation as a later fallback.

Multiple agreeing sources increase confidence. Disagreement never resolves by
arbitrary priority alone: the conflict is shown to the user.

## Matching model

A proposed match is explainable evidence, not a boolean guess. Initial scoring
uses:

- timecode overlap and offset consistency;
- compatible frame and sample rates;
- scene/take/reel agreement;
- recording date proximity;
- duration coverage including handles;
- filename or clip-description evidence;
- channel availability;
- continuity with adjacent edits.

An auto-accepted match must pass hard invariants and a configurable confidence
threshold. Ambiguous top candidates remain unresolved.

## Output package

```text
PROJECT_NAME_JARVIS/
  PROJECT_NAME.aaf
  Video/
    reference-video.ext
  Audio/
    generated-or-copied-media.ext
  Reports/
    preflight-report.html
    project-manifest.json
    build-log.jsonl
  Template/
    Studio89-Nuendo15-Template.npr
    README-IMPORT.txt
  Nuendo Project/
    # operator's Save As destination; initially empty
```

MVP copies required audio into the package so the delivery is self-contained.
Referenced-media output is enabled only after separate Nuendo relink validation.
Any copied or transformed media receives a digest and a record connecting it to
the source asset. The approved Nuendo template is copied into the package and
the source template remains byte-identical. Output is first staged in a new
temporary package, validated, and then atomically promoted to the chosen final
location.

## Nuendo handoff

The supported first workflow is:

1. Open the package's copied, pinned Studio89 Nuendo 15 template without
   modifying the source template.
2. Import the generated AAF into the active project.
3. Choose absolute-position import.
4. Attach or import the reference video if the delivery profile requires it.
5. Run the generated validation checklist.
6. Use Save As to create a distinct `.npr` under `Nuendo Project/`; do not
   overwrite either the source template or its packaged copy.

UI scripting of Nuendo is not part of the trusted core. It may be researched as
an optional convenience only if failure cannot corrupt or misrepresent output.

Here, **Nuendo-ready** means that the package has passed internal structural
validation and is ready for the documented import step. **Nuendo-compatible**
is reserved for an export profile that has also passed import, playback, Save
As and reopen in its exact pinned Nuendo maintenance release.

## Distribution boundary

The MVP is Apple Silicon-only. The first external build must declare a minimum
macOS version, bundle every runtime and native dependency for arm64, and
document all third-party licenses. Distribution uses a
Developer ID signed and notarized DMG or ZIP chosen during release design. A
clean-machine test must cover Gatekeeper quarantine, first launch, security-
scoped access, offline use and local plus explicitly supported network volumes.
Apple Developer credentials and notarization ownership must be assigned before
Phase 6. The initial release may omit automatic updates; if updates are added,
their signature verification and rollback policy require a separate decision.

## Relationship with DAW Project Cleaner

Safe candidates for a future shared library are:

- bounded and cancellable traversal;
- path normalization and symlink refusal;
- hashing and changed-file detection;
- atomic report writing;
- structured progress events;
- application packaging and release checks.

Project Builder's timeline, sync and interchange models remain independent.
Cleaner may later read the Builder manifest as supplemental provenance, but the
manifest must not become sole authorization for destructive cleanup.
