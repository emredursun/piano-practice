# ADR-011: Repertoire (Phase 1) — Bach Inventions, Bach-Gesellschaft 1853 PD edition

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §7.1 (Phase 1 corpus), §5.10 (repertoire copyright), §2.4 (Phase 1 scope)

## Context

The v1.0 plan's 25-level curriculum carried a €100-150k realistic licensing/authoring cost (audit PK-1). To validate the product thesis without incurring that cost, Phase 1 must ship a **defensible single-composer corpus** from confirmed public-domain sources — pedagogically substantial, not a token selection.

## Decision

**Phase 1 ships the 15 Bach Two-Part Inventions (BWV 772–786) engraved from the Bach-Gesellschaft Ausgabe (1853) public-domain source.**

- **Source verification**: Bach-Gesellschaft 1853 is confirmed PD globally (composer d. 1750; editor d. before any currently-active term).
- **Engraving**: re-engraved in MuseScore 4 (open format) → exported to MusicXML 4.0.
- **Editorial fingering**: Czerny (1840) and Busoni (1894), both PD, bundled as toggleable presets.
- **AI fingering**: `pianoplayer` (Parncutt 1997 DP) at build-time — see ADR-005.
- **Reference recording**: owner's own performance in Phase-1 Weeks 8-10, released CC-BY-NC.
- **Difficulty rating**: ABRSM Grade 5-7 equivalent, documented per-piece in `corpus/metadata.json`.
- **Every piece**: row in `docs/legal/REPERTOIRE-PROVENANCE.md` with PD status per jurisdiction.

## Consequences

- **Positive:**
  - Zero external licensing cost; entirely founder-hours.
  - Pedagogically coherent: inventions teach two-voice independence, a universal piano skill gate.
  - Competitive posture: comparable to single-composer Henle-style apps at a fraction of the cost.
  - Opens up a marketing story ("serious practice tool for Bach two-part inventions — your first Bach-level goal, fully realised").
- **Negative / trade-offs:**
  - Narrow corpus. Some users will want more. That is G2's test: does narrow-but-excellent convert?
  - Risk R-07: Bach-corpus differentiation insufficient. Mitigation: pedagogical depth (fingering reasoning, hand-separation analysis) rather than quantity.
- **Neutral:**
  - Phase 2 expansion adds Goldberg Aria + variations, Czerny Op. 599, Burgmüller Op. 100, Heller Op. 47, selected Mendelssohn (§7.2) — all PD.

## Alternatives Considered

1. **Full 25-level curriculum at v1.0** — rejected by audit (PK-1 cost), Master Plan §2.2.
2. **Licensed curriculum (Faber, Hoffman, RCM)** — rejected at Phase 1 on cost and lock-in; re-considered at Phase 3.
3. **Mixed composer showcase (10 pieces across 10 composers)** — rejected: dilutes the "defensible depth" positioning; harder to validate the thesis.
4. **Chopin/Debussy showcase** — rejected: harder entry level, smaller beginner/amateur TAM; audit noted Bach's universality.

## References

- `MASTER-PLAN.md §3` (ADR-011)
- `MASTER-PLAN.md §7.1` (Phase-1 corpus detail)
- `MASTER-PLAN.md §5.10` (repertoire copyright and provenance)
- `MASTER-PLAN.md §10.1` (R-07 risk)
