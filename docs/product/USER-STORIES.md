# User Stories — Piano-Practice (Phase 1)

> **Scope:** User stories with acceptance criteria, mapped to PRD features and Phase-1 scope.
> **Authority:** [`PRD.md`](PRD.md), [`../design/USER-JOURNEY-MAP.md`](../design/USER-JOURNEY-MAP.md).
> **Format:** Gherkin-light. Every story has a unique ID (`US-NN`) and links to PRD feature and test fixtures.

---

## Persona Shortcodes

- **A** — Returning amateur
- **B** — Serious self-learning beginner (adult)
- **C** — Pre-professional

All ≥ 16; TR or EU resident.

---

## Epic 1 — Purchase & License

### US-01 — First-time purchase

**As** persona A/B/C
**I want** to buy a lifetime license in one click
**So that** I can start practising without creating an account

**Given** I'm on `/`
**When** I click "Buy lifetime — $29"
**Then** I'm redirected to Stripe Checkout
**And** geo-priced according to `cf-ipcountry` (TR / EU / fallback)
**And** the terms include a 16+ age confirmation checkbox
**And** TR users see an explicit KVKK cross-border consent checkbox

**Maps to:** F-01
**Tests:** `e2e/purchase.spec.ts`

---

### US-02 — Receive license email

**As** a buyer who just paid
**I want** my license to arrive in email within a minute
**So that** I can unlock the app without hunting for support

**Given** checkout completes successfully
**When** Stripe fires `checkout.session.completed`
**Then** the Worker webhook verifies + issues a license
**And** the buyer receives an email with the license code and `/unlock?t=<token>` link within 60 s p95
**And** the app on `/buy/return` polls every 10 s up to 30 s and auto-redirects to `/app` once valid license observed

**Maps to:** F-01
**Tests:** `integration/stripe-webhook.spec.ts`, `e2e/buy-return.spec.ts`

---

### US-03 — License entry from email link

**As** a buyer who opens the email link
**I want** my license pre-filled and validated
**So that** I don't have to copy-paste manually

