# Safety and validation

## Core rule

The application may automate preparation, but it must not hide uncertainty.
Source media is irreplaceable; editorial interchange is lossy; frame-rate or
timecode mistakes can produce plausible-looking but incorrect synchronization.

## Input safety

- Open source files read-only.
- Do not follow symlinks or traverse outside user-selected roots implicitly.
- Record size, modification time and digest before long-running analysis.
- Revalidate changed inputs before export.
- Bound every parser read and reject malformed declared lengths.
- Treat external paths, XML entities and AAF references as untrusted.
- Never execute embedded content or load media plugins.

## Timing invariants

- Preserve the source frame rate as a rational value.
- Preserve whether timecode is drop-frame or non-drop-frame.
- Keep media sample rate separate from timeline frame rate.
- Require explicit handling for mixed or variable frame rates.
- Detect LTC gaps, reversals, duplicate frame runs and discontinuities.
- Detect insufficient head/tail coverage for requested handles.
- Never silently round an event boundary to a different video frame.

## Match confidence

Confidence labels are behavioral gates:

- **Verified:** independent timecode and metadata evidence agree; all hard
  invariants pass. It may be preselected, but remains visible in the mandatory
  global review.
- **Probable:** one strong source and supporting evidence agree; operator
  confirmation is required for that individual match before export.
- **Ambiguous:** multiple candidates remain plausible; no default selection.
- **Conflict:** strong sources disagree; export blocked until resolved.
- **Unmatched:** no defensible candidate exists.

Export is allowed only when every included edit is Verified or has an explicit
operator decision. Ambiguous and Conflict items block export until corrected,
replaced or explicitly excluded. An Unmatched item must be excluded or marked as
an intentional editorial-only event. Drift requiring time-stretch or continuous
correction blocks MVP export; a plain documented offset may be applied manually.

The application, not an end-user preference, owns the auto-preselection
threshold for a released profile. The exact versioned scoring formula and
threshold are included in the build report. Every operator correction records
the original proposal, chosen file/channel, offset, user decision and timestamp
in the provenance manifest.

## Export transaction

1. Pin the approved build plan and every input digest.
2. Create a new staging directory on the output filesystem.
3. Generate or copy each output without modifying a source.
4. Hash output media and record provenance.
5. Reparse the generated AAF.
6. Compare reparsed events, ranges, rates and references with the plan.
7. Write final reports last.
8. Atomically rename staging to its unused final destination.
9. Leave failed staging recoverable for diagnostics or remove it only after
   showing the exact target and receiving user approval.

## Application-level validation

Parser round trips do not prove Nuendo behavior. Every promoted export profile
needs a controlled checklist in the exact Nuendo version:

- import completes without unexplained warnings;
- project start and frame rate are correct;
- event count and boundaries match the approved plan;
- reference video starts at the intended timecode;
- production sound is synchronized at several points;
- handles and channels are accessible;
- no LTC channel is routed to audible dialogue output;
- no media is unexpectedly offline;
- Save As, close, reopen and playback succeed.

## Private data

- Processing is local and offline-capable.
- Reports use relative or redacted paths when exported for support.
- Private productions, AAFs, FCPXMLs, audio and derived waveforms never enter
  the public repository or release package.
- Diagnostic export requires an explicit preview and excludes media by default.
