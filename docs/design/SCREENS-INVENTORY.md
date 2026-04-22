# Screens Inventory — Piano-Practice (Phase 1)

> **Scope:** Every screen that ships in Phase 1 MVP. Phase 2/3 screens marked explicitly.
> **Authority:** `MASTER-PLAN.md §2.4` (Phase 1 scope), ADR-001 (SPA), [`INFORMATION-ARCHITECTURE.md`](INFORMATION-ARCHITECTURE.md).
> **Owner:** Founder. Every screen listed has a wireframe in [`WIREFRAMES.md`](WIREFRAMES.md).

---

## 1. Route Map (Phase 1)

| # | Route | Name | Auth | Purpose | Primary components |
|---|---|---|---|---|---|
| 1 | `/` | Landing (product) | none | Marketing + purchase CTA | `Hero`, `FeatureList`, `PricingCard`, `FAQ`, `Footer` |
| 2 | `/buy/return` | Post-checkout | none | Confirmation + license capture | `LicenseEntry`, `DeviceInstructions` |
| 3 | `/unlock` | License entry | none | User pastes license from email | `LicenseEntry` |
| 4 | `/app` | Library (piece picker) | license | Pick a piece to practice | `PieceGrid`, `MidiStatusPill`, `LatencyBadge` |
| 5 | `/app/piece/:id` | Piece detail | license | Metadata + "Practice" CTA | `PieceHeader`, `DifficultyBadge`, `PracticeBtn` |
| 6 | `/app/practice/:id` | Practice session (Guided) | license | Score-follower playthrough w/ hand-holding | `ScorePane`, `ScoreCursor`, `PracticeBar` (guided) |
| 7 | `/app/practice/:id?lane=studio` | Practice session (Studio) | license | Full control, advanced params | `ScorePane`, `PracticeBar` (studio), `TempoRamp`, `LoopAB` |
| 8 | `/app/session/:id/summary` | Session summary | license | Post-session stats + rating | `SessionStats`, `ProgressHeatmap`, `SRSRatingStub` |
| 9 | `/app/settings` | Settings | license | Audio, MIDI, theme, locale, export | `SettingsTabs`, `DeviceTest`, `ExportPanel` |
| 10 | `/app/settings/export` | Export / Import | license | JSON portability | `ExportPanel`, `ImportPanel` |
| 11 | `/legal/privacy` | Privacy policy | none | KVKK/GDPR notice (TR+EN) | static MD-rendered |
| 12 | `/legal/terms` | Terms | none | ToS + 16+ gate, refund | static |
| 13 | `/legal/subprocessors` | Subprocessors | none | Stripe, Cloudflare, Sentry, Honeycomb list | static |
| 14 | `/legal/accessibility` | Accessibility statement | none | EN 301 549 template | static |
| 15 | `/legal/security` | Security contact | none | `security@`, PGP, bug bounty intent | static; mirrors `/.well-known/security.txt` |
| 16 | `/404` | Not found | — | Friendly error + search | `ErrorFallback` |
| 17 | `/offline` | Offline shell | — | Service Worker fallback | `OfflineCard` with cached pieces list |
| 18 | `/sprint0/landing` | Phase-0 ad landing | none | Value prop + email + intent — disposable | `HeroSprint0`, `IntentForm` |
| 19 | `/sprint0/proto` | Phase-0 prototype | none | 2-piece vertical slice — disposable | `ProtoShell`, `ScorePane`, `LatencyBadge` |

**Routing library:** React Router v6. **Auth wrapper:** `<Licensed>` HOC reads from `LicenseValidator`; redirects unlicensed users to `/unlock`.

---

## 2. Screen Detail Template

Every screen ships with this contract:

- **Purpose** — one sentence; what the user is trying to do
- **Entry** — how the user gets here (link, redirect, deep-link)
- **Exit** — where the user goes next (CTAs, back)
- **Data dependencies** — what must be loaded before the screen is useful
- **States** — empty, loading, loaded, error, offline
- **Error budget** — which errors are tolerable, which redirect
- **A11y notes** — heading order, landmarks, focus on mount
- **Responsive behaviour** — `xs`, `md`, `lg` variants (see [`RESPONSIVE-STRATEGY.md`](RESPONSIVE-STRATEGY.md))
- **Analytics events** — Plausible event names fired from this screen
- **Feature flags** — any flag guarding full/partial render
- **Performance budget** — LCP target, chunk hint

---

## 3. Per-Screen Specification

### 3.1 `/` — Landing (product)

- **Purpose:** convince a qualified piano learner to buy the $29 lifetime license.
- **Entry:** organic, ad click, referral, direct.
- **Exit:** Stripe Checkout (primary), `/legal/*` (secondary), `/unlock` (already bought).
- **Data:** none (pure marketing). Optional: Stripe price-ID lookup for geo pricing.
- **States:** loaded (default), loading (Stripe redirect), error (Stripe init failed).
- **A11y:** `<h1>` product name; `main` landmark; price CTA has `aria-describedby` pointing to refund hint.
- **Responsive:** `xs` stacked, `lg` two-column hero.
- **Events:** `landing.view`, `pricing.cta.click`, `faq.expand`.
- **Flags:** `landing.heroVariant` (A/B).
- **Budget:** LCP ≤ 2.0 s on 4G mid-tier mobile.

