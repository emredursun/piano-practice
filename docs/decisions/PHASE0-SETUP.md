# Phase-0 Setup Decisions (Day-1 Ambiguity Resolution)

- **Status:** DECIDED (by Senior Staff Engineer authority, 2026-04-22).
- **Scope:** The 8 ambiguities flagged by `/plan` that block Week-1 execution of Phase 0.
- **Authority:** `MASTER-PLAN.md §2.3`, §8.1, §9.2; [Phase-0 Validation Sprint plan output](../research/PHASE0-PLAN-NOTES.md).
- **Reversibility:** Every decision here is reversible at low cost during Phase 0. A different decision at Phase-1 is encouraged if evidence emerges.

---

## 1. Legal Entity (ad billing + KVKK/GDPR controller)

**Decision:** Personal capacity (founder as natural-person controller) for Phase 0; **switch to TR şahıs şirketi within 2 weeks of a G1 PASS**.

**Rationale:**
- Phase 0 spend is ≤ €2k (ads + interview incentives + pedagogue fees). No tax-invoicing complexity at this scale.
- Audit notes a 40 %+ probability of G1 repositioning/sunset; entity setup would be wasted if that path triggers.
- KVKK/GDPR compliance for a natural-person controller is legally sufficient — footer publishes controller identity (name + contact) plus KVKK Aydınlatma Metni.
- Switching post-G1 costs ~€30 + 1 week and preserves professional posture for Phase 1 launch.

**Actions for Week 1 Day 1:**
- Landing page legal footer: "Controller: <Founder Full Name>, <City>, TR. Contact: privacy@…".
- Privacy notice (TR + EN) names the natural-person controller.
- Bookkeeping: keep all Phase-0 receipts in a single folder; reconcile into şahıs şirketi books after G1.

**Reversibility:** trivial — entity registration can start immediately after G1 with no data migration.

---

## 2. Email Capture Service

**Decision:** **MailerLite EU** (free tier up to 1,000 subscribers).

**Rationale vs. alternatives:**
- **Buttondown** — primarily US-hosted; GDPR adequacy decision exists but EU data residency weaker.
- **Beehiiv** — newsletter-first; overkill and US-first.
- **ConvertKit** — creator-pricing, premium tier needed too quickly.
- **MailerLite EU** — EU data residency (GDPR), free ≤ 1,000 subscribers, native double opt-in, first-class API + webhook, reasonable Turkish locale support, published sub-processor list.

**Actions for Week 1 Day 1:**
- Create MailerLite EU account with personal-capacity billing.
- Configure a single subscriber group "Sprint-0".
- Generate API key + webhook URL; store as `MAILERLITE_API_KEY` in `.env.local` (gitignored).
- Double opt-in template: customise subject line to brand voice (see `../design/UX-COPY.md`).

**Reversibility:** export subscribers as CSV at any time; provider-neutral.

---

## 3. Landing-Page Build Path

**Decision:** **Hand-rolled React + Vite + TS (strict)** skeleton — matches ADR-001; same skeleton seeds the prototype (WS4).

**Rationale:**
- No-code tools (Carrd, Framer, Webflow) save ~8–12 h but introduce a throwaway vendor, weaker CSP control, weaker a11y defaults, and zero skeleton reuse for WS4.
- Our Phase-0 landing is public-facing ad traffic — it needs the same a11y + privacy bar as Phase 1.
- The 6-hour overhead is recovered in WS4 (prototype uses the same Vite config, design tokens, component primitives).

**Actions for Week 1 Day 1–2:**
- Scaffold `apps/sprint0/` (Vite + React + TS strict) inside the monorepo (or at repo root if monorepo not yet set up — decided at Week-1 Day 1 alongside `package.json` init).
- Brand primitives from [`../design/DESIGN-SYSTEM.md`](../design/DESIGN-SYSTEM.md); colours / type tokens scoped to landing initially.
- Deploy to Cloudflare Pages production domain (ads point to production, not preview per P0-R9).

**Reversibility:** the React skeleton is ~150 LOC at Phase-0 scope; replaceable if needed.

---

## 4. Analytics

**Decision:** **Plausible EU-hosted** (starter paid tier ~€9/mo during Phase 0, or 30-day trial).

**Rationale:**
- **Cloudflare Web Analytics** is excellent for traffic but lacks first-class **custom events** — which we need to compute CPA against *intent-to-pay* (the G1-C2 criterion). CPA from CF CAN be derived but cross-referencing with email-submit and intent-CTA events is messy.
- **Plausible EU** — EU-hosted (Germany), cookieless, privacy-preserving, custom events via JS API, exportable CSV for offline analysis.

