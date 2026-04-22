# User Journey Map — Piano-Practice (Phase 1)

> **Scope:** Phase 1 MVP flows end-to-end. Phase 2/3 journeys flagged for later maps.
> **Authority:** `MASTER-PLAN.md §1.1`, §2.4; [`SCREENS-INVENTORY.md`](SCREENS-INVENTORY.md); [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md).

---

## 1. Primary Personas (Phase 1)

| Persona | Snapshot | Primary goal |
|---|---|---|
| **A — Returning amateur** | 28–55, learned piano as a teen, returning after years, has a digital piano at home | Re-learn repertoire with structured feedback |
| **B — Serious beginner (adult)** | 22–45, disciplined, self-learner, bought a MIDI keyboard in last 2 years | Progress from scratch to playable-Bach in 12 months |
| **C — Pre-professional** | 16–30, conservatoire student/alum, pedagogically literate | Deepen two-voice independence, explore fingering reasoning |

All three are ≥ 16 (ADR-017). Children explicitly out of scope.

---

## 2. Journey J1 — First-Time Buyer (cold lead → first practice session)

### Stages

1. **Discover** — sees ad / referral → lands on `/`
2. **Evaluate** — reads value prop, pricing, FAQ, sample notation screenshot
3. **Decide** — clicks `Buy lifetime $29`
4. **Pay** — Stripe Checkout (hosted)
5. **Receive license** — return to `/buy/return` + email receipt
6. **Install / pin** — PWA install prompt on supported browsers
7. **Connect MIDI** — plug in keyboard, accept Web MIDI permission
8. **Pick first piece** — BWV 772 default suggestion
9. **Unlock audio** — tap "Start" (browser autoplay policy)
10. **Play first bars** — Guided lane, score-follower highlights correct notes
11. **Session summary** — "You played 64% of bar 1–4 correctly — try again?"
12. **Return next day** — PWA icon opens library; last-practiced piece is top card

### Per-stage detail

| # | Screen | User thinks / feels | Happy-path | Risk / friction | Mitigation |
|---|---|---|---|---|---|
| 1 | `/` | "Is this real?" | Hero + 30 s demo video | Too salesy → bounce | Copy from [`UX-COPY.md`](UX-COPY.md) — evidence-first |
| 2 | `/` | "Does this fit me?" | FAQ answers 5 real objections | Unclear what's included | Explicit "What's in v1.0" block |
| 3 | `/` | "Worth $29?" | One-click to Checkout | Fear of commitment | 14-day refund mentioned adjacent to CTA |
| 4 | Stripe | "Payment is secure" | Stripe hosted UI | 3DS friction in EU (unavoidable) | Pre-educate in FAQ |
| 5 | `/buy/return` + email | "Did it work?" | Polling + email arrives in 30–60 s | Email in spam / delay | In-app polling + "Didn't arrive?" link → `/unlock` |
| 6 | native prompt | "What is this asking?" | PWA install card appears | Confusing on iOS (manual add to home screen) | Plain-language guide screen for iOS |
| 7 | browser prompt | "Why does it need MIDI?" | Inline explainer before request | Permission denied → silent failure | Explicit banner + retry button |
| 8 | `/app` | "What should I play?" | BWV 772 highlighted as "Start here" | Overwhelm (15 pieces) | Curated first-piece recommendation |
| 9 | `/app/practice/:id` | "Nothing's happening" | Obvious Start button | Autoplay policy silent fail on Safari | Persistent Start overlay until gesture registered |
| 10 | `/app/practice/:id` | "Is it listening?" | Visible note-hit feedback + MIDI pill | High latency feels "broken" | LatencyBadge warns if > 30 ms |
| 11 | `/app/session/:id/summary` | "How did I do?" | Honest stats, no faux-confetti | Over-praise fatigues experienced users | Quiet summary; optional rating |
| 12 | PWA icon → `/app` | "Where did I leave off?" | Resume card top of library | Expectation: sync across devices | Phase 1 = single-device; clear copy (ADR-013) |

### Success metric
- Time from Checkout completion to first note played: **p75 ≤ 5 minutes**.
- First-session completion (reached session summary): **≥ 60 %**.

### Known friction accepted for Phase 1
- iOS Safari has no Web MIDI → iOS = playback-only. Copy states this on device detection.
- Single-device only → no cross-device handoff. Copy states this on Settings → Data.

---

## 3. Journey J2 — Returning User (day 3+)

### Stages
1. PWA icon / bookmark → `/app`
2. "Resume BWV 774 — last practised 2 days ago" card top of Library
3. One tap → `/app/practice/774`
4. Audio unlocks on first interaction (sample cached from prior session; load < 200 ms)
5. Practice session → summary → optionally next piece suggestion

### Key moments
- **Reopen latency:** offline cache hits; LCP p75 ≤ 1.5 s.
- **Continuity:** last-practised piece visible without search.
- **Progress feedback:** 7-day heatmap visible on `/app/session/:id/summary`.

### Friction to monitor
- MIDI device re-connect: browser sometimes forgets permission — explicit re-grant button.
- Sample cache eviction (iOS 7-day quota): first-hit re-download; warn if slow network.

---

## 4. Journey J3 — License Recovery (email lost)

