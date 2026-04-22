# Security — Piano-Practice

> **Derived from:** [MASTER-PLAN.md](MASTER-PLAN.md) §5 (Security & Privacy by Design) and §10 (Compliance).
> **Status:** Summary + operational pointers. Full artefacts live under `docs/security/` (authored Weeks 4–12 of Phase 1).
> **Contact:** `security@piano-practice.<tld>` (published at launch via `/.well-known/security.txt`).

---

## 1. Security Posture by Phase

| Area | Phase 1 (MVP) | Phase 2 | Phase 3 |
|---|---|---|---|
| Auth | None (license key only, ADR-019) | Supabase Auth, MFA optional | MFA forced for subscription |
| Data storage | Device-local (Dexie) + KV | Supabase Postgres + RLS | Same + Vault for sensitive cols |
| Uploads | **NONE** (bundled corpus only) | Enabled with full pipeline (§5.3) | Same |
| Sync | None (device-local) | Server-authoritative + append-only events | Yjs CRDT |
| Children | Hard 16+ gate (ADR-017) | Same | Re-evaluated at v3.0 only |
| Geo | TR + EU only (ADR-018) | Same | US/BR re-evaluated |
| Compliance | KVKK + GDPR + ePrivacy + EAA 2025 + SAQ-A | + DPIA for AI | + EU AI Act + SOC 2 readiness |

Phase 1 deliberately minimises attack surface: **no auth, no uploads, no backend for user data, no children, no high-risk geos**. This is by design — see `MASTER-PLAN.md §2.4` ("Why this scope").

---

## 2. Threat Model

- **Framework:** STRIDE, per-asset, with abuser stories.
- **Workshop:** scheduled Week 4 of Phase 1 (before any production code). Output lives in `docs/security/THREAT-MODEL.md`.
- **Tooling:** OWASP Threat Dragon.

Phase-1 asset map (summary):

| Asset | Trust Boundary | Primary Threats |
|---|---|---|
| Static app (Cloudflare Pages) | Public CDN | Tampering (subresource), DoS |
| Salamander samples (R2) | Public CDN + signed URLs | Tampering, exfiltration cost |
| MusicXML/MIDI bundle (in app) | Same-origin | N/A in Phase 1 (bundled only); XXE / zip-bomb from user uploads enabled Phase 2+ |
| License keys (Stripe webhook) | Webhook + email | Forgery, replay, brute-force enumeration |
| Stripe webhook endpoint (Worker) | Public HTTPS | Spoofing, replay, race |
| Browser IndexedDB | User-controlled | XSS exfiltration, prototype pollution |

Full detail: `MASTER-PLAN.md §5.1`.

---

## 3. Content Security Policy

Baseline defined in `MASTER-PLAN.md §5.2`. Cloudflare Pages `_headers`, enforced at `/*`.

Key directives (Phase 1):

- `default-src 'none'`
- `script-src 'self' 'wasm-unsafe-eval' https://js.stripe.com`
- `connect-src 'self' https://*.r2.cloudflarestorage.com https://api.stripe.com https://*.ingest.sentry.io https://api.honeycomb.io`
- `frame-src https://js.stripe.com https://hooks.stripe.com`
- `frame-ancestors 'none'`
- `require-trusted-types-for 'script'`

**Rollout:** Report-Only for Weeks 12–14, then enforce at Phase-1 launch. See `docs/security/CSP.md` for the full policy and violation-handling runbook.

Additional headers:

- `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`
- `X-Content-Type-Options: nosniff`
- `Referrer-Policy: strict-origin-when-cross-origin`
- `Permissions-Policy: midi=(self), microphone=(), camera=(), geolocation=(), payment=(self "https://js.stripe.com"), interest-cohort=()`

---

## 4. File Upload Pipeline

**Phase 1: NOT APPLICABLE.** The app bundles only PD-confirmed corpus. No upload endpoint exists; no parser runs on untrusted input. The upload surface is zero.

**Phase 2 reintroduction** (`docs/security/UPLOAD-PIPELINE.md`, authored at Phase 1 end):

