# ADR-005: Fingering — pianoplayer (Parncutt 1997 DP) at build-time

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §5.10 (repertoire copyright), §2.4 (AI fingering corpus)

## Context

Phase 1 advertises "AI fingering suggestions trained on confirmed public-domain corpus only" (§1.1). Two risks converged:

1. **Copyright risk:** training fingering models on Henle/ABRSM editions would be infringing. The corpus must be PD or openly licensed.
2. **Runtime ML risk:** a per-user ML model at runtime triggers EU AI Act obligations, DPIA, and explainability concerns (CRIT-S4 adjacent). A deterministic algorithm, pre-computed at build-time, avoids all of this in Phase 1.

The **Parncutt (1997) dynamic-programming algorithm** for piano fingering is a well-documented, deterministic academic algorithm — confirmed PD.

## Decision

**Use `pianoplayer` (an open-source implementation of Parncutt 1997) at build-time to pre-compute fingering manifests for the bundled corpus. Store manifests per-piece alongside the MusicXML. No runtime ML in Phase 1.**

- Build step produces `corpus/fingering/{piece_id}.json` with per-note finger assignments.
- Editorial fingering (Czerny 1840, Busoni 1894 — both PD) bundled as toggleable presets alongside the algorithmic suggestion.
- User can override any fingering; overrides stored in IndexedDB.
- Future personalised fingering is Phase 3+ and gated by a DPIA.

## Consequences

- **Positive:**
  - Zero runtime ML → zero AI Act / DPIA burden in Phase 1.
  - Deterministic and explainable ("this finger chosen to minimise total hand span over the next 4 beats").
  - Corpus provenance is clean — the algorithm and the training data are both PD.
  - Manifests are small (~KB per piece), cacheable, and versioned with the corpus.
- **Negative / trade-offs:**
  - Parncutt 1997 has known limitations (chord transitions, polyphonic voices in single hand). For the Phase-1 corpus (two-voice Inventions), this is well within its domain.
  - Building a ground-truth QA dataset to verify suggestions is a manual cost (mitigated: founder + any teacher reviewer validates sample pieces).
- **Neutral:**
  - Overrides are first-class in the UI — "our" algorithmic suggestion is always user-overridable.

## Alternatives Considered

1. **Runtime ML fingering (e.g. PiG-RL, RoboPianist)** — rejected for Phase 1: DPIA required; data-residency and training-data provenance unresolved; no product-critical benefit at Phase-1 corpus size.
2. **Editorial-only (Czerny/Busoni exclusively)** — rejected: less impressive product positioning; the "AI suggestion" layer is part of the value proposition.
3. **Manual fingering by contractor** — rejected: non-trivial cost per piece; difficult to scale beyond the 15-piece Phase-1 corpus to Phase-2 expansion.

## References

- `MASTER-PLAN.md §3` (ADR-005)
- `MASTER-PLAN.md §2.4` (AI fingering on PD corpus)
- `MASTER-PLAN.md §5.10` (repertoire copyright and provenance)
- Parncutt, R. (1997). "Modeling piano performance: Physics and cognition of a virtual pianist." Journal of New Music Research.
