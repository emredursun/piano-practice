# Service Level Objectives — Piano-Practice

> **Authority:** `MASTER-PLAN.md §4.1` (observability + SLOs).
> **Scope:** Phase 1 production. Phase 2/3 amendments via ADR.

---

## 1. SLO Set (Phase 1)

| # | SLO | Target | Window | Error budget |
|---|---|---|---|---|
| 1 | Site availability | 99.9 % | 30-day rolling | 43.2 min/month |
| 2 | Page time-to-interactive p95 | ≤ 3.5 s (4G mid-tier mobile) | 7-day rolling | 5 % of sessions |
| 3 | Audio input → output latency p95 | ≤ 30 ms (wired MIDI) | 7-day rolling | 5 % of sessions |
| 4 | Notation render p95 | ≤ 800 ms (≤ 200-bar piece) | 7-day rolling | 5 % of sessions |
| 5 | Stripe Checkout completion success | ≥ 99 % (excl. user abandon) | 30-day rolling | 1 % of attempts |

Sources: SLO #1 — Sentry uptime cron. SLO #2–4 — RUM events to Honeycomb. SLO #5 — Stripe webhook + local Plausible event.

---

## 2. Burn-Rate Alerts

Pager triggers founder SMS (Phase 1):

- **2 %** of monthly error budget consumed in **1 hour** → page.
- **5 %** of monthly error budget consumed in **6 hours** → page.

Non-pager notifications (email):

- 1 %/24 h → log for daily review.

---

## 3. SLO Review Cadence

- **Weekly:** founder reviews dashboard; 5-minute standup-with-self.
- **Monthly:** retrospective; archive error-budget burn; adjust targets if systemically miscalibrated.
- **Quarterly:** publish SLO status in `docs/operations/SLO-REVIEW-YYYY-Qn.md`.

---

## 4. When We Burn the Budget

### 4.1 Freeze (not halt)

When more than 50 % of a monthly budget is consumed:
- No new feature deploys to production until the burn rate returns to sustainable.
- Bug fixes and performance work continue.
- The founder documents root cause + remediation in a short incident record.

### 4.2 Halt (rare)

When budget is fully consumed:
- No non-critical deploys.
- Daily check-in on remediation.
- Next month's budget resumes only after a documented fix.

---

## 5. Calibration Notes

- **Availability 99.9 %** is generous for a Cloudflare Pages static app; we expect ≥ 99.99 %. The looser target absorbs our own errors (bad deploy, CSP misconfigured).
- **Audio latency p95 ≤ 30 ms** is the product-critical SLO — direct tie to pedagogical accuracy.
- **Notation render p95 ≤ 800 ms** — tied to OSMD on ≤ 200-bar pieces; re-evaluated when Phase-2 longer pieces land.

---

## 6. Tooling

- **Sentry** — SLO #1 (uptime cron + release health).
- **Honeycomb** (via OpenTelemetry) — SLO #2, #3, #4 from RUM events.
- **Stripe + Plausible** — SLO #5.

Dashboards are linked from `docs/operations/DASHBOARDS.md` (authored Week 4).

---

## 7. Phase 2/3 Additions (preview)

- API availability & p99 latency (Supabase) — when backend goes live.
- Auth sign-in success rate — when auth ships.
- Subscription billing success rate — Phase 3.
- AI fingering inference latency — Phase 3 (if runtime ML introduced, DPIA-gated).

---

## 8. References

- `MASTER-PLAN.md §4.1`
- [`IR-PLAN.md`](IR-PLAN.md) — severity matrix + pager flow
- [`DR.md`](DR.md) — outage scenarios
- ADR-015 (observability)
