# Nuendo Project Builder

> **Status: concept and product specification only — no implementation yet.**
> Whether this product is built at all is an open question. It depends on its
> sibling product, [DAW Project Cleaner](https://github.com/ShankZappa/daw-project-cleaner),
> which is itself still in progress; the two are planned to share safe
> filesystem, hashing and reporting components. Until the Cleaner reaches a
> stable release, this repository documents the product thinking, not a
> committed roadmap.

## Why it is in this portfolio

This repository shows the product work that comes *before* code:

- [`docs/PRODUCT_BRIEF.md`](docs/PRODUCT_BRIEF.md) — problem, users, scope and non-goals
- [`docs/DEVELOPMENT_PLAN.md`](docs/DEVELOPMENT_PLAN.md) — phased plan, each phase with an explicit exit gate
- [`docs/SAFETY_AND_VALIDATION.md`](docs/SAFETY_AND_VALIDATION.md) — what must be proven before anything is promoted
- [`docs/DECISIONS.md`](docs/DECISIONS.md) — decision log with reasons
- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — proposed architecture

The first phase is deliberately research, not code: build a controlled,
rights-cleared set of interchange files and prove one reproducible
AAF/FCPXML → Nuendo handoff before committing to implementation.

---


JARVIS Nuendo Project Builder is a planned local macOS application that prepares
film and television sound-editing sessions for Nuendo. It combines an editorial
timeline, reference video and production audio, resolves timecode, proposes
synchronization, and exports a reviewable Nuendo-ready package.

The application is a sibling of **DAW Project Cleaner**, not a replacement for
it. Project Builder creates and documents new sessions; Cleaner audits and
eventually cleans archived sessions. The projects may share safe filesystem,
hashing, progress, reporting and packaging components, but they must keep
separate product logic and release gates.

## Product promise

The operator supplies:

- an AAF or FCPXML/FCPXMLD editorial export;
- one constant-frame-rate reference movie;
- one or more production-audio folders;
- a Studio89 Nuendo template and delivery preset.

The application produces:

- a normalized and validated picture timeline;
- proposed production-audio matches with confidence and conflict information;
- a visual review step before export;
- an AAF and organized media package for Nuendo 15;
- a machine-readable provenance manifest and human-readable preflight report;
- no modification of the source editorial project or source recordings.

## First-release boundary

Version 1 will **not** write a native `.npr` file directly. Nuendo's binary
project format is private and version-specific. The safe initial workflow is a
validated AAF imported into a known Nuendo template at absolute timecode.

Version 1 will also not promise automatic success when timecode is absent,
contradictory or discontinuous. It will stop, explain the uncertainty and let
the operator approve or correct the match.

## Documentation

- [Product brief](docs/PRODUCT_BRIEF.md)
- [Technical architecture](docs/ARCHITECTURE.md)
- [Development plan](docs/DEVELOPMENT_PLAN.md)
- [Safety and validation](docs/SAFETY_AND_VALIDATION.md)
- [Architecture decisions](docs/DECISIONS.md)

## Current status

Planning started on 2026-08-09. No production code or supported build exists
yet. The immediate next deliverable is the Phase 0 controlled interchange
corpus: pinned application versions, rights-cleared fixtures, equivalent
editorial exports and a documented AAF implementation decision. The read-only
preflight prototype starts only after that gate passes.
