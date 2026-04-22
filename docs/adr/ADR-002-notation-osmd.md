# ADR-002: Notation — OpenSheetMusicDisplay (OSMD)

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §1.1 (real engraved notation), §4.3 (render budget)

## Context

The product thesis hinges on **real engraved notation** — not stylized cartoons. This requires a renderer that consumes MusicXML (industry interchange format for scored music), renders musician-grade typography (Bach Inventions demand it), and exposes hooks we can decorate for practice features (highlight, loop bracket, fingering overlay, score-follower cursor).

## Decision

**Use OpenSheetMusicDisplay (OSMD)** as the notation renderer. Input format: MusicXML 4.0. Output: SVG.

## Consequences

- **Positive:**
  - BSD-3 licensed, permissive for commercial use.
  - Consumes standard MusicXML → our Phase-1 corpus (Bach-Gesellschaft 1853, re-engraved in MuseScore 4) flows natively.
  - Production-grade output quality; used in MuseScore-adjacent tooling.
  - Exposes internal graphical objects; we can attach our own overlays (fingering, score-follower cursor, loop marker) without forking.
  - Actively maintained; responsive to bug reports.
- **Negative / trade-offs:**
  - SVG renders of large scores can be slow on low-end mobile. Phase-1 mitigation: all 15 Bach Inventions are ≤ 200 bars; pagination is a Phase 2 concern (§4.3 budget: notation render p95 ≤ 800 ms for ≤ 200-bar piece). Tracked as R-08.
  - OSMD's internal model is opinionated; customizing advanced layout is possible but non-trivial.
- **Neutral:**
  - We bundle MusicXML at build-time in Phase 1 (no user upload surface), which keeps parser-attack concerns dormant until Phase 2 upload pipeline.

## Alternatives Considered

1. **Verovio** — rejected: MEI-first, requires MusicXML→MEI conversion step; smaller JS ecosystem; less proven for interactive overlays.
2. **VexFlow** — rejected: low-level drawing primitives; we would have to rebuild much of what OSMD already provides (layouts, ties, beaming rules).
3. **Roll-your-own (Canvas)** — rejected: engineering cost enormous; cannot credibly compete with OSMD's typography.

## References

- `MASTER-PLAN.md §3` (ADR-002)
- `MASTER-PLAN.md §4.3` (notation render p95 budget)
- OSMD GitHub; MusicXML 4.0 spec
