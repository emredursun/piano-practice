# Disaster Recovery & Business Continuity — Piano-Practice

> **Authority:** `MASTER-PLAN.md §4.4` (DR & BC).
> **RTO** = Recovery Time Objective. **RPO** = Recovery Point Objective.

---

## 1. Scenario Matrix

| Scenario | Detection | Mitigation | RTO | RPO |
|---|---|---|---|---|
| **Cloudflare Pages outage** | Sentry uptime + status.cloudflare.com | Phase 1: degraded (static only). Phase 2+: AWS S3 secondary + Route53 DNS failover | 1 h | 0 |
| **R2 outage (samples)** | RUM `sample.load.failure` rate alert | Samples cached in IndexedDB after first load; degraded but functional | n/a | 0 |
| **Supabase region outage (Phase 2+)** | Sentry + supabase status | Read-only mode with IndexedDB cache; banner notice; operations suspended | 4 h | 1 h |
| **Stripe outage** | Stripe webhook failure alert | Queue checkout intents; retry hourly; communicate via email | 8 h | 0 |
| **KV corruption / loss (license store)** | Audit mismatch with Stripe events | Replay Stripe event history → reconstruct KV | 4 h | 0 |
| **Owner unavailable** | n/a | Documented runbooks + designated technical executor (legal) | 72 h | 0 |
| **Sentry / Honeycomb outage** | self-observed via backup RUM | Fall back to Cloudflare Web Analytics + Worker logs | 4 h | 0 |
| **Domain expiry** | Prepaid 5 years + calendar reminder | Renew immediately; registrar auto-renew on | 1 h | 0 |

---

## 2. Backup Strategy

- **Supabase (Phase 2+):** Pro-tier daily backups (retained 30 days).
- **Off-vendor audit log backup (Phase 2+):** weekly AWS S3 Object Lock (7-year retention, CMK-encrypted).
- **Phase 1 KV:** no formal backup — KV is derivable from Stripe events (source of truth).
- **Code:** GitHub is canonical; main branch protected + signed commits (Phase 2+).
- **Secrets:** Cloudflare Workers Secrets; rotated quarterly; recovery via founder credentials (vault + YubiKey + paper-backup per `docs/operations/SECRETS.md`).

---

## 3. Recovery Procedures

### 3.1 Cloudflare Pages instant rollback

- Cloudflare dashboard → Pages → Deployments → "Rollback" on previous known-good.
- RTO ≤ 30 s.
- Post-rollback: open an incident, keep the broken commit on a branch for forensics.

### 3.2 Worker rollback

- `wrangler rollback` to previous version.
- Verify webhook continues to fire.
- Recover missed webhooks via Stripe dashboard "Resend".

### 3.3 Database migrations (Phase 2+)

- Every migration has a forward (`up`) and a reversible (`down`).
- Destructive migrations forbidden by policy; any ALTER that drops data must be staged.
- Supabase CLI `db push` in CI; `db diff` in PRs.

### 3.4 KV reconstruction (Phase 1 scenario)

If the license KV is lost or corrupted:

1. Trip `killswitch.stripeCheckout`.
2. From the Stripe dashboard: export all `checkout.session.completed` events (past 180 days at minimum).
3. Re-run the Worker issuance path offline, reconstructing `stripe_event_id → license_token` pairs.
4. Write results back to KV.
5. Verify random-sample licenses unlock successfully.
6. Un-trip checkout.

Validated annually via tabletop.

---

## 4. Communication During Disaster

- Status page updated within **1 h** for SEV-1.
- Email blast for **outages > 1 h** affecting paying customers.
- Post-mortem published publicly (anonymised) within **7 working days** for any SEV-1.

---

## 5. Business Continuity (solo founder)

### 5.1 Bus factor

Mitigated (not eliminated) by:

- **Designated technical executor**: named in `docs/operations/EXECUTOR.md` (sealed envelope in physical safe + digital copy in encrypted vault). Credentials scoped to "pause ads + take down checkout + communicate to customers" only.
- **Runbooks**: IR-PLAN + DR + SECRETS + RUNBOOKS.
- **Insurance**: professional liability policy reviewed at Phase-1 launch.

### 5.2 Recovery from founder incapacity

1. Executor opens sealed instructions.
2. Trips `killswitch.stripeCheckout`.
3. Sends pre-drafted email to all customers explaining pause.
4. Legal counsel (named in `LEGAL-CONTACTS.md`) handles refunds if founder cannot return within 30 days.

---

## 6. DR Testing Cadence

- **Phase 1:** one DR test before launch (Week 14) — simulate Cloudflare Pages outage + rollback.
- **Phase 2+:** quarterly (rotating scenario).
- Log results in `docs/operations/dr-tests/YYYY-MM-DD-<scenario>.md`.

---

## 7. Out of Scope (Phase 1)

- Multi-region active-active.
- Sub-15-minute RTO for non-critical services.
- Paid third-party incident-management platform (PagerDuty-class).
- Dedicated DR region on AWS.

These are Phase 3+ considerations, contingent on revenue.

---

## 8. References

- `MASTER-PLAN.md §4.4`
- [`IR-PLAN.md`](IR-PLAN.md)
- [`SLO.md`](SLO.md)
- ADR-008 (Cloudflare), ADR-009 (Supabase Phase 2+)
