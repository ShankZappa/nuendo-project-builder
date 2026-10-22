# Project operating rules

## Scope

This directory is the canonical workspace for JARVIS Nuendo Project Builder.
Keep it separate from DAW Project Cleaner. Reuse code only through an explicit,
tested shared component or a copied module whose origin and license are recorded.

## Safety invariants

- Source editorial projects, video and production recordings are read-only.
- Never overwrite an AAF, FCPXML, video, BWF/iXML file or Nuendo template.
- Generated output always goes to a new, empty destination package.
- Treat every media file, interchange file and metadata field as untrusted.
- Fail closed on unsupported frame rates, malformed timelines, ambiguous paths,
  conflicting timecode, symlinks and changed inputs.
- Automatic matches need an explainable confidence result and evidence list.
- A user review is mandatory before a package is declared export-ready.
- Never claim that a generated package is Nuendo-compatible until it has passed
  the pinned Nuendo version's import, open, playback and resave test.
- Direct CPR/NPR generation remains out of scope unless separately researched,
  version-gated and application-validated.

## Development hygiene

- Use generated fixtures or rights-cleared disposable media in public tests.
- Keep private production samples and derived reports under `internal-research/`
  and exclude them from source control and releases.
- Keep parsing, matching, export and UI as separate modules.
- Preserve exact rational timing; do not use floating-point seconds as the
  canonical timeline representation.
- Every export must include a provenance manifest and validation report.