**Given** I click the `/unlock?t=<token>` link
**When** the page loads
**Then** the token is verified client-side
**And** on success I'm redirected to `/app`
**And** the `?t` parameter is removed from the history via `history.replaceState` (so it doesn't leak via Referer)

**Maps to:** F-01
**Tests:** `e2e/unlock-deeplink.spec.ts`

---

### US-04 — License recovery

**As** a buyer who lost the email
**I want** to recover my license using my purchase email
**So that** I can continue practising on a new device

**Given** I'm on `/unlock` and click "Can't find your license?"
**When** I enter my purchase email
**Then** the Worker looks up the license via Stripe customer and re-sends the original license
**And** I receive a privacy-safe response ("If an account exists, we've sent the license to <email>")
**And** I am rate-limited to 3 requests per email per 24 h

**Maps to:** F-01
**Tests:** `integration/license-recovery.spec.ts`

---

### US-05 — Refund

**As** a buyer within 14 days of purchase
**I want** to request a full refund
**So that** I can get my money back if the product isn't for me

**Given** I email `support@` within 14 days
**When** the founder initiates the refund via Stripe
**Then** Stripe fires `charge.refunded`
**And** the Worker revokes the license in KV
**And** I receive a confirmation email encouraging JSON export **before** access is revoked
**And** my next app open shows a screen-level block with the revocation reason

**Maps to:** F-01
**Tests:** `integration/refund.spec.ts`

---

## Epic 2 — Library & Piece Selection

### US-06 — Browse library

**As** a licensed user
**I want** to see all 15 pieces at a glance
**So that** I can choose what to practise

**Given** I'm on `/app` with a valid license
**When** the Library loads
**Then** I see 15 pieces with title, BWV number, ABRSM grade, length
**And** p95 LCP ≤ 2.5 s on 4G mobile

**Maps to:** F-02
**Tests:** `e2e/library.spec.ts`, Lighthouse CI

---

### US-07 — Resume last-practised piece

**As** a returning user
**I want** my last-practised piece to be visible immediately
**So that** I can continue without searching

**Given** I practised a piece within the last 14 days
**When** I open `/app`
**Then** a "Resume" card is displayed at the top with a one-tap CTA

**Maps to:** F-02
**Tests:** `e2e/library-resume.spec.ts`

---

### US-08 — Offline library state

**As** an offline user
**I want** to see which pieces I can practise now vs. which need download
**So that** I don't click into a dead-end

**Given** `navigator.onLine === false`
**When** I open `/app`
**Then** cached pieces show a "Practise" CTA
**And** un-cached pieces show "Needs download (X MB)" and are not clickable for practise

**Maps to:** F-02, F-08
**Tests:** `e2e/offline-library.spec.ts`

---

## Epic 3 — Practice Session

### US-09 — Start a Guided session

**As** a serious self-learner
**I want** to tap Start and have audio + MIDI come alive
**So that** I can begin practising without a permission ambiguity loop

**Given** I'm on `/app/practice/:id` and the audio context is locked
**When** I tap Start
**Then** `Tone.start()` is called inside the event handler
**And** MIDI permission is requested in the same gesture
**And** a 200 ms A4 test-tone plays (confirming audio path)
**And** the session enters `ready` state

**Maps to:** F-03
**Tests:** `e2e/practice-start.spec.ts`, Playwright iOS Safari emulation

---

### US-10 — Play along with score-follower feedback

**As** a practising user
**I want** correct-note highlights and a forgiving wrong-note indicator
**So that** I can see my progress without being interrupted

**Given** I'm in `playing` state
**When** I play a correct note within the chord window (50 ms)
**Then** the cursor advances and the note renders with the correct-overlay colour
**When** I play an unexpected note
**Then** a brief red pulse appears on the score and the cursor stays
**And** the SR announces "Missed" at most once per 2 s (debounced)

**Maps to:** F-03
**Tests:** `unit/score-follower.spec.ts`, `e2e/practice-session.spec.ts`

---

### US-11 — Section loop A/B (Studio)

**As** a pre-professional user
**I want** to set loop boundaries and drill a passage
**So that** I can focus on the hard parts

**Given** I'm in Studio lane
**When** I press `[` at bar 4 and `]` at bar 8
**Then** bars 4–8 highlight as the loop
**And** pressing Play loops that section indefinitely until I press `Esc`

**Maps to:** F-04
**Tests:** `unit/loop-ab.spec.ts`, `e2e/studio.spec.ts`

---

### US-12 — Hands-separate (Studio)

**As** a pre-professional user
**I want** to drill left hand or right hand only
**So that** I can fix voice-specific issues

**Given** I'm in Studio lane
**When** I select "Right hand only"
**Then** the score dims the left-hand staff
**And** only right-hand notes drive the score-follower match

**Maps to:** F-04
**Tests:** `unit/voice-split.spec.ts`, `e2e/studio.spec.ts`

---

### US-13 — Tempo ramp (Studio)

**As** a practising user
**I want** the tempo to gradually increase over repetitions
**So that** I can build speed without setting it manually each time

**Given** I've set ramp from 60 % to 100 % over 5 iterations
**When** I press Play
**Then** each complete iteration increments tempo linearly
**And** drift from the schedule ≤ 5 ms cumulative

**Maps to:** F-04
**Tests:** `unit/tempo-ramp.spec.ts`

---

### US-14 — Blind play (Studio)

**As** a pre-professional user
**I want** to hide notation below the cursor
**So that** I can practise memorisation

**Given** I'm in Studio lane
**When** I press `b` or tap the Blind toggle
**Then** bars below the current cursor position are masked
**And** the mask lifts when I press Pause

**Maps to:** F-04
**Tests:** `e2e/blind-play.spec.ts`

---

## Epic 4 — Session Summary

### US-15 — See session accuracy

**As** any user completing a session
**I want** to see a calm summary of how I did
**So that** I close the loop emotionally and learn where to focus next

**Given** I've completed a practice session
**When** the session ends (reached final bar or user taps "End")
**Then** I'm taken to `/app/session/:id/summary`
**And** I see overall accuracy %, per-bar accuracy sparkline, and session duration
**And** the summary renders within 800 ms of end

**Maps to:** F-05
**Tests:** `e2e/session-summary.spec.ts`

---

### US-16 — Log an SRS rating (seed for Phase 2)

**As** a user who completed a session
**I want** to rate how it felt
**So that** Phase-2 spaced repetition has data when it launches

**Given** I'm on the summary screen
**When** I tap one of Again / Hard / Good / Easy
**Then** the rating is stored in Dexie
**And** no scheduling change occurs (Phase 1 does not use ratings yet)

**Maps to:** F-05
**Tests:** `unit/session-rating.spec.ts`

---

## Epic 5 — Settings

### US-17 — Switch theme and locale live

**As** any user
**I want** theme and locale to change without a reload
**So that** I don't lose context

**Given** I'm in `/app/settings/appearance` or `/language`
**When** I change the option
**Then** all UI strings re-render in the new locale
**And** CSS tokens re-bind for the new theme
**And** `<html lang>` updates
**And** the preference persists to Dexie

**Maps to:** F-06
**Tests:** `e2e/settings-theme-locale.spec.ts`

---

### US-18 — Test-tone for audio / device

**As** a user with a new MIDI device
**I want** a test-tone button
**So that** I can confirm audio + MIDI are alive

**Given** I'm on `/app/settings/audio` or `/midi`
**When** I tap "Play test tone"
**Then** a 500 ms A4 tone plays
**And** if a MIDI device is connected, pressing any key is echoed audibly

**Maps to:** F-06
**Tests:** `e2e/settings-test-tone.spec.ts`

---

## Epic 6 — Data Portability

### US-19 — Export my data

**As** a privacy-conscious user
**I want** to export my practice history and settings to JSON
**So that** I can back up or move devices

**Given** I'm on `/app/settings/export`
**When** I tap "Export my data"
**Then** a JSON file is downloaded
**And** it contains: settings, sessions, license reference, fingering overrides
**And** it does **not** contain samples or secret material

**Maps to:** F-07
**Tests:** `e2e/export.spec.ts`, `unit/export-schema.spec.ts`

---

### US-20 — Import my data

**As** a user on a new device
**I want** to import my JSON so I pick up where I left off

**Given** I have a valid export JSON
**When** I tap "Import" and select the file
**Then** I see a confirmation dialog noting this replaces current data
**And** on confirm, my state is replaced
**And** samples re-download on first play
**And** a schema-version mismatch is caught with a helpful message

**Maps to:** F-07
**Tests:** `e2e/import.spec.ts`, `unit/import-schema.spec.ts`

---

## Epic 7 — PWA & Offline

### US-21 — Install as PWA

**As** a returning user
**I want** to install the app to my home screen
**So that** I can launch it like a native app

**Given** I'm on a supported browser (Chromium) with `beforeinstallprompt` available
**When** I return for the second time OR complete my first session
**Then** an install card is shown with a dismiss option
**And** accepting installs the PWA with the correct icon and theme colour

**Given** iOS Safari (no programmatic prompt)
**Then** the card shows a visual "Add to Home Screen" guide

**Maps to:** F-08
**Tests:** `e2e/pwa-install.spec.ts`

---

### US-22 — Practise offline

**As** an offline user
**I want** to practise a cached piece
**So that** travel / poor Wi-Fi doesn't block me

**Given** I cached a piece's samples during a previous session
**And** I'm now offline
**When** I open `/app/practice/:id` for that piece
**Then** the app loads and plays without network
**And** LCP ≤ 1.5 s p95 on local cache hit

**Maps to:** F-08
**Tests:** `e2e/offline-practice.spec.ts`

---

## Epic 8 — Legal & Compliance

### US-23 — View privacy policy

**As** a regulator or user
**I want** to read a clear privacy policy in TR or EN
**So that** I understand what is collected and why

**Given** I'm anywhere in the product
**When** I click "Privacy" in the footer
**Then** I see `/legal/privacy` in my current locale
**And** the page passes axe-core with zero violations
**And** the contact for data-subject requests is visible

**Maps to:** F-09
**Tests:** `e2e/legal.spec.ts`

---

### US-24 — Accessibility statement

**As** a regulator
**I want** to read an accessibility statement conforming to EN 301 549
**So that** I can assess EAA 2025 compliance

**Given** I visit `/legal/accessibility`
**Then** I see scope, conformance level (WCAG 2.1 AA), known limitations, feedback contact, and last-reviewed date

**Maps to:** F-09
**Tests:** content review checklist

---

## Epic 9 — Observability

### US-25 — Opt-in RUM

**As** a privacy-conscious user
**I want** to turn off anonymous performance telemetry
**So that** I fully control what leaves my device

**Given** I'm on `/app/settings/data`
**When** I toggle "Share anonymous performance metrics"
**Then** RUM events stop firing immediately
**And** the preference persists

**Maps to:** F-10
**Tests:** `e2e/rum-optout.spec.ts`, `unit/rum-disabled.spec.ts`

---

## Traceability Matrix (excerpt)

| Story | PRD Feature | Gate criterion | Primary test |
|---|---|---|---|
| US-01 | F-01 | G2 (paying customers) | `e2e/purchase.spec.ts` |
| US-09 | F-03 | G1-C5 (audio latency) | `e2e/practice-start.spec.ts` |
| US-22 | F-08 | — | `e2e/offline-practice.spec.ts` |
| US-25 | F-10 | — | `e2e/rum-optout.spec.ts` |

Full matrix generated from story frontmatter at build-time.

---

## References

- [`PRD.md`](PRD.md)
- [`FEATURE-FLAGS.md`](FEATURE-FLAGS.md)
- [`../design/USER-JOURNEY-MAP.md`](../design/USER-JOURNEY-MAP.md)
- [`../design/SCREENS-INVENTORY.md`](../design/SCREENS-INVENTORY.md)