- Hard size limits: MIDI 1 MB, MusicXML 5 MB, MXL 10 MB
- Magic-byte sniff (never trust filename extension)
- MusicXML parsed with `processEntities: false`, `DOCTYPE` rejected, validated against MusicXML 4.0 XSD, allowlist of elements
- MXL stream-decompressed with 100× ratio cap
- `DOMPurify` sanitisation of all user-visible string fields (`<work-title>`, `<credit>`, `<creator>`, `<lyric>`, `<words>`)
- PDF intentionally **not supported** (audit CRIT-S3 risk too high without server-side sandboxing; re-evaluated Phase 3+)
- Upload quota: 5/day free, 50/day paid

See `MASTER-PLAN.md §5.3`.

---

## 5. Children's Data Policy

**Decision: 16+ hard age gate. No service to under-16 in v1.0 or v2.0.** ADR-017.

- Phase 1: footer disclaimer + age confirmation checkbox in Stripe Checkout terms.
- Phase 2: DOB at signup, reject if < 16 (GDPR Art. 8 highest-bar interpretation; simplifies KVKK by avoiding explicit-consent complications).
- Rate-limit re-attempts per IP/email.
- No COPPA VPC pathway — US users geo-blocked anyway (ADR-018).
- Privacy policy contains: "We do not knowingly collect data from children under 16. If you become aware that a child has provided us personal data, contact us for deletion."

Re-opens for review at v3.0 only, with proper VPC infrastructure and business case.

---

## 6. Stripe Integration Security

See `MASTER-PLAN.md §5.5`.

- **Checkout (hosted) only** — SAQ-A scope. Elements **never** used. Keeps PCI scope at SAQ-A.
- **Webhook verification**: `stripe.webhooks.constructEvent()` with timing-safe HMAC, raw body, 5-minute replay window.
- **Idempotency**: handler upserts by `stripe_event_id` (KV in Phase 1 / Postgres unique constraint in Phase 2).
- **Server-side price lookup** via `STRIPE_PRICE_LIFETIME_TRY` / `_EUR` / `_USD` env vars; **never trust client** for price.
- **3DS forced in EU** via Stripe Radar rules.
- **Refund SOP** documented; refunds trigger license-key revocation in KV.
- **License key**: Ed25519-signed JWT-shaped token. Payload = `{email_hash, sku, issued_at, version}`. Verified client-side at app load.
- **Stripe Tax** enabled for EU VAT compliance from Day 1.

---

## 7. Supabase RLS (Phase 2+)

When auth + database are introduced, the following is mandatory **before any user data lands**:

- `ALTER TABLE ... ENABLE ROW LEVEL SECURITY` on every table — enforced via CI lint that fails build if any table lacks RLS.
- Default-deny policies; explicit `USING (auth.uid() = user_id)` per table.
- Three schemas:
  - `public` (RLS, app-accessible via anon-key)
  - `private` (service-role only)
  - `audit` (append-only, service-role write, no read from app)
- Service-role key **never** in client — only Cloudflare Workers.
- pgTAP RLS test suite in CI (`tests/rls/*.sql`).
- `pg_audit` extension for query-level auditing.

Schema reference: `MASTER-PLAN.md §5.6`.

---

## 8. Auth Hardening (Phase 2+)

- JWT TTL: 15 min. Refresh rotation enabled. Refresh TTL: 7 days paid / 24 h free.
- Rate limits: sign-in 5/min/IP, signup 3/hr/IP.
- HaveIBeenPwned k-anonymity password check on signup.
- MFA (TOTP) forced for any account with active subscription.
- "Sign out everywhere" via `auth.admin.signOut(userId, scope='global')`.
- Cloudflare Turnstile on auth forms.
- Idle session timeout: 30 min free / 4 h paid. Absolute: 24 h.

---

## 9. Secrets & Supply Chain

