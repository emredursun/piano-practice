# ADR-006: State — Zustand (UI) + Dexie (IndexedDB persistence)

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §6.2 (Dexie schema versioning), §6.3 (sync model)

## Context

Phase 1 is device-local only (ADR-013). We need:
- A **predictable UI state store** for lanes/modes/settings/transient practice state.
- A **robust persistence layer** for practice history, settings, sample cache, with forward-only migration and safe JSON export/import (GDPR Art. 20).

Both need to be SSR-irrelevant (we are a pure SPA).

## Decision

**Use Zustand for in-memory UI state and Dexie (IndexedDB wrapper) for persistent storage.**

- Zustand stores are organized per-domain (`useAudioStore`, `useSessionStore`, `useSettingsStore`), each < ~150 LOC.
- Dexie `db.version(1).stores({...})` schema locked at v1; new versions append, never drop. Version bumps always include an upgrade function with rollback guidance (§6.2).
- JSON export/import implemented from Phase 1 launch (portability + DR).
- No global "redux-style" mega-store.

## Consequences

- **Positive:**
  - Minimal API — no `Provider` wrapping, no action-type boilerplate, fast for solo development.
  - Zustand's `subscribeWithSelector` allows fine-grained subscriptions, avoiding re-render cascades on hot audio-update paths.
  - Dexie handles IndexedDB's rough edges (transactions, upgrade events, TypeScript typing).
  - Forward-only migration protects against production-data loss as the schema evolves through Phases 2 and 3.
- **Negative / trade-offs:**
  - Zustand's simplicity can foster ad-hoc patterns; `docs/CONTRIBUTING.md` references a store-structure convention to resist this.
  - IndexedDB is browser-local; quota is browser-governed. Users with small-device quota pressure could hit limits; export-to-JSON is the escape valve.
- **Neutral:**
  - At Phase 2, server-authoritative licensing moves `public.licenses` server-side; Dexie stays the practice-history home until Phase 3 CRDT sync.

## Alternatives Considered

1. **Redux Toolkit** — rejected: more ceremony than needed at Phase-1 scope; higher learning curve for AI agents generating code.
2. **Jotai / Valtio** — rejected: similar capability to Zustand; Zustand's larger ecosystem and docs tip the balance.
3. **LocalStorage only** — rejected: no indexed queries, 5-10 MB quota, synchronous blocking. Unsuitable for practice history.
4. **Raw IndexedDB** — rejected: API ergonomics poor; high risk of migration bugs in long-lived app.
5. **SQLite-wasm** — rejected: larger footprint; IndexedDB is sufficient for Phase-1 query patterns.

## References

- `MASTER-PLAN.md §3` (ADR-006)
- `MASTER-PLAN.md §6.2` (Dexie schema versioning, closes CRIT-A3)
- Zustand, Dexie docs
