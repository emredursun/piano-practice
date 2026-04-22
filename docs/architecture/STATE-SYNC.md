# State Synchronisation — Piano-Practice

> **Scope:** Strategy across phases. Phase 1 is explicitly "no sync"; Phase 2 introduces server-authoritative mirrors; Phase 3 CRDTs.
> **Authority:** `MASTER-PLAN.md §6.3`, ADR-013, ADR-014.

---

## 1. Phase 1 — Device-Local Only

### 1.1 State locations

- **UI (ephemeral):** Zustand stores, in-memory.
- **Persistent local:** Dexie (IndexedDB).
- **License:** localStorage (Ed25519 token) + Cloudflare KV revocation list (authoritative for revocation).
- **Samples:** Cache Storage API.

### 1.2 Sync semantics

- **No network sync.** Zero out-of-browser state for practice history.
- **Portability** via manual JSON export/import (see [`SCHEMA.md §1`](SCHEMA.md)).
- **Revocation** is the only server-authoritative bit; client polls KV on app boot when online.

### 1.3 Multi-device story

- User opens app on a second device → re-uses the license URL from email → unlocks.
- Practice history **does not follow**. User can export on device A, import on device B (manual).

This closes audit CRIT-A3 by phasing — no CRDT commitment until demand validates it.

---

## 2. Phase 2 — Server-Authoritative Mirror

### 2.1 Model

- **License / billing:** server-authoritative (Supabase).
- **Practice history:** client-authoritative; periodic append-only upload.
- **Settings:** server-authoritative (server row updated on change).
- **Fingering overrides:** server-authoritative.

### 2.2 Append-only event log for practice sessions

```ts
type SessionEvent = {
  id: string;        // uuid v4
  pieceId: string;
  startedAt: string; // ISO
  endedAt: string;
  notesCorrect: number;
  notesTotal: number;
  barsAccuracy: number[];
  rating?: "again" | "hard" | "good" | "easy";
  createdAt: string; // Client clock; server trusts nothing
};
```

Upload flow:
1. Session completes → record in Dexie.
2. Queue an upload task; exponential backoff on failure.
3. Worker endpoint `POST /sessions` accepts batch; server generates its own `server_created_at`.
4. Duplicate events rejected by `id` unique constraint.

### 2.3 Conflict model

- **Settings:** last-write-wins per field (server clock).
- **Sessions:** append-only; no conflict possible (each session has unique `id`).
- **Fingering overrides:** keyed by `(user_id, piece_id, note_index)`; LWW by `updated_at`.

### 2.4 Read path

- On app boot when online: server returns deltas since `last_sync_at`.
- Dexie is updated; UI reacts via Zustand subscriptions.

---

## 3. Phase 3 — Yjs CRDT for Collaboration-Like State

### 3.1 Scope

- **Per-piece annotations** (notes, colours) — `Y.Doc` per piece.
- **Per-piece fingering overrides** migrated from LWW to CRDT map for conflict-free multi-device edits.
- **Loop markers** — CRDT array.
- **Settings remain LWW**; no collaboration need.
- **License / billing remain server-authoritative**; no CRDT.

### 3.2 Transport

- **Supabase Realtime** channels per `Y.Doc`.
- `y-supabase-provider` or equivalent (library to be evaluated at Phase-3 kickoff).
- Binary updates; smaller than JSON diffing.

### 3.3 Persistence

- Snapshot periodically; GC history older than N days.
- Snapshots stored in Supabase; users can download their own `Y.Doc` binary.

### 3.4 Offline

- Yjs operates offline; on reconnect, merge converges.
- No conflict UI is needed — Yjs semantics guarantee convergence.

---

## 4. Export / Import Across Phases

### 4.1 Phase 1

- Manual JSON export/import via Settings.
- Scheme: `ExportV1` in [`API-CONTRACT.md §4.2`](API-CONTRACT.md).

### 4.2 Phase 2

- Manual JSON export continues to include local-only fields.
- In-app "transfer to another device": server-authoritative history is already available via login.

### 4.3 Phase 3

- Export includes Yjs `Y.Doc` binary per piece (for long-term portability / archival).

---

## 5. Failure Modes & Recovery

| Failure | Phase 1 | Phase 2 | Phase 3 |
|---|---|---|---|
| Server outage | n/a (no server) | Write queue; client keeps working | Same; CRDT merges on recovery |
| Client data corruption | Export/import only way | Reload from server | Reload from server + replay CRDT |
| Client clock skew | n/a | Server rewrites timestamps | CRDT uses logical clocks |
| Schema upgrade in flight | Forward-only migration | DB migration forward+down | Same + `Y.Doc` snapshot version |

---

## 6. Trade-offs & Rationale

- **Phase 1 no-sync** is a deliberate scope reduction to validate willingness-to-pay before investing in sync.
- **Phase 2 append-only event log** is the simplest durable mirror that avoids conflict semantics; suits practice history well (sessions are inherently append-only events).
- **Phase 3 Yjs** is adopted because collaboration-adjacent state (annotations, overrides) benefits from CRDT convergence without a conflict UI.

---

## 7. References

- `MASTER-PLAN.md §6.3`
- ADR-013, ADR-014
- [`SCHEMA.md`](SCHEMA.md)
- [`API-CONTRACT.md`](API-CONTRACT.md)
- Yjs docs, Supabase Realtime docs