**Actions for Week 1 Day 1:**
- Plausible account + site `piano-practice.<tld>`.
- Events to instrument:
  - `landing.view`
  - `landing.email.submit`
  - `landing.intent.click` (with `variant = yes|maybe|no`)
  - `landing.faq.expand`
- UTM parameters: `utm_source`, `utm_medium`, `utm_campaign`, `utm_content` captured and attributed.

**Reversibility:** event instrumentation is additive; CF Web Analytics stays on in parallel as a privacy-minimal baseline.

---

## 5. Video-Call Tool (customer discovery interviews)

**Decision:** **Whereby** free tier — EU-hosted, browser-based (zero install for participants), 45-min session cap fits 30-min interviews with buffer.

**Rationale vs. alternatives:**
- **Zoom** — US-hosted; SCC + explicit KVKK cross-border consent complexity for TR participants. Adds legal overhead at Phase 0.
- **Jitsi Meet (meet.jit.si)** — open, EU, but UX inconsistent, occasional audio issues on free instance.
- **Whereby** — EU (Norway), polished UX, browser-only, Free tier supports our scale.

**Recording:** **OS-level screen recording with explicit written consent** (see [`../research/INTERVIEWS-PHASE0/CONSENT-FORM.md`](../research/INTERVIEWS-PHASE0/CONSENT-FORM.md)) rather than Whereby's paid tier. Recordings stored in `docs/research/INTERVIEWS-PHASE0/RAW/` (gitignored). Retention: 90 days, then deletion.

**Actions for Week 1 Day 1:**
- Whereby workspace at `<subdomain>.whereby.com`.
- Permanent room URL attached to every outreach email.
- QuickTime (macOS) / OBS (Windows/Linux) configured for consent-gated local recording.

**Reversibility:** interviews can switch to Jitsi fallback mid-sprint if Whereby underperforms.

---

## 6. Participant Compensation

**Decision:**

| Segment | Amount | Mechanism |
|---|---|---|
| Piano teachers (TR + DE) | **€40 Amazon gift** (Amazon.tr for TR, Amazon.de for DE) | Instant, no KYC |
| Adult self-learners (TR + EU) | **€25 Amazon gift** (localised per country) | Instant, no KYC |
| Advanced amateurs / pre-professionals | **€25 Amazon gift** | Instant |
| ABRSM/RCM pedagogues (paid consultation) | **€120/h** (1-hour slot) | **Wise transfer** against professional invoice |

**Rationale:**
- Amazon gifts: under the €50-per-gift threshold in most EU jurisdictions → no withholding. Recipient choice + instant delivery + universal usability.
- Teachers compensated higher (€40) because teacher time has higher opportunity cost.
- Pedagogues are **paid consultation**, not interview — respects their professional status and dramatically improves reply rate. Wise transfer with invoice aligns to how they normally bill.

**Actions for Week 1 Day 1:**
- Open Wise Business account (if not existing) for pedagogue payments.
- Pre-purchase 15 × €25 Amazon.tr/de gift codes + 5 × €40 Amazon.tr/de as inventory.
- Budget envelope: 15 × ~€30 avg = €450 + 3 × €120 = €360 = **€810 total** — within §9.2 envelope.

**Reversibility:** any unspent gift codes carry over to Phase 1 for customer research.

---

## 7. Test Hardware Inventory

**Decision (minimum required for credible G1-C5 pass):**

| Device class | Minimum | Purpose |
|---|---|---|
| macOS laptop (any Apple Silicon, macOS 13+) | 1 | Safari + Chrome; baseline |
| Windows laptop (Win 10/11) | 1 | Edge + Chrome + Firefox |
| iOS device (iPhone or iPad, iOS 17+, Safari) | 1 | autoplay policy, WebAudio quirks, no WebMIDI (documented) |
| Android phone (Android 12+, Chrome) | 1 | WebMIDI-capable secondary |
| USB-wired MIDI keyboard (any, GM-compatible) | 1 | G1 latency test gold path |

**Ideal (extends confidence but not required):**

- Linux laptop (Ubuntu 22.04+) — catches Firefox-specific MIDI edge cases.
- Bluetooth MIDI device — lets us characterise BLE latency for the R-07 warning.

**This is a FOUNDER-TBD confirmation item.** Please reply with your actual available hardware; any gap from the "minimum required" set becomes a documented limitation in the G1 decision record (not a G1 failure).

**If any device is missing:**

