# Threat Model — Piano-Practice

> **Status:** SCAFFOLD. The full STRIDE workshop runs Phase-1 Week 4 (before any production code). This document seeds that workshop.
> **Authority:** `MASTER-PLAN.md §5.1` (threat model), [`../SECURITY.md`](../SECURITY.md).
> **Tool:** OWASP Threat Dragon.

---

## 1. System Scope

**In scope (Phase 1):**
- Static Cloudflare Pages app (SPA).
- Cloudflare R2 sample bucket (public, signed URLs).
- Cloudflare KV license store.
- Cloudflare Worker `/stripe/webhook` endpoint.
- Browser-side IndexedDB (Dexie).
- License token (Ed25519).
- Stripe Checkout redirect + webhook.

**Out of scope:**
- Stripe internal infrastructure (their trust boundary).
- Cloudflare's own infrastructure (vendor trust).
- User's MIDI hardware firmware.
- User's OS / browser internals (assume hostile).

---

## 2. Data Flow Diagram

```
┌────────────────┐     HTTPS      ┌────────────────────┐
│  User Browser  │──── static ───▶│  Cloudflare Pages  │
│  (React SPA)   │                └────────────────────┘
│                │
│  Dexie/IDB     │     HTTPS signed URL
│  Ed25519 pub   │─── samples ──▶┌────────────┐
│                │               │ R2 bucket  │
└──────┬─────────┘               └────────────┘
       │ redirect (3DS in EU)
       ▼
┌────────────────┐ webhook (HMAC)┌────────────────────┐
│ Stripe Checkout│──────────────▶│ Cloudflare Worker  │
│ (hosted SAQ-A) │               │  /stripe/webhook   │
└────────────────┘               └────────┬───────────┘
                                          │ write
                                          ▼
                                    ┌──────────┐
                                    │  KV      │
                                    │ event_id │
                                    │ → token  │
                                    └──────────┘
                                          │ email link
                                          ▼
                                    user email
```

Full diagram to be recreated in OWASP Threat Dragon at Week 4.

---

## 3. Assets & Abuser Stories

| Asset | Value | Primary abusers | Worst case |
|---|---|---|---|
| License signing private key (Ed25519) | Forgery at scale | Insider, supply chain, exploit of Workers Secrets | Free licenses minted indefinitely |
| KV license store | Revocation integrity | Worker compromise | Revoked keys re-activated |
| User's practice history (Dexie) | Privacy (minor) | XSS, prototype pollution | Leakage of non-sensitive practice data |
| Stripe webhook endpoint | Integrity of revenue flow | Replay, spoofing | Free license issuance, financial loss |
| Salamander samples (R2) | Availability + bandwidth cost | Hotlinking, DoS | Cost escalation |
| Static app bundle | Integrity | Tampering, CDN misconfiguration | Malicious code shipped to users |

---

## 4. STRIDE Matrix (seed — Week 4 workshop completes)

### 4.1 Static app (Cloudflare Pages)

| Category | Threat | Mitigation (existing / planned) |
|---|---|---|
| **S**poofing | Fake domain + phishing | HSTS preload, domain monitoring |
| **T**ampering | Subresource tampering via CDN compromise | SRI for critical scripts where feasible; Trusted Types |
| **R**epudiation | Supply chain CVE in app code | `npm audit`, Renovate, SBOM |
| **I**nformation disclosure | Leak via `Referer`, window.name | `Referrer-Policy: strict-origin-when-cross-origin` |
| **D**enial of service | DDoS on public CDN | Cloudflare absorbs; custom rate limits at Workers |
| **E**levation of privilege | CSP bypass via dangling `script-src` | Strict CSP (`default-src 'none'`), `require-trusted-types-for 'script'` |

### 4.2 Stripe webhook (Cloudflare Worker)

| Category | Threat | Mitigation |
|---|---|---|
| **S** | Forged webhook POST | `stripe.webhooks.constructEvent` HMAC timing-safe |
| **T** | Mutation in transit | TLS-only; HMAC |
| **R** | Replay of old `checkout.session.completed` | 5-minute replay window; `stripe_event_id` idempotency |
| **I** | Secret leakage via logs | Never log headers; Workers Secrets store signing secret |
| **D** | Webhook flood → cost escalation | Cloudflare rate limiting rule on the endpoint |
| **E** | Compromise of Workers Secrets | Hardware MFA on CF account; quarterly key rotation |

### 4.3 License token (Ed25519)

| Category | Threat | Mitigation |
|---|---|---|
| **S** | Forged token | Ed25519 signature; private key only in Worker |
| **T** | Token field tampering | Signature covers full payload |
| **R** | Non-repudiation of issuance | Worker audit log (Phase 2+ to `audit.events`) |
| **I** | PII in token payload | Payload uses `email_hash`, not raw email |
| **D** | Brute-force enumeration of tokens | Key space too large (Ed25519 256-bit); no observable oracle on `/unlock` |
| **E** | Version-downgrade attack | `version` field in payload; client enforces minimum |

### 4.4 IndexedDB / Dexie

| Category | Threat | Mitigation |
|---|---|---|
| **S** | — | — |
| **T** | Dexie schema downgrade | Schema version checks; refuse incompatible imports |
| **R** | — | — |
| **I** | XSS exfiltrates practice history | Strict CSP; Trusted Types; DOMPurify on any render of user-imported JSON |
| **D** | Quota exhaustion | Export prompt on quota warn; settings show usage |
| **E** | Prototype pollution via import | Zod validation at import boundary; `Object.freeze` on parsed JSON |

### 4.5 Samples (R2)

| Category | Threat | Mitigation |
|---|---|---|
| **S** | Tampered sample served | Hash manifest; client verifies checksum before caching |
| **T** | Swap of samples by compromised bucket | R2 versioning + hash verification |
| **I** | Hotlinking → cost | Signed URLs, `Referer` check, CF rate limits |
| **D** | Mass download | CF rate limit + cost alert |

---

## 5. Non-Applicable in Phase 1 (explicitly)

- **User uploads (MusicXML/MIDI/MXL):** disabled in Phase 1. No XXE, zip-bomb, or parser attack surface.
- **PDF processing:** disabled (`MASTER-PLAN.md §5.3`).
- **User auth:** no passwords, no sessions, no reset flows.
- **Supabase RLS:** not applicable (no backend DB in Phase 1; Phase 2+ gates this with pgTAP tests).

---

## 6. Workshop Output (Week 4 — TBD)

The Week-4 workshop will:

1. Redraw the DFD in OWASP Threat Dragon.
2. Complete the STRIDE matrix per asset with quantified likelihood/impact.
3. Produce an action list of code-level mitigations scheduled into Phase-1 implementation.
4. Record in `docs/security/THREAT-MODEL-WORKSHOP-v1.md` (dated).
5. Revisit at Phase-2 kickoff and every time an ADR adds an asset or trust boundary.

---

## 7. References

- `MASTER-PLAN.md §5.1`
- [`../SECURITY.md`](../SECURITY.md)
- [`CSP.md`](CSP.md)
- ADR-008, ADR-010, ADR-013, ADR-019
- OWASP Threat Dragon
