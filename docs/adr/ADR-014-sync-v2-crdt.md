# ADR-014: Sync (v2.0+) — Yjs CRDT over Supabase Realtime

- **Status:** Accepted (deferred to Phase 3)
- **Date:** 2026-04-17
- **Phase:** 3
- **Master Plan reference:** §3, §6.3 (sync model), §2.6 (Phase 3)

## Context

Phase 3 re-opens multi-device sync conditional on G2/G3 passing. By that point, account infrastructure (ADR-009) exists, pricing is subscription-capable, and user appetite for cross-device continuity is validated by real demand signals. We must pick a conflict-free design upfront so Phase 3 isn't a second re-architecture.

## Decision

**Phase 3 sync uses Yjs CRDTs, transported over Supabase Realtime channels, with per-piece `Y.Doc` scoping and last-writer-wins for settings.**

- Per-piece `Y.Doc`: practice annotations, custom fingerings, loop markers. Each document small, independently syncable.
- Settings: LWW (last-writer-wins on user-facing settings — acceptable because individual-user state).
- License/billing: **not** CRDT — server-authoritative via Supabase (trust boundary matters for entitlement).
- Offline edits merge on reconnect; Yjs semantics guarantee convergence.

## Consequences

- **Positive:**
  - Yjs is battle-tested, mature, well-documented.
  - Per-piece `Y.Doc` keeps documents small; memory use and sync cost bounded.
  - Supabase Realtime reuses existing backend; no new infrastructure.
  - Conflict-free by design; user never sees a merge conflict UI.
- **Negative / trade-offs:**
  - Yjs document size grows over time (history). Periodic snapshot + GC required.
  - Supabase Realtime throughput at scale is untested for our pattern; Phase-3 plan includes a load test.
- **Neutral:**
  - Users who don't upgrade to Phase-3 plan continue to use Phase-2 server-authoritative model.

## Alternatives Considered

1. **Automerge** — rejected: slightly smaller ecosystem for browser+realtime pattern; Yjs has better docs.
2. **Server-authoritative OT (operational transform)** — rejected: conflict model more complex; CRDT is the modern default.
3. **Differential sync / event sourcing (no CRDT)** — rejected: conflict UX forced to the user.
4. **Firebase RTDB / Firestore** — rejected: Supabase already in the stack; adding a second backend for sync is ops overhead.

## References

- `MASTER-PLAN.md §3` (ADR-014)
- `MASTER-PLAN.md §6.3` (sync phasing)
- `MASTER-PLAN.md §2.6` (Phase 3)
- Yjs docs; Supabase Realtime docs
