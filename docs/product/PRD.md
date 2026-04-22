# Product Requirements Document — Piano-Practice (Phase 1 MVP)

> **Scope:** Feature-level requirements for Phase 1 only. Strategic framing lives in [`MASTER-PLAN.md`](../MASTER-PLAN.md).
> **Authority:** `MASTER-PLAN.md §2.4` (Phase 1 scope), ADR-011, ADR-019. This document operationalises them.
> **Status:** Baseline. Amendments require product owner + ADR if scope changes.

---

## 1. Product Overview

### 1.1 One-sentence definition

A browser-based practice studio that lets adult pianists practise 15 Bach Two-Part Inventions with real engraved notation, real sampled piano audio, real MIDI input, score-following, and AI fingering suggestions — for a one-time $29 lifetime license.

### 1.2 Target user (Phase 1)

- Adults **16+** (ADR-017), resident in Türkiye or EU/EEA (ADR-018).
- Three personas: returning amateur, serious self-learning beginner, pre-professional (see [`design/USER-JOURNEY-MAP.md`](../design/USER-JOURNEY-MAP.md)).

### 1.3 Non-goals (Phase 1)

Negative scope is as important as positive scope. See [`CLAUDE.md §8`](../../CLAUDE.md) for the full Hard NO list.

- No under-16 audience.
- No US / UK / BR launch.
- No user accounts or subscription.
- No multi-device sync.
- No user uploads.
- No runtime AI personalisation.
- No non-Bach repertoire.

---

## 2. Feature Set (Phase 1)

### F-01 Purchase & License

**User story:** As a qualified visitor, I want to buy a lifetime license in one click so I can start practising immediately.

**Requirements:**
- Stripe Checkout (hosted) with geo-priced SKUs: $29 / TRY 999 / €27.
- 14-day refund policy (GDPR distance-selling baseline).
- Ed25519-signed license token emailed on successful checkout.
- Client-side license verification; no login.
- License recovery via email lookup (rate-limited).

**Out of scope Phase 1:** subscriptions, bundles, gift licenses, teacher seats.

**Acceptance criteria:**
- Checkout → license email ≤ 60 s p95.
- Invalid license displays actionable error; valid license grants `/app` access.
- Refund triggers revocation on next app open.

---

### F-02 Library & Piece Selection

**User story:** As a licensed user, I want to browse the 15 pieces and see which I've practised recently so I can pick one and begin.

**Requirements:**
- Grid of 15 Bach Inventions with title, BWV number, difficulty (ABRSM grade), length (bars), last-practised timestamp.
- "Resume" card at top of Library if last practised within 14 days.
- Filtering by difficulty and last-practised age (nice-to-have; defer if schedule pressure).
- Offline: cached pieces labelled "available offline"; un-cached labelled with size and "requires connection".

**Acceptance criteria:**
- Library renders p95 LCP ≤ 2.5 s on 4G mobile.
- Clicking a piece opens detail + "Practise" CTA.

---

### F-03 Practice Session — Guided Lane

**User story:** As a serious-beginner pianist, I want a practice mode that holds my hand just enough (score-follower, visible fingering, forgiving wrong-note feedback) so I can learn a new piece without a teacher present.

**Requirements:**
- OSMD score rendering.
- MIDI input via WebMidi.js.
- Tone.js + @tonejs/piano + Salamander Lite (5 MB) audio.
- Score-follower with 50 ms chord aggregation.
- Correct-note highlight + wrong-note toast (sampled, not per-note).
- AI fingering overlay (Parncutt 1997 via pianoplayer, build-time).
- Editorial fingering toggle (Czerny / Busoni).
- Play / pause / restart.
- Tempo control (50–120 %).
- Session auto-saves to Dexie on completion.

**Acceptance criteria:**
- MIDI → audio latency p95 ≤ 30 ms on wired device.
- Score-follower match accuracy ≥ 95 % on clean playthrough (internal test corpus).
- Session summary renders on completion.

---

### F-04 Practice Session — Studio Lane

**User story:** As a pre-professional pianist, I want advanced practice tools (section loop, hands-separate, tempo ramp, blind play) so I can drill the parts of a piece that are hard for me.

**Requirements:**
- Everything from Guided Lane, plus:
  - Section loop A/B, visible selection.
  - Hands-separate (left-only / right-only / both), via voice-analysis on MusicXML.
  - Tempo ramp (start at `T1` end at `T2` over `N` iterations).
  - Blind play (hide notation below cursor).
  - Metronome on/off.

**Acceptance criteria:**
- Hands-separate correctly isolates voices for all 15 Phase-1 pieces (visual + audible test).
- Tempo ramp completes without drift > 5 ms.
- Blind play restores notation on pause.

---

### F-05 Session Summary

**User story:** As any user, I want a calm summary after practising so I know how I did and feel motivated to come back.

**Requirements:**
- Accuracy % across bars.
- Per-bar accuracy sparkline.
- Session duration.
- 90-day practice heatmap.
- Optional rating ("Again / Hard / Good / Easy") — **stored** but **not used** in Phase 1 (seeds Phase-2 FSRS).
- "Practise again" and "Back to library" CTAs.

**Acceptance criteria:**
- Summary renders within 800 ms of session completion.
- Heatmap updates before summary renders.

---

### F-06 Settings

**User story:** As any user, I want to adjust audio, MIDI, theme, locale, and export my data.

**Requirements:**
- Audio: volume, Lite ⇄ Full sample set, mute.
- MIDI: device picker, permission re-request, test-tone button.
- Appearance: theme (Dark / Light / Auto), reduced-motion switch.
- Language: Turkish / English.
- Data: JSON export / import (see F-07).
- Auto-save with toast confirmation.

