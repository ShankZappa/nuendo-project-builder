# Development plan

## Planning assumptions

- First platform: Apple Silicon macOS.
- First receiving application: a Studio89 Nuendo 15 maintenance release that
  must be selected and pinned during Phase 0 before implementation claims begin.
- First editorial inputs: one-sequence AAF and FCPXML/FCPXMLD.
- First production-audio inputs: WAV/BWF with optional iXML and LTC.
- First output: AAF with separate audio, reference video and reports.
- Source projects and source media remain read-only.
- Work is promoted only after application-level validation, not parser tests
  alone.

## Phase 0 — research fixtures and decisions

**Goal:** replace assumptions with a controlled interchange corpus.

This is the immediate next deliverable and precedes production implementation.
Its artifacts are owned by the project maintainer and stored as public generated
fixtures plus private rights-cleared validation material under the repository
separation rules.

Tasks:

- Record exact macOS, Nuendo, Premiere, Final Cut and Avid versions in scope.
- Create a small rights-cleared test shoot with slate, BWF/iXML and LTC variants.
- Export equivalent cuts from Premiere, Final Cut and Avid where available.
- Produce AAF/FCPXML samples covering straight cuts, gaps, disabled clips,
  handles, stereo/polywav, DF/NDF and missing media.
- Test each editorial application's recommended turnover against Nuendo.
- Evaluate AAF libraries and their licenses, maintenance and macOS packaging.
- Pin the first supported AAF and FCPXML subsets.
- Pin supported reference-video codecs and PCM WAV/polywav constraints.
- Define the delivery-preset schema, copied-media default, handle default and
  initial Studio89 track mapping.
- Decide whether nested, multicam, compound and retimed structures are rejected
  or transformed individually; no global silent-flatten rule is allowed.
- Write fixture privacy and repository-separation rules.

Exit gate:

- At least one AAF and one FCPXML path have an understood, reproducible Nuendo
  handoff.
- Unsupported structures and expected warnings are documented.
- The exact Nuendo maintenance release, source-application versions, AAF
  implementation and dependency licenses are pinned.

## Phase 1 — read-only preflight prototype

**Goal:** inspect inputs and produce a normalized report without exporting.

Tasks:

- Scaffold parser, model, CLI test harness and generated fixtures.
- Implement exact rational time and frame-rate primitives.
- Implement AAF sequence ingestion for the pinned subset.
- Implement FCPXML/FCPXMLD ingestion for the pinned subset.
- Probe reference video and resolve referenced media paths.
- Read WAV/BWF/BEXT/iXML metadata and channel layouts.
- Emit a stable JSON preflight report.
- Add cancellation, bounded concurrency and visible parse errors.

Exit gate:

- Golden fixtures reproduce every source and timeline range exactly.
- Malformed files, ambiguous paths and unsupported constructs fail closed.

## Phase 2 — metadata and LTC synchronization

**Goal:** create explainable production-audio match proposals.

Tasks:

- Detect candidate LTC channels without assuming left or right.
- Decode LTC frames with sample positions and continuity diagnostics.
- Normalize BWF, embedded video timecode and LTC into one time domain.
- Implement matching evidence, confidence and conflict rules.
- Add handles and channel-to-track mapping.
- Produce a human-readable match report.
- Test missing timecode, contradictory timecode, midnight rollover, DF/NDF,
  interrupted LTC and two matching takes.

Exit gate:

- Every automatic match can state why it was selected.
- Ambiguous and conflicting cases remain unresolved instead of guessed.

## Phase 3 — desktop review application

**Goal:** let a sound editor complete the workflow without a terminal.

Tasks:

- Build native macOS project-creation and file-selection screens.
- Show discovery and analysis progress with Stop support.
- Build timeline overview and per-edit match inspector.
- Add accept, reject, replace, offset and track-assignment controls.
- Add Nuendo target presets and saved folder permissions.
- Surface unsupported editorial features before export.
- Add resumable project state that does not contain source audio.

Exit gate:

- A non-technical operator can create, review and save a complete build plan.
- Closing the app during read-only analysis cannot alter inputs.

## Phase 4 — AAF and package export

**Goal:** generate a staged, auditable Nuendo-ready delivery.

Tasks:

- Implement AAF export for the validated subset.
- Support separate BWF media and configurable handles.
- Produce the package structure, import instructions and manifests.
- Reparse the generated AAF and compare it with the approved build plan.
- Validate every generated/copied file size and digest.
- Add output collision protection and atomic finalization.
- Add Open Output and Launch Nuendo actions.

Exit gate:

- Generated AAF reparses to the approved event count, ranges and media map.
- No source file changes and no unexplained output files are detected.

## Phase 5 — Nuendo application validation and private pilot

**Goal:** prove the workflow in the exact receiving application.

Tasks:

- Import controlled packages into the pinned Nuendo 15 version.
- Check project start, frame rate, DF/NDF, sample rate and event positions.
- Check audio/video sync at beginning, middle, end and edit boundaries.
- Check channel names, handles, fades and missing-media state.
- Save, close, reopen and play the resulting project.
- Compare expected and actual Pool/media contents.
- Run several disposable real-world turnovers with manual review.
- Freeze the first supported delivery profile.

Exit gate:

- All controlled cases pass import, playback, resave and reopen.
- Every known unsupported case is blocked or explicitly labeled.

## Phase 6 — signed internal release

**Goal:** distribute a normal Mac application without terminal setup.

Tasks:

- Bundle all approved local dependencies.
- Declare the minimum macOS version, CPU architectures, sandbox/entitlement
  policy and supported local/network storage behavior.
- Add app icon, versioning, privacy text and diagnostics export.
- Run clean-machine and offline tests.
- Sign with Developer ID, notarize and staple the ticket.
- Assign ownership of Apple Developer credentials and notarization.
- Test Gatekeeper quarantine and security-scoped folder access on a clean Mac.
- Choose DMG or ZIP delivery and document update/rollback policy.
- Publish checksums and a rollback-capable installer/update procedure.
- Write the operator guide and troubleshooting matrix.

Exit gate:

- The app launches on a clean supported Mac without developer tools.
- A full supported job can be completed offline from UI to Nuendo handoff.

## Phase 7 — post-MVP research

Prioritize only after the supported workflow is stable:

1. Waveform fingerprint matching.
2. Clock-drift analysis and correction proposals.
3. Picture-change lists and reconform.
4. Batch episodes and reusable show presets.
5. Additional AAF/FCPXML versions.
6. Windows packaging if there is demonstrated demand.
7. Direct `.npr` generation only as a separate research track with exact-version
   format profiles and Nuendo validation.

## Initial backlog

### P0

- Choose and document the pinned Nuendo 15 version.
- Assemble the controlled fixture shoot and editorial exports.
- Select the AAF implementation strategy.
- Specify exact time primitives and normalized model schemas.
- Build AAF/FCPXML preflight readers.
- Build BWF/iXML/timecode inspection.

### P1

- LTC decoding and channel classification.
- Match scoring and conflict representation.
- Desktop input/preflight screens.
- Timeline review model.
- AAF round-trip validation.
- Nuendo import checklist automation in the report.

### P2

- Studio89 routing/group presets.
- Waveform matching.
- Reconform.
- Batch builds.

## Milestone reporting

Each milestone report records:

- supported and refused format/version profiles;
- fixture and application test coverage;
- open correctness or licensing risks;
- performance on local and network storage;
- known manual steps;
- whether the build is research-only, internal-pilot or distributable.
