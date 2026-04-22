# Data Schema — Piano-Practice

> **Scope:** Phase 1 Dexie schema + Phase 2 Postgres preview. Phase 3 CRDT structures sketched.
> **Authority:** ADR-006 (state), ADR-009 (Supabase), ADR-013 (sync), `MASTER-PLAN.md §5.6`, §6.

---

## 1. Phase 1 — Dexie (IndexedDB)

Local-only. Forward-only migrations.

```ts
// apps/web/src/data/db.ts
import Dexie, { Table } from "dexie";

export type Session = {
  id: string;              // uuid v4
  pieceId: string;
  startedAt: number;       // epoch ms
  endedAt: number;
  notesCorrect: number;
  notesTotal: number;
  barsAccuracy: number[];  // per-bar % (integer)
  rating?: "again" | "hard" | "good" | "easy";  // for Phase-2 SRS
};

export type PieceMeta = {
  id: string;              // e.g. "bwv772"
  composer: string;
  workNo: string;          // "BWV 772"
  title: string;
  grade: number;           // ABRSM 1–8
  bars: number;
  lengthSec: number;
  lastPractised?: number;  // epoch ms
};

export type Setting = {
  key: string;
  value: unknown;
};

export type SampleMeta = {
  name: string;
  sizeBytes: number;
  cachedAt: number;        // epoch ms
};

export type FingeringOverride = {
  pieceId: string;
  noteIndex: number;
  finger: 1 | 2 | 3 | 4 | 5;
  hand: "L" | "R";
  updatedAt: number;
};

export class PianoPracticeDB extends Dexie {
  sessions!: Table<Session, string>;
  pieces!: Table<PieceMeta, string>;
  settings!: Table<Setting, string>;
  samples!: Table<SampleMeta, string>;
  fingeringOverrides!: Table<FingeringOverride, [string, number]>;

  constructor() {
    super("piano-practice");
    this.version(1).stores({
      sessions: "id, pieceId, startedAt",
      pieces:   "id, composer, grade",
      settings: "key",
      samples:  "name",
      fingeringOverrides: "[pieceId+noteIndex]",
    });
    // Future versions append; never DROP a field or store without an exporter.
  }
}

export const db = new PianoPracticeDB();
```

### Migration policy (§6.2)

- Every `version(N)` adds fields; never drops without an export-then-reimport flow.
- Each new version ships with a Vitest migration test against a fixture DB.
- If a destructive change is ever required, we:
  1. Ship version `N` that makes the field optional.
  2. Ship a release note advising users to export JSON.
  3. Ship version `N+1` that removes the field.

---

## 2. Phase 2 — Postgres (Supabase, EU region)

Three schemas: `public` (RLS, app-accessible), `private` (service-role only), `audit` (append-only).

```sql
-- public schema (RLS enforced on every table)
create table public.users (
  id uuid primary key references auth.users(id) on delete cascade,
  display_name text,
  locale text not null default 'tr',
  created_at timestamptz not null default now()
);

create table public.licenses (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references public.users(id) on delete cascade,
  sku text not null,
  stripe_event_id text not null unique,  -- idempotency
  issued_at timestamptz not null default now(),
  revoked_at timestamptz
);

create table public.practice_sessions (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references public.users(id) on delete cascade,
  piece_id text not null,
  started_at timestamptz not null,
  ended_at timestamptz not null,
  notes_correct int not null check (notes_correct >= 0),
  notes_total int not null check (notes_total >= 0),
  bars_accuracy int[] not null,
  rating text check (rating in ('again','hard','good','easy'))
);

create table public.fingering_overrides (
  user_id uuid not null references public.users(id) on delete cascade,
  piece_id text not null,
  note_index int not null,
  finger smallint not null check (finger between 1 and 5),
  hand char(1) not null check (hand in ('L','R')),
  updated_at timestamptz not null default now(),
  primary key (user_id, piece_id, note_index)
);

-- audit schema (append-only; partitioned monthly)
create schema audit;
create table audit.events (
  id bigserial primary key,
  ts timestamptz not null default now(),
  actor_type text not null check (actor_type in ('user','admin','system')),
  actor_id text,
  action text not null,
  resource text not null,
  metadata_hash text not null,  -- no raw PII
  -- IPv4 /24 (last octet zeroed) + IPv6 /48 (last 80 bits zeroed).
  -- /48 in IPv6 CIDR retains 48 bits (coarser than /64, which retains 64).
  -- /48 identifies the site prefix (household / ISP customer assignment),
  -- not the specific subnet within it. This aligns with Google Analytics +
  -- EDPB anonymisation guidance. Going coarser to /32 loses legitimate
  -- operations signal (geo region); /48 is the calibrated balance.
  ip_truncated text,
  ua_family text                -- first 100 chars
) partition by range (ts);
```

### RLS (excerpt)

```sql
alter table public.users enable row level security;
create policy users_self_read on public.users
  for select using (auth.uid() = id);
create policy users_self_update on public.users
  for update using (auth.uid() = id);

alter table public.licenses enable row level security;
create policy licenses_self_read on public.licenses
  for select using (auth.uid() = user_id);
-- writes only via service-role webhook handler

alter table public.practice_sessions enable row level security;
create policy sessions_self_all on public.practice_sessions
  for all using (auth.uid() = user_id) with check (auth.uid() = user_id);

alter table public.fingering_overrides enable row level security;
create policy fo_self_all on public.fingering_overrides
  for all using (auth.uid() = user_id) with check (auth.uid() = user_id);
```

CI lint fails build if any `public.*` table lacks RLS.

### Indexes

```sql
create index sessions_by_user_time
  on public.practice_sessions (user_id, started_at desc);
create index licenses_by_user
  on public.licenses (user_id, issued_at desc)
  where revoked_at is null;
```

---

## 3. Phase 3 — Yjs CRDT Structures (preview)

Per-piece `Y.Doc`:

```ts
// Each user opens a Y.Doc per piece they annotate
yDoc.getMap("annotations") // { noteIndex -> { color, text } }
yDoc.getMap("fingering")   // { noteIndex -> { finger, hand, updatedAt } }
yDoc.getArray("loopMarkers") // [{ from, to, label }]
```

Settings: LWW map; not CRDT per-field but last-writer-wins is acceptable for single-user settings.

Licence/billing: **not** CRDT — server-authoritative via Supabase.

Document size controlled by monthly snapshot + GC of operational history > N days.

---

## 4. Data Classes

| Class | Contains PII? | Retention |
|---|---|---|
| Dexie sessions | No (local only) | User-controlled (export/delete) |
| Dexie settings | No | same |
| Dexie samples | No | Browser-managed cache |
| Supabase `users` | Yes (email via auth, display_name) | Until account deleted |
| Supabase `practice_sessions` | Pseudonymous via `user_id` | Until account deleted + 30 d |
| Supabase `licenses` | Pseudonymous | 7 years (tax records) |
| `audit.events` | No raw PII | 7 years; Object Lock in S3 backup |

---

## 5. Phase-1 → Phase-2 Migration

When a user on Phase 1 logs into Phase 2 (account creation):

1. Upload their Dexie sessions as an append-only batch (server accepts idempotent POST).
2. Keep Dexie as the write path; Supabase is the durable mirror.
3. License reference maps to a `public.licenses` row keyed by the Ed25519 token they already hold — no re-purchase.

---

## 6. References

- ADR-006, ADR-009, ADR-013, ADR-014
- `MASTER-PLAN.md §5.6`, §6
- [`API-CONTRACT.md`](API-CONTRACT.md)
- [`STATE-SYNC.md`](STATE-SYNC.md)
