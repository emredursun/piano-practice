# API Contract — Piano-Practice

> **Scope:** Phase 1 surface only. Phase 2 REST endpoints preview in §5.
> **Authority:** `MASTER-PLAN.md §5.5` (Stripe webhook), ADR-010, ADR-019.

---

## 1. Phase-1 API Surface

Phase 1 has **one** server endpoint (Cloudflare Worker) and two **client-local** contracts (license token format, export JSON schema). Everything else is static.

| Endpoint / Contract | Owner | Visibility |
|---|---|---|
| `POST /stripe/webhook` | Cloudflare Worker | Stripe only |
| `POST /license/recover` | Cloudflare Worker | public, rate-limited |
| License token | Client-verifiable | embedded |
| Export JSON | Client-generated | user-controlled |

---

## 2. `POST /stripe/webhook`

### Request

- **Headers:**
  - `Stripe-Signature: t=<ts>,v1=<hex>` — required
  - `Content-Type: application/json` — required
- **Body:** raw Stripe event (JSON) — **must not be parsed before HMAC verification**

### Processing

1. Read raw body as `ArrayBuffer`.
2. `stripe.webhooks.constructEvent(rawBody, sigHeader, STRIPE_WEBHOOK_SECRET)` with `tolerance: 300` seconds (5-min replay window).
3. Idempotency check: `KV.get('stripe:' + event.id)` — if found, return 200 (already processed).
4. Switch on `event.type`:
   - `checkout.session.completed` → issue license.
   - `charge.refunded` → revoke license.
   - Others → log + 200.
5. Upsert to KV.

### Responses

- **200** on success or idempotent replay.
- **400** on signature verification failure (do **not** include details in body).
- **500** on internal error (Sentry captures).

### Constraints

- Response time **p95 ≤ 500 ms** (Stripe retries after 5 s).
- Never leak signing secret in logs.
- Never trust `event.data.object.amount` for license decisions — use server-side price lookup via `STRIPE_PRICE_LIFETIME_{TRY,EUR,USD}`.

### License issuance payload

```ts
type LicensePayload = {
  email_hash: string;    // SHA-256(email) hex
  sku: "lifetime-v1";
  issued_at: number;     // Unix seconds
  version: 1;            // Schema version
};
```

Signed Ed25519 → base64url encoded → shaped as `pp-<ver>.<payload>.<sig>`.

---

## 3. `POST /license/recover`

### Request

- **Body:** `{ "email": "<string>" }`
- **Rate limit:** 3 per email per 24 h (KV counter).
- **Captcha (Phase 2+):** Cloudflare Turnstile.

### Processing

1. Validate email shape (Zod).
2. Look up Stripe customer by email (Stripe API).
3. If found, re-issue **the same** license token (or fetch from KV by `email_hash`).
4. Send email via transactional provider.

### Responses

- **202 Accepted** — always, regardless of whether customer exists (privacy / enumeration prevention).
- **429 Too Many Requests** on rate limit exceeded.

---

## 4. Client-Local Contracts

### 4.1 License token (client verification)

```ts
function verifyLicense(token: string, publicKey: Uint8Array): LicensePayload | null;
```

- Parses `pp-<ver>.<payload>.<sig>`.
- Rejects if `version < MIN_VERSION`.
- Verifies Ed25519.
- Returns payload or `null`.

### 4.2 Export JSON schema

```ts
type ExportV1 = {
  schemaVersion: 1;
  generatedAt: string;   // ISO 8601
  appVersion: string;    // Build hash
  settings: Settings;
  sessions: PracticeSession[];
  fingeringOverrides: Record<string, Fingering[]>;
  licenseRef: { sku: "lifetime-v1" }; // no secret
};
```

Import: validated with Zod; incompatible `schemaVersion` → helpful upgrade-path error.

---

## 5. Phase-2 REST Preview

Supabase + Worker adds:

| Endpoint | Purpose |
|---|---|
| `POST /auth/signup` | Supabase Auth proxy |
| `POST /auth/signin` | Supabase Auth proxy |
| `POST /auth/signout` | Global sign-out |
| `GET /me` | Current user + license |
| `GET /sessions` | Practice history |
| `POST /sessions` | Append practice event |
| `GET /pieces` | Expanded catalog |
| `POST /fingering/override` | User fingering override |

Full Phase-2 contract drafted at Phase-2 kickoff via OpenAPI 3.1.

---

## 6. Error Model

Responses use RFC 9457 Problem Details **only** for Phase-2 REST; Phase-1 Worker keeps responses minimal for security and simplicity.

```json
{
  "type": "https://piano-practice.dev/errors/rate-limited",
  "title": "Too Many Requests",
  "status": 429,
  "detail": "Try again in 24 hours.",
  "instance": "/license/recover"
}
```

---

## 7. Versioning

- License tokens: explicit `version` field; back-compat strategy documented at each bump.
- Export JSON: explicit `schemaVersion`; import must upgrade forward, never silently downgrade.
- Phase-2 REST: URL prefix `/v1/...`; breaking changes bump the prefix.

---

## 8. References

- `MASTER-PLAN.md §5.5`
- ADR-010, ADR-019
- [`SCHEMA.md`](SCHEMA.md) — Phase-2 DB schema
- [`STATE-SYNC.md`](STATE-SYNC.md) — Phase-2/3 sync design