### 3.2 `/buy/return` — Post-checkout

- **Purpose:** reassure the buyer and capture the emailed license.
- **Entry:** Stripe redirect after successful checkout (`?session_id=...`).
- **Exit:** `/app` once license validated; `/unlock` if session_id invalid.
- **Data:** Stripe session verification via Worker; license token from Stripe receipt email.
- **States:** waiting-for-webhook (poll 3× 10 s), success, timeout (manual entry), failure.
- **A11y:** progress bar has `role="progressbar"` + live-region status announcements.
- **Responsive:** single column all viewports.
- **Events:** `checkout.return`, `checkout.licenseObserved`, `checkout.timeout`.

### 3.3 `/unlock` — License entry

- **Purpose:** paste license from email → unlock `/app`.
- **Entry:** email link (`/unlock?t=<token>`), manual navigation, `/buy/return` timeout.
- **Exit:** `/app` on success; `/legal/terms` or help link on failure.
- **Data:** public Ed25519 verify key (bundled).
- **States:** empty, validating, valid, invalid, revoked.
- **A11y:** input `<label>` "License code"; error as `aria-live="polite"`.
- **Responsive:** full-width form on `xs`; centered card on `md+`.
- **Events:** `unlock.attempt`, `unlock.success`, `unlock.failure:{reason}`.

### 3.4 `/app` — Library (piece picker)

- **Purpose:** pick a piece to practice.
- **Entry:** from `/unlock` success, from previous session, deep link.
- **Exit:** `/app/piece/:id`.
- **Data:** bundled corpus metadata; Dexie: last-practiced timestamps; MIDI device status.
- **States:** loaded (default), empty-corpus (should never happen), device-missing-warning, offline-cached-only.
- **A11y:** `<h1>Library`; pieces in `<ul role="list">`; cards are `<a>` (keyboard-navigable).
- **Responsive:** 1 col `xs`, 2 col `sm`, 3 col `md`, 4 col `lg+`.
- **Events:** `library.view`, `library.piece.select`.
- **Budget:** TTI ≤ 2.5 s.

### 3.5 `/app/piece/:id` — Piece detail

- **Purpose:** preview a piece and start practice.
- **Entry:** from Library.
- **Exit:** `/app/practice/:id` (primary), `/app` (back).
- **Data:** piece metadata, fingering manifest, reference-recording hint.
- **States:** loaded, samples-loading (progress bar), samples-failed.
- **A11y:** `<h1>` piece title; difficulty chip has `aria-label="Grade 5"`.
- **Responsive:** single column all viewports.
- **Events:** `piece.view`, `piece.practice.start`.

### 3.6 `/app/practice/:id` — Practice session (Guided)

- **Purpose:** play through the piece with score-follower hand-holding.
- **Entry:** from piece detail, from "resume" card in Library.
- **Exit:** on completion → `/app/session/:id/summary`; "Quit" → `/app`.
- **Data:** MusicXML, reference MIDI, fingering manifest, piano sample set (Lite → Full).
- **States:** preparing, ready-awaiting-gesture (audio unlock button), playing, paused, wrong-note-feedback, completed.
- **Error budget:** MIDI device disconnect → "Reconnect" toast, session continues in listen-only. Sample-load failure → degrade to Lite → warn.
- **A11y:** live-region announces "correct" / "wrong note — try again" sparingly (not per-note — batch by bar). Keyboard shortcuts visible in `?` overlay.
- **Responsive:** `xs`/`sm` → stacked (score top, practice bar sticky bottom); `lg+` → two-pane (score left, controls right).
- **Events:** `practice.start`, `practice.pause`, `practice.resume`, `practice.complete`, `practice.note.wrong` (sampled, not per-note).
- **Flags:** `practice.lane.defaultGuided`.
- **Budget:** audio unlock → first note p95 ≤ 200 ms; audio latency p95 ≤ 30 ms (wired MIDI).

### 3.7 `/app/practice/:id?lane=studio` — Practice session (Studio)

- **Purpose:** advanced practice with section loop, tempo ramp, hands-separate, blind play.
- **Entry:** lane toggle from Guided or user setting.
- **Exit:** same as Guided.
- **Data:** same as Guided + voice-analysis cache for hands-separate.
- **States:** same as Guided + `ramp-active`, `loop-AB-set`, `blind-play-on`.
- **A11y:** every studio control has a keyboard shortcut + visible label.
- **Responsive:** `lg+` recommended; `xs`/`sm` renders but warns that Studio is easier on larger viewports.
- **Events:** `practice.lane.switch`, `practice.studio.loop.set`, `practice.studio.tempoRamp`, `practice.studio.blind`.

