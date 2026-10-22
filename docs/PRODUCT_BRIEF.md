# Product brief

## Problem

Preparing a film-sound session often involves repetitive technical work before
creative editing can begin: importing an editorial turnover, setting project
timecode and frame rate, locating production recordings, aligning takes,
splitting channels, recreating picture edits and organizing tracks. Editorial
exports may contain incomplete metadata, while production audio may carry its
timecode in BWF/iXML metadata, a QuickTime timecode track or an audible LTC
channel.

The goal is to automate the deterministic portion of this work while keeping
uncertain editorial and synchronization decisions visible to the sound editor.

## Primary user

A Nuendo-based film or television sound editor receiving picture turnovers from
Premiere Pro, Final Cut Pro or Avid Media Composer and original production audio
from one or more field recorders.

## Main workflow

1. Start a new build and select an output folder.
2. Add an AAF or FCPXML/FCPXMLD editorial export.
3. Add reference video and production-audio folders.
4. Choose the target Nuendo template and project preset.
5. Run read-only preflight.
6. Review frame rate, sequence start, media availability and timecode sources.
7. Review proposed clip-to-recording matches on a timeline.
8. Resolve ambiguous, missing or conflicting matches.
9. Generate the Nuendo-ready package.
10. Import the generated AAF into the pinned Nuendo template and run the
    application validation checklist.

## Required MVP capabilities

### Editorial ingestion

- Read one sequence from an AAF.
- Read one project timeline from FCPXML/FCPXMLD.
- Preserve clip names, source references, edit positions, source ranges,
  disabled state, track/role information and sequence timecode.
- Reject or clearly flatten unsupported nested, multicam, retimed and compound
  structures rather than silently changing them.

### Media inspection

- Read video frame rate, duration, audio-channel layout and embedded timecode.
- Read WAV/BWF technical properties, BEXT time reference and iXML scene, take,
  reel and channel metadata.
- Detect likely LTC channels and decode continuous LTC frame sequences.
- Never include an LTC channel in audible dialogue output by default.

### Synchronization

- Normalize every time value to an exact rational representation.
- Prefer matches supported by consistent timecode, overlapping duration and
  compatible frame/sample rates.
- Use scene, take, reel, recording date, filename and channel metadata as
  supporting evidence.
- Report discontinuity, drift and multiple-candidate conflicts.
- Preserve configurable handles before and after picture edits.
- Support multiple production channels or takes aligned beneath the same
  picture region.

### Review and output

- Show every proposed match, its method, confidence and warnings.
- Allow accept, reject, replace, offset and track-assignment corrections.
- Export AAF with separate BWF media and absolute timeline positions.
- Copy or reference a user-selected reference video according to the preset.
- Produce `project-manifest.json` and `preflight-report.html`.
- Open the output folder and optionally launch Nuendo after successful export.

The selected Nuendo template is never edited. MVP copies an approved template
into the new output package as `Template/Studio89-Nuendo15-Template.npr`, opens
that copy, and instructs the operator to import the generated AAF at absolute
position. The operator then uses Save As to create a distinct project under
`Nuendo Project/`; even the packaged template copy remains unchanged.

## Initial support matrix

| Area | MVP target | Refused or deferred |
|---|---|---|
| Editorial | One-sequence AAF; one-timeline FCPXML/FCPXMLD | Native NLE projects; EDL; ambiguous multi-sequence selection |
| Picture | One constant-frame-rate reference movie in a codec validated with the pinned Nuendo build | Variable-frame-rate media; unsupported codecs; silent transcoding |
| Production audio | PCM WAV/BWF, mono or polywav, with supported BEXT/iXML and optional LTC | Compressed production audio; malformed or contradictory metadata |
| Timing | One explicit project rate; supported DF/NDF modes; exact rational conversion | Unresolved mixed rates; unmarked rate conversion; ambiguous rollover |
| Sync | Timecode/metadata proposals with mandatory review | Unreviewed waveform-only matching; automatic drift correction |
| Output | AAF with separate BWF media, copied approved Nuendo template and reports | Direct NPR/CPR writing; destructive source consolidation |

Exact AAF subset, FCPXML version, video codecs, WAV constraints and application
versions are Phase 0 outputs. Until pinned and application-tested, the table is
a target rather than a compatibility claim. MVP starts with copied output media;
referenced-media mode is deferred until its relink behavior is separately
validated.

## Explicit non-goals for MVP

- Direct creation or arbitrary editing of `.npr`/`.cpr` files.
- Direct parsing of native Premiere `.prproj`, Final Cut libraries or Avid bins.
- EDL ingestion.
- Destructive movement, trimming or renaming of source recordings.
- Automatic dialogue editing, noise reduction, mixing or plugin insertion.
- Guaranteed waveform-only synchronization without operator review.
- Background cloud processing, telemetry or mandatory accounts.

## Success criteria

A representative turnover is successful when:

- the generated timeline matches the editorial cut at every tested boundary;
- all accepted production-audio events land at the expected absolute timecode;
- DF/NDF, frame rate and sample rate survive the exchange correctly;
- selected handles and channel names are preserved;
- the generated AAF imports into the pinned Nuendo version without missing
  media or unexplained warnings;
- picture and production audio play in sync at the start, middle and end;
- the imported project can be saved, closed and reopened;
- the manifest accounts for every generated or referenced file.

## Future capabilities

- Waveform-fingerprint synchronization when reliable timecode is unavailable.
- Long-recording drift estimation and non-destructive correction proposals.
- Picture-change comparison and reconform.
- Studio89 track, folder, group, marker and naming presets.
- Batch episode processing.
- Additional export profiles after separate application validation.
- EDL ingestion when its reduced metadata is sufficient for the chosen workflow.
