# ADR-013: Sync (v1.0) — device-local only, JSON export/import

- **Status:** Accepted
- **Date:** 2026-04-17
- **Phase:** 1
- **Master Plan reference:** §3, §6.3 (sync model), §2.4 (scope), closes audit CRIT-A3

## Context

Audit finding CRIT-A3 flagged "sync ambiguity": the v1.0 plan implied multi-device without committing to a design. Committing to CRDT sync in Phase 1 would add significant complexity (Yjs integration, conflict-resolution UX, Realtime channels) to a phase whose primary job is to validate willingness-to-pay. A phased commitment resolves the ambiguity without over-committing.

## Decision

**Phase 1 is device-local only.** Practice history, settings, and sample cache all live in Dexie / IndexedDB on the single device where the app is installed. JSON export/import is implemented from launch to provide portability (GDPR Art. 20) and DR (user-controlled backup).

- No server-side sync.
- No multi-device "handoff" UX.
- License key is the only cross-device state; user re-uses the license URL on another device to install the app there.
- Export JSON includes the full Dexie state except samples (which can be re-downloaded).

## Consequences

- **Positive:**
  - Eliminates an entire class of architectural risk in Phase 1 (sync, conflict, merge UX, server-storage costs).
  - Audit CRIT-A3 closed by explicit phasing, not hand-waving.
  - GDPR Art. 20 portability satisfied natively via export.
  - Accelerates Phase 1 delivery; resources redirect to notation/audio/MIDI polish where the product thesis is tested.
- **Negative / trade-offs:**
  - Users with multiple practice devices must re-enter the license URL per device and will have separate practice histories.
  - A user who wipes a device loses history unless they exported first. Export UX must be obvious.
- **Neutral:**
  - Phase 2 introduces server-authoritative license + append-only event log for practice history.
  - Phase 3 introduces true CRDT multi-device sync (ADR-014).

## Alternatives Considered

1. **Cloud sync from day 1** — rejected: Phase-1 scope inflation; forces Supabase adoption earlier than validated.
2. **Browser-native sync (e.g. Chrome profile sync)** — rejected: not portable across browsers; unreliable.
3. **Email-based "export+restore" ritual only, no in-app export** — rejected: terrible UX.

## References

- `MASTER-PLAN.md §3` (ADR-013)
- `MASTER-PLAN.md §6.3` (sync model phasing, closes CRIT-A3)
- `MASTER-PLAN.md §2.4` (Phase 1 scope)
- ADR-014 (future Phase 3 sync)