### 3.8 `/app/session/:id/summary` — Session summary

- **Purpose:** close the loop emotionally; capture a rating for future SRS (Phase 2).
- **Entry:** auto after practice completion.
- **Exit:** "Practice again" → `/app/practice/:id`; "Library" → `/app`.
- **Data:** session timings from in-memory store.
- **States:** default. No error state — data is in-memory, session can always summarize.
- **A11y:** ordered stats in `<dl>`; heatmap has textual summary for SR users.
- **Responsive:** single column all viewports.
- **Events:** `session.summary.view`, `session.rating.submit`.

### 3.9 `/app/settings` — Settings

- **Purpose:** adjust audio, MIDI, theme, locale, export.
- **Tabs:** Audio | MIDI | Appearance | Language | Data.
- **Entry:** header icon, keyboard `g s`.
- **Exit:** back button; changes auto-save with toast confirmation.
- **Data:** Dexie settings store.
- **States:** default, saving, saved.
- **A11y:** tabs use `role="tablist"`; device-test plays an A4 tone; SR announces "Tone played".
- **Responsive:** tabs on `md+`; accordion on `xs`/`sm`.
- **Events:** `settings.tab.change`, `settings.update:{key}`.

### 3.10 `/app/settings/export` — Export / Import

- **Purpose:** GDPR Art. 20 portability + DR.
- **Data:** full Dexie dump (minus samples).
- **States:** idle, exporting (spinner + size), exported (download triggered), importing, import-error (schema version mismatch), import-conflict (existing data present).
- **A11y:** confirm dialog on destructive import.
- **Events:** `data.export.click`, `data.export.success`, `data.import.click`, `data.import.success`, `data.import.failure`.

### 3.11–3.15 Legal / static pages

All legal pages:
- **Entry:** footer links, email links, `/.well-known/security.txt`.
- **Data:** static MD content compiled at build; TR and EN per locale.
- **States:** loaded.
- **A11y:** semantic `<article>`, heading order, plain-language summary block at top.
- **Responsive:** single column max-width `65ch`.

### 3.16 `/404` — Not found

- **Purpose:** inform + return.
- **A11y:** `<h1>404 — Not found`; primary action "Go home".

### 3.17 `/offline` — Offline shell

- **Purpose:** SW fallback when the SPA route is unknown AND network is absent.
- **Data:** Dexie list of cached pieces.
- **A11y:** explanatory heading + cached-piece list as `<ul>`.

### 3.18 `/sprint0/landing` — Phase-0 ad landing

- **Purpose:** Phase-0 CAC + intent test. **Disposable.**
- **Data:** email service API + Plausible event.
- **States:** idle, submitting, success, failure.
- **A11y:** same rigor as product landing — this is public-facing copy.
- **Events:** `sprint0.view`, `sprint0.email.submit`, `sprint0.intent.click`.

### 3.19 `/sprint0/proto` — Phase-0 prototype

- **Purpose:** technical feasibility probe. **Disposable.** Noindexed.
- **Data:** 2 MusicXML, Lite samples, WebMIDI device list.
- **States:** audio-locked, ready, playing, paused, midi-disconnected.
- **A11y:** full — even disposable code holds the a11y bar.
- **Events:** none in production Plausible; local-only `console.debug`.

---

## 4. Phase-2 Additions (preview, not shipped)

| Route | Purpose |
|---|---|
| `/auth/sign-in`, `/auth/sign-up` | Supabase Auth flows |
| `/app/profile` | Account settings |
| `/app/progress` | Cross-piece analytics |
| `/app/review` | FSRS review queue |
| `/app/discover` | Expanded catalog |
| `/legal/cookies` | Cookie preferences |

Every Phase-2 addition opens an ADR amendment if it requires design-system additions.

---

## 5. Phase-3 Additions (preview)

Teacher tooling, subscription billing, multi-device sync UI. Deferred pending G2/G3.

---

## 6. Quality Gates per Screen

Every screen in CI must:
- Pass axe-core (zero violations).
- Hit Lighthouse budget (see `MASTER-PLAN.md §4.3`).
- Have at least one Playwright E2E touching it (critical-path screens only — landing, unlock, app, practice, settings).
- Have a Storybook story for each state listed.

---

## 7. References

- [`INFORMATION-ARCHITECTURE.md`](INFORMATION-ARCHITECTURE.md)
- [`USER-JOURNEY-MAP.md`](USER-JOURNEY-MAP.md)
- [`WIREFRAMES.md`](WIREFRAMES.md)
- [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md)
- [`RESPONSIVE-STRATEGY.md`](RESPONSIVE-STRATEGY.md)
- [`ACCESSIBILITY.md`](ACCESSIBILITY.md)
- `MASTER-PLAN.md §2.4`, §4.3
