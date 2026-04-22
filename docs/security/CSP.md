# Content Security Policy — Piano-Practice

> **Authority:** `MASTER-PLAN.md §5.2`, [`../SECURITY.md §3`](../SECURITY.md).
> **Rollout:** Report-Only for 2 weeks pre-launch → enforce at Phase-1 launch.

---

## 1. Policy (Phase 1, enforce at launch)

Served via Cloudflare Pages `_headers`:

```
Content-Security-Policy:
  default-src 'none';
  script-src 'self' 'wasm-unsafe-eval' https://js.stripe.com;
  style-src 'self' 'unsafe-hashes' 'sha256-<per-build-hash>';
  img-src 'self' data: https://*.r2.cloudflarestorage.com;
  connect-src 'self' https://*.r2.cloudflarestorage.com https://api.stripe.com
              https://*.ingest.sentry.io https://api.honeycomb.io
              https://eu.i.posthog.com;
  media-src 'self' https://*.r2.cloudflarestorage.com;
  worker-src 'self' blob:;
  frame-src https://js.stripe.com https://hooks.stripe.com;
  frame-ancestors 'none';
  base-uri 'self';
  form-action 'self' https://checkout.stripe.com;
  require-trusted-types-for 'script';
  trusted-types default osmd-sanitized;
  report-to csp-endpoint;
  report-uri https://<project>.ingest.sentry.io/api/<id>/security/?sentry_key=<key>;

Reporting-Endpoints: csp-endpoint="https://<project>.ingest.sentry.io/api/<id>/security/?sentry_key=<key>"

Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Resource-Policy: same-origin
Cross-Origin-Embedder-Policy: credentialless

Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: midi=(self),
                    microphone=(),
                    camera=(),
                    geolocation=(),
                    payment=(self "https://js.stripe.com"),
                    interest-cohort=()
```

### Phase-0 prototype (`/sprint0/*`)

Looser CSP while prototyping. Must not ship to production. A dedicated `_headers` file under `/sprint0/*` enforces a narrower scope with `report-only` to catch regressions early.

---

## 2. Per-Directive Rationale

| Directive | Value | Why |
|---|---|---|
| `default-src 'none'` | | Deny-by-default; opt in each category |
| `script-src 'self'` | | First-party only |
| `'wasm-unsafe-eval'` | | Tone.js AudioWorklet + WASM-heavy OSMD glyphs |
| `https://js.stripe.com` | | Stripe Checkout JS |
| `style-src 'self'` | | First-party only |
| `'unsafe-hashes' 'sha256-…'` | | Limited inline-style hashes generated per build |
| `img-src data:` | | Small inline SVGs (icons); never external data: URLs |
| `connect-src` list | | Samples (R2), Stripe API, Sentry, Honeycomb, PostHog EU |
| `frame-src stripe.com hooks.stripe.com` | | Stripe Checkout iframe + 3DS |
| `frame-ancestors 'none'` | | No one frames us |
| `require-trusted-types-for 'script'` | | DOM-XSS hardening |
| `trusted-types default osmd-sanitized` | | OSMD output sanitised via named policy |
| `base-uri 'self'` | | Prevent base-tag injection |
| `form-action 'self' checkout.stripe.com` | | Only our forms + Stripe |

---

## 3. Trusted Types

Two policies:

- **`default`** — strict fall-through that refuses most assignments; used by libraries.
- **`osmd-sanitized`** — accepts OSMD-generated HTML strings after explicit sanitisation by DOMPurify (restricted tag allowlist).

All code calling `innerHTML`, `document.write`, `setTimeout(stringArg)` **must** go through a Trusted Types policy or be refactored to avoid them.

---

## 4. Report-Only Stage

Two weeks before Phase-1 launch:

- Deploy policy with `Content-Security-Policy-Report-Only` header.
- Monitor Sentry `Security Report` events.
- Classify each report:
  - **Legitimate:** tighten policy, or add allowed origin after review.
  - **Spurious:** browser extension / malware injection — no action.
  - **Bug:** code change to comply.
- Require **zero legitimate unclassified reports for 48 h** before flipping to enforcing.

---

## 5. Runbook — CSP Violation in Production

1. **Sentry alert:** Security Report spike.
2. Identify violation directive + blocked URL.
3. If legitimate resource: temporarily trip `killswitch.sentry` equivalent or deploy Report-Only override, then permanently adjust policy in a PR.
4. If attack: document; no policy change needed.
5. Post-mortem if the spike correlated with an outage signal.

---

## 6. Test Coverage

- **Playwright CI:** loads every critical route, asserts no CSP violations in Report-Only mode.
- **Manual per release:** Stripe Checkout redirect + return tested with CSP enforcing; any 3DS variant tested.

---

## 7. Interaction with PWA / SW

- Service Worker itself served from `self`; `worker-src 'self' blob:` allows dynamic workers (AudioWorklet blobs).
- SW updates go through the same CSP; no relaxation for SW-fetched resources.

---

## 8. Known Tensions / Trade-offs

- **`'unsafe-hashes'`**: required for a small set of Tone.js / OSMD inline styles. Scoped per sha256 hash and audited each build. If either library removes those inline styles, the directive drops.
- **Stripe requires `js.stripe.com`**: unavoidable; Stripe maintains this host.
- **PostHog EU**: opt-in-only RUM path; `eu.i.posthog.com` listed under `connect-src`. Removing PostHog means removing the directive.

---

## 9. Future (Phase 2+)

- Supabase adds `*.supabase.co` to `connect-src` + `auth.supabase.co` to `form-action`.
- If multi-region DR ships, add secondary hosts to `connect-src`.
- If per-user AI fingering ships (Phase 3), the inference endpoint goes under `connect-src` with DPIA sign-off.

---

## 10. References

- `MASTER-PLAN.md §5.2`
- [`THREAT-MODEL.md`](THREAT-MODEL.md)
- [`../SECURITY.md §3`](../SECURITY.md)
- MDN CSP docs; W3C Trusted Types