### Stages
1. User opens app on new device → `/unlock` empty field
2. Tries old license from memory → fails
3. Clicks "Can't find your license?" → email capture
4. Form asks for purchase email → Worker endpoint looks up license via Stripe customer → re-sends license email
5. User pastes fresh license → `/app`

### Acceptance criteria
- Lookup takes ≤ 10 s.
- Re-issued license is the same token (not newly-signed) — avoids issuing duplicates.
- Rate-limited: 3 requests per email per 24 h.

### Copy constraints
- Never expose "license for <email> not found" (privacy + enumeration).
- Always respond with "If an account exists, we've sent the license to <email>".

---

## 5. Journey J4 — Refund (within 14 days)

### Stages
1. User emails `support@`
2. Founder (Phase 1) reviews, initiates Stripe refund via dashboard
3. Stripe webhook fires `charge.refunded`
4. Worker revokes license in KV (`revokedAt = now`)
5. Next app open: LicenseValidator sees revocation → redirects to `/legal/terms#refund` with message

### UX requirements
- **Explicit** — user understands access will stop.
- **Graceful** — offline sessions continue to end of day; next re-open fails.
- **No data loss** — user can export JSON before access is revoked (encourage this in the "refund confirmed" email).

---

## 6. Journey J5 — Offline Practice

### Stages
1. User on plane / no Wi-Fi opens PWA icon
2. Service Worker serves cached app shell; SW detects offline
3. `/app` shows library — pieces whose samples are cached show "Practice" CTA; others show "Needs download" badge
4. User practices cached piece → session saved locally in Dexie
5. On reconnect, nothing to sync in Phase 1 (ADR-013); Phase 2+ will upload append-only events

### UX requirements
- Clear offline banner (dismissible, reappears on route change if still offline).
- Uncached pieces not hidden — explicit "will download 8 MB" on re-connect CTA.
- No silent failures: every offline-blocked interaction emits a toast with a reason.

---

## 7. Journey J6 — Accessibility Path (screen reader + keyboard only)

### Stages
1. User lands on `/` → Tab through; Skip-to-content is the first focusable element
2. Tabs to Buy CTA; SR announces "Buy lifetime $29, 14-day refund"
3. Stripe Checkout is a3y-audited by Stripe (not our responsibility) but referrer check ensures we return gracefully
4. `/buy/return` announces polling progress via `aria-live="polite"`
5. `/unlock` — label-associated input; errors announced
6. `/app` — `h1` "Library"; pieces navigated via Tab or arrow keys in grid mode
7. `/app/practice/:id` — keyboard shortcuts: `Space` = play/pause, `[` and `]` = tempo ±5 BPM, `,` and `.` = section loop A/B, `r` = restart bar
8. Session progress announced at bar boundaries (batched, not per-note)
9. Summary: `<dl>` list of stats; heatmap has textual alt description

### Acceptance
- Zero axe-core violations on all critical-path screens.
- Manual SR pass with NVDA (Windows), VoiceOver (macOS/iOS), TalkBack (Android). Cadence: pre-launch + quarterly.

---

## 8. Journey J7 — Locale Switch (TR ⇄ EN)

### Stages
1. User lands in default locale (based on `Accept-Language`, TR fallback for TR IP geo hint via CF `cf-ipcountry`)
2. Opens `/app/settings/appearance` → Language dropdown
3. Selects alternative → all strings re-render; `<html lang>` updates
4. Practice session strings (including live-region feedback) respect the new locale

### Constraints
- No page reload.
- Locale preference persists to Dexie.
- Per-piece content (composer name, title) stays canonical (proper name); localised fields are descriptions, difficulty labels, practice tips.

---

## 9. Journey J8 — Phase-0 Validation Visitor (disposable)

(Documented for completeness — this is the Sprint-0 ad-traffic journey.)

1. Click ad → `/sprint0/landing`
2. Read value prop → submit email
3. Click intent CTA ("I'd pay $29 for this") → intent event fired
4. Thank-you state: "We'll email you when we launch in 12 weeks"
5. (No sale. No access. Phase 0 is pure validation.)

### Success metric
- CPA ≤ €15 to email; intent ≥ 5 % (Gate G1 criterion C1+C2).

---

## 10. Friction Heat Map (Phase 1 highest-risk moments)

1. **Stripe return → license observed in app** — webhook vs UX race; timeout handled.
2. **Audio unlock on iOS Safari** — first gesture invisible if Start button scrolled off-screen.
3. **MIDI permission re-prompt** — browser memory isn't persistent across all browsers.
4. **Sample download on cellular** — 5 MB Lite is OK; Full 150 MB must never auto-download.
5. **License email deliverability** — spam folder blocks entire install path.
6. **PWA install UX on iOS** — manual add-to-home-screen; user needs a clear 3-step explainer.

Each friction has a mitigating feature in [`SCREENS-INVENTORY.md`](SCREENS-INVENTORY.md) and a Playwright E2E (where automatable).

---

## 11. References

- [`SCREENS-INVENTORY.md`](SCREENS-INVENTORY.md)
- [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md)
- [`UX-COPY.md`](UX-COPY.md)
- [`ACCESSIBILITY.md`](ACCESSIBILITY.md)
- `MASTER-PLAN.md §2.4` (Phase 1 scope), §5.4 (age), §5.5 (checkout)
