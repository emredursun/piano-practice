# ADR-009: Backend (Phase 2+) — Supabase EU region

- **Status:** Accepted (deferred to Phase 2)
- **Date:** 2026-04-17
- **Phase:** 2, 3
- **Master Plan reference:** §3, §5.6 (RLS), §5.7 (auth hardening), §6.1 (data stores)

## Context

Phase 1 has no backend for user data (license issuance runs on a Cloudflare Worker writing to KV; no accounts). Phase 2 introduces user accounts to prep for subscription and multi-device sync. The backend needs: Postgres (relational integrity), auth with MFA, Realtime (future CRDT sync), and EU data residency (GDPR + KVKK cross-border posture).

## Decision

**Use Supabase, EU region, as the Phase 2+ backend. RLS is mandatory from day one of the backend's existence. pgTAP-based RLS test suite gates CI.**

- Three schemas: `public` (RLS, app via anon-key), `private` (service-role only), `audit` (append-only, service-role write, no app read).
- Service-role key lives **only** in Cloudflare Workers — never in the browser bundle, never in CI logs.
- `pg_audit` enabled for query-level auditing.
- Forward+down migrations via Supabase CLI, never destructive.
- Weekly off-vendor backup of `audit.events` to AWS S3 Object Lock (7-year retention).

## Consequences

- **Positive:**
  - Postgres + Auth + Realtime in a single platform; lower ops burden for solo founder at Phase 2.
  - RLS + pgTAP gives a declarative, testable security boundary — directly closes audit findings on schema/RLS.
  - EU region aligns with geo scope (ADR-018) and simplifies DPIA conversations.
- **Negative / trade-offs:**
  - Vendor lock-in. Mitigated by Postgres portability + off-vendor audit-log backup.
  - RLS-by-default is strict; requires discipline. CI lint enforces "no table without RLS".
  - Supabase Realtime has known performance edges at scale; Phase-3 CRDT sync (ADR-014) will load-test this before committing.
- **Neutral:**
  - Supabase free tier covers Phase-2 validation; Pro tier at Phase-3 scale.

## Alternatives Considered

1. **Self-hosted Postgres + custom auth** — rejected: solo-founder ops burden unacceptable.
2. **Firebase** — rejected: GDPR / EU residency concerns; non-Postgres data model a poor fit.
3. **PlanetScale (MySQL)** — rejected: no RLS equivalent; no integrated auth.
4. **Neon + custom auth** — rejected: missing auth + realtime; two additional vendors.

## References

- `MASTER-PLAN.md §3` (ADR-009)
- `MASTER-PLAN.md §5.6` (Supabase RLS)
- `MASTER-PLAN.md §5.7` (auth hardening)
- Supabase docs; pgTAP