**Acceptance criteria:**
- All settings persist across sessions.
- Theme + locale switch live without reload.

---

### F-07 JSON Export / Import (Portability & DR)

**User story:** As a privacy-conscious user, I want to export my practice history and settings to JSON so I can back up or move devices.

**Requirements:**
- Export includes: settings, practice sessions, license reference (not secret), override fingerings.
- Export **excludes** samples (they re-download on import).
- Import is destructive — replaces current state after confirmation.
- Schema version embedded; import warns on version mismatch.

**Acceptance criteria:**
- Export size < 5 MB for a year of daily practice (validated via fixture).
- Import replays successfully on a fresh browser.
- Mismatched schema version blocks import with a helpful upgrade path message.

---

### F-08 PWA & Offline

**User story:** As a user with unreliable connectivity, I want to practise without internet after I've used the app once.

**Requirements:**
- Service Worker precaches app shell + corpus metadata.
- Samples (Lite) cached on first piece-play; persist offline.
- Samples (Full) opt-in only.
- Offline banner + `/offline` fallback.
- Install prompt (per-platform) on second visit or post-first-session.

**Acceptance criteria:**
- Cold offline load of a previously-practised piece: p95 LCP ≤ 1.5 s.
- No regression in TTI vs. online load.

---

### F-09 Legal & Compliance Surface

**User story:** As a user or regulator, I need to see the policies that govern this product.

**Requirements:**
- `/legal/privacy`, `/legal/terms`, `/legal/subprocessors`, `/legal/accessibility`, `/legal/security`.
- TR + EN from launch.
- Footer links present on every screen.
- `/.well-known/security.txt` per RFC 9116.
- 16+ age confirmation in Checkout terms.
- KVKK cross-border consent checkbox for TR users in Checkout.

**Acceptance criteria:**
- All legal pages pass a11y + are available in both locales.
- Accessibility statement conforms to EN 301 549 template.

---

### F-10 Observability (opt-in)

**User story:** As a founder operating the product, I need to see error and latency signals without harvesting user data.

**Requirements:**
- Sentry for exceptions (no PII; source-map uploads via CI).
- OpenTelemetry → Honeycomb for traces (sampled).
- RUM events (anonymous, opt-in) for `audio.latency`, `notation.render.ms`, `midi.roundtrip.ms`, `sample.load.bytes`.
- Cookie-less Cloudflare Web Analytics for traffic.

**Acceptance criteria:**
- No PII in any captured event (verified in CI lint).
- Opt-in toggle visible in Settings → Data.
- SLO burn-rate alerts live.

---

## 3. Feature Dependencies

```
F-01 Purchase ──► F-02 Library ──► F-03/F-04 Practice ──► F-05 Summary
                    │                       │
                    └──► F-06 Settings ─────┤
                           │                │
                           └──► F-07 Export/Import
F-08 PWA ─────────────────────────────────► all
F-09 Legal ──► pre-launch blocker
F-10 Observability ──► runs alongside from day 1
```

---

## 4. Requirements Traceability

Each feature maps to user stories (see [`USER-STORIES.md`](USER-STORIES.md)) and to acceptance tests in Playwright + Vitest.

---

## 5. Launch Criteria (end of Phase 1 Week 15)

All must hold before enabling production traffic on `/`:

- [ ] CI pipeline green (`MASTER-PLAN.md §4.2`).
- [ ] Lighthouse a11y = 100 on all critical screens.
- [ ] Manual SR pass (NVDA, VoiceOver macOS, VoiceOver iOS, TalkBack).
- [ ] TR + EN locales complete; zero missing-key errors.
- [ ] All 15 Bach Inventions pass content QA (`MASTER-PLAN.md §7.4`).
- [ ] CSP Report-Only measured for 2 weeks with zero actionable violations.
- [ ] Stripe Checkout + webhook tested end-to-end in production test mode.
- [ ] Legal pages (privacy, terms, subprocessors, accessibility, security) published.
- [ ] Refund SOP documented and tested.
- [ ] SLO dashboards live; pager enrolled.
- [ ] Phase-0 G1 decision = GO (upstream gate).

---

## 6. Success Metrics (Gate G2 at Week 20)

From `MASTER-PLAN.md §11`:

- ≥ 100 paying customers at $29.
- Refund rate < 10 %.
- NPS ≥ 30.
- Average session ≥ 12 minutes.
- ≥ 1 organic mention in a piano-teacher community.

---

## 7. Open Questions (to be resolved during Phase 1 build)

- Feature flag gradation for Studio lane: default-on for all, or gated behind G1 PASS signal?
- Should Session Summary ask the SRS rating in Phase 1 (to seed Phase-2 FSRS) or defer entirely?
- Should the fingering layer default to AI (Parncutt) or to Editorial (Czerny)? — depends on prototype user-testing in Phase-0.

Each open question resolves via ADR amendment or a product note in [`USER-STORIES.md`](USER-STORIES.md).

---

## 8. References

- [`MASTER-PLAN.md`](../MASTER-PLAN.md) — §2.4, §11
- [`USER-STORIES.md`](USER-STORIES.md)
- [`FEATURE-FLAGS.md`](FEATURE-FLAGS.md)
- [`../design/SCREENS-INVENTORY.md`](../design/SCREENS-INVENTORY.md)
- [`../design/USER-JOURNEY-MAP.md`](../design/USER-JOURNEY-MAP.md)