- **No iOS** → G1 cannot verify iOS autoplay path → G1 PARTIAL-PASS with explicit scope note; plan 1 day to borrow / test on BrowserStack before Phase-1 Week 4.
- **No Android** → same posture; WebMIDI broadly available so lower risk.
- **No Windows** → harder to skip (largest user base); borrow if needed.
- **No MIDI device** → **blocker** for G1-C5; order immediately. A basic USB MIDI keyboard (Akai LPK25, Alesis V25) costs ~€50 and is reusable forever.

**Actions for Week 1 Day 2:**
- Founder confirms inventory in a reply; this section updates from TBD to CONFIRMED.
- Any missing device has a named acquisition plan or documented gap with G1 scope note.

---

## 8. Pedagogue Outreach Source

**Decision:** Parallel three-track outreach, **15 messages Day 1–2**:

- **Track A — LinkedIn search (6 messages):** query combinations "ABRSM" / "RCM" + "piano teacher" in TR + DE + UK.
- **Track B — EPTA national chapters (5 messages):** contact DE, NL, UK EPTA member listings (public).
- **Track C — Personal network (4 messages):** founder's direct music contacts / conservatoire alumni.

**Rationale:**
- 15-message pipeline produces **3 paid consultations** at a 20 % reply rate (industry standard for cold B2B with a paid offer).
- Three tracks de-risk single-channel failure (P0-R2).
- Personal network is fastest but thinnest — the two external tracks scale the sample.

**Offer text (in outreach email):**
> "I'm running a 1-hour paid consultation (€120, Wise/PayPal) for ABRSM/RCM-certified pedagogues to quote licensing vs. authoring cost for a 1-grade-level piano curriculum. Short, structured, no follow-up obligation."

**Actions for Week 1 Day 1–2:**
- Draft LinkedIn + EPTA + personal-network templates ([`../research/INTERVIEWS-PHASE0/OUTREACH-TEMPLATES.md`](../research/INTERVIEWS-PHASE0/OUTREACH-TEMPLATES.md)).
- Send all 15 Day 1–2; track reply status in `docs/research/INTERVIEWS-PHASE0/OUTREACH-LOG.md` (founder-maintained).
- If < 20 % reply by Day 5, double outreach (additional 15 messages).

---

## Decision Summary Table

| # | Area | Decision | Owner | Reversibility |
|---|---|---|---|---|
| 1 | Legal entity | Personal now → TR şahıs şirketi post-G1 | Founder | Trivial |
| 2 | Email capture | MailerLite EU (free tier) | Founder | CSV export anytime |
| 3 | Landing build | React + Vite + TS strict (shared with WS4) | Founder | Low |
| 4 | Analytics | Plausible EU + CF Web Analytics baseline | Founder | Additive |
| 5 | Video call | Whereby Free + OS-level consent recording | Founder | Fallback Jitsi |
| 6 | Compensation | €25/€40 Amazon gift + €120/h Wise for pedagogues | Founder | Gift codes carry over |
| 7 | Hardware inventory | Min macOS + Win + iOS + Android + wired MIDI | **FOUNDER-TBD** | Borrow / BrowserStack fallback |
| 8 | Pedagogue outreach | LinkedIn + EPTA + personal (15 Day 1–2) | Founder | Double on low reply |

---

## Downstream Artefacts (Day-1 Created)

- [`../research/INTERVIEWS-PHASE0/CONSENT-FORM.md`](../research/INTERVIEWS-PHASE0/CONSENT-FORM.md) — TR + EN bilingual
- [`../research/INTERVIEWS-PHASE0/INTERVIEW-GUIDE.md`](../research/INTERVIEWS-PHASE0/INTERVIEW-GUIDE.md) — JTBD structure
- [`../research/INTERVIEWS-PHASE0/OUTREACH-TEMPLATES.md`](../research/INTERVIEWS-PHASE0/OUTREACH-TEMPLATES.md) — 4 templates (teacher TR, teacher DE, self-learner, pedagogue)
- [`../sprint0/LANDING-COPY.md`](../sprint0/LANDING-COPY.md) — final landing copy (TR + EN)
- [`../sprint0/AD-CREATIVES-BRIEF.md`](../sprint0/AD-CREATIVES-BRIEF.md) — Meta + Google creative brief
- [`G1-DECISION.md`](G1-DECISION.md) — Week-3 decision-record template, pre-structured

---

## Changelog

- **2026-04-22 — Initial record.** Decisions 1–6, 8 resolved by Senior Staff Engineer authority. Decision 7 pending founder hardware inventory confirmation.