- **All production secrets in Cloudflare Workers Secrets** (never in repo). Rotated quarterly.
- `gitleaks` pre-commit hook + GitHub Action — non-negotiable.
- **Renovate** auto-PRs for patch updates; manual review for minor/major.
- `npm install --ignore-scripts` policy (postinstall script protection).
- CycloneDX SBOM per release, stored with artefact.
- **Cloudflare account**: hardware MFA (YubiKey × 2, primary + backup), scoped deploy tokens, monthly audit log review.
- **GitHub**: branch protection on `main`, required reviews (Phase 2+), required signed commits.

---

## 10. Incident Response

See `docs/operations/IR-PLAN.md` (authored Week 4).

Severity matrix:

- **SEV-1** (data breach, payment failure ≥ 1 h, total outage) — respond 15 min, status page within 1 h
- **SEV-2** (degraded perf, partial outage) — respond 1 h, fix within 24 h
- **SEV-3** (cosmetic, individual user issue) — respond 24 h
- **SEV-4** (enhancement) — backlog

Breach notifications (GDPR Art. 33 / KVKK Art. 12): templates pre-drafted in `docs/security/IR-TEMPLATES/` (TR + EN).

Tabletop: Week 14 before launch (single in Phase 1); quarterly from Phase 2.

---

## 11. Responsible Disclosure

From Phase-1 launch:

- `/.well-known/security.txt` published with `security@` contact.
- Coordinated disclosure window: **90 days** from first contact.
- PGP key published for encrypted reports.
- Safe harbour: good-faith researchers will not face legal action for findings reported in accordance with this policy.
- Bug bounty: formal programme Phase 3+ (see `MASTER-PLAN.md §5.9`).

Scope:
- **In scope:** `piano-practice.<tld>` app, Cloudflare Worker endpoints, license validation logic.
- **Out of scope:** Stripe, Cloudflare infrastructure, third-party CDNs.

---

## 12. Compliance Roadmap

See `MASTER-PLAN.md §10.2`.

| Framework | Phase 1 | Phase 2 | Phase 3 |
|---|---|---|---|
| GDPR (EU) | Privacy notice + cookie banner + DSR workflow | + DPIA | + Art. 22 explainability |
| KVKK (TR) | Cross-border consent + VERBİS check | Same | Same |
| ePrivacy | Cookie banner w/ equal-prominence reject | Same | Same |
| EAA 2025 | WCAG 2.1 AA + a11y statement | + 3rd-party audit | Same |
| EU AI Act | N/A (no runtime AI) | Model card if AI ships | Full compliance |
| PCI DSS | SAQ-A via Stripe Checkout | Same | Same |
| COPPA / CCPA / LGPD | N/A (geo-deferred) | N/A | Re-evaluate at v3.0 |
| SOC 2 / ISO 27001 | N/A | "Ready" architecture | Optional certification |

---

## 13. Do / Don't Quick Reference

**DO:**
- Run `security-reviewer` agent on every auth/webhook/license/CSP/upload/secrets diff.
- Keep `gitleaks` hook enabled.
- Verify ADR-017, ADR-018, ADR-019 still apply before adding user-facing features.
- Use Cloudflare Workers Secrets for every secret.
- Treat the browser as fully compromised — server-side-verify anything that matters.

**DON'T:**
- Commit a secret, even in a test fixture. Use placeholders.
- Disable a CI security check to "unblock" a PR. Fix the root cause.
- Accept user uploads in Phase 1 under any circumstance.
- Add an under-16 pathway without an ADR amendment **and** legal counsel sign-off.
- Move to a new hosting region without re-running KVKK + GDPR cross-border analysis.
- Use Stripe Elements (only Checkout hosted — preserves SAQ-A).

---

## References

- [MASTER-PLAN.md §5](MASTER-PLAN.md) — full Security & Privacy by Design
- [MASTER-PLAN.md §10](MASTER-PLAN.md) — Compliance & Risk Register
- `docs/adr/ADR-017.md` — Children's data policy
- `docs/adr/ADR-018.md` — Geo scope v1.0
- `docs/security/THREAT-MODEL.md` — STRIDE analysis (authored Week 4)
- `docs/security/CSP.md` — full CSP + violation handling
- `docs/security/UPLOAD-PIPELINE.md` — Phase 2 upload validation
- `docs/operations/IR-PLAN.md` — incident response playbook
