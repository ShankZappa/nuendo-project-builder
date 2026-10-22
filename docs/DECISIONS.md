# Architecture decisions

## ADR-001 — Separate product from DAW Project Cleaner

**Status:** Accepted, 2026-08-09

**Decision:** Build Nuendo Project Builder as a sibling project. Share only
clearly bounded infrastructure.

**Reason:** Cleaner handles archive ownership, quarantine and constrained
project cleanup. Builder handles editorial interchange, timecode and session
creation. Combining them would enlarge the destructive product's trust boundary
and make validation harder.

## ADR-002 — AAF plus Nuendo template before native NPR generation

**Status:** Accepted, 2026-08-09

**Decision:** The first supported output is an AAF package imported into a
pinned Nuendo template. Direct `.npr` generation is excluded from MVP.

**Reason:** Nuendo officially imports AAF at original timecode positions, while
its native binary serialization is private and version-specific. Cleaner's
ability to delete a few validated Pool records does not provide a general
writer for new tracks and events.

## ADR-003 — Standard exports instead of native NLE project parsing

**Status:** Accepted, 2026-08-09

**Decision:** Accept AAF and FCPXML/FCPXMLD first. Do not parse `.prproj`, Final
Cut library databases or Avid bins in MVP.

**Reason:** Interchange formats are intended for cross-application transfer and
can be validated independently. Native project storage adds large, unstable and
unnecessary compatibility surfaces.

## ADR-004 — Reviewable synchronization

**Status:** Accepted, 2026-08-09

**Decision:** The sync engine emits proposals with evidence, confidence and
conflicts. It does not silently commit uncertain matches.

**Reason:** Incorrect sync can look plausible and reach delivery unnoticed.
Operator review is inexpensive compared with repairing a corrupted edit.

## ADR-005 — Local, offline-first desktop application

**Status:** Accepted, 2026-08-09

**Decision:** Process editorial and production media locally. No account,
telemetry or cloud upload is required.

**Reason:** Film material is large, confidential and normally already present
on local or studio storage. The existing Cleaner packaging model demonstrates a
terminal-free Mac delivery path.

## Open decisions

- Exact public product name and visual identity.
- Pinned Nuendo 15 maintenance release.
- AAF library/SDK and its distribution license.
- SwiftUI versus AppKit for the first interface.
- Python versus Rust for the long-term processing core.
- Default handle duration and track/group preset.
- Whether the first private pilot includes Premiere only or Premiere plus Final
  Cut and Avid.
- Minimum macOS version and sandbox entitlements. MVP architecture is Apple
  Silicon arm64; additional architectures require a later decision.
- DMG versus ZIP delivery, release ownership and update/rollback strategy.
