# Ad Creatives Brief — Phase 0

> **Scope:** 5 Meta (Facebook/Instagram) + 1 Google Ads creative set for the 7-day Phase-0 paid run.
> **Budget:** €500 Meta + €300 Google = €800, 7 days, caps at €110/day.
> **Authority:** `MASTER-PLAN.md §8.1`, `../decisions/PHASE0-SETUP.md §4`.
> **Governing constraints:** ADR-017 (16+ only), ADR-018 (TR + EU only), age confirmation in terms, no live offer.

---

## 1. Audience Definition

### 1.1 Meta (Facebook + Instagram)

| Audience | Geo | Age | Interests | Language |
|---|---|---|---|---|
| A — TR adult piano learners | Türkiye | 20–60 | Piano, Bach, classical music, music theory, MIDI, digital piano brands (Yamaha, Kawai, Roland), music schools | TR |
| B — DE/EU adult piano learners | DE, NL, AT, FR, IT, ES, BE, IE, SE, DK, FI | 22–60 | Same as A, plus ABRSM, RCM, Henle Verlag, Faber Music | DE + EN (dual) |

**Exclusion filters (mandatory):**
- Exclude under-20s (2-year buffer above ADR-017's 16+ gate, for ad-audience safety).
- Exclude US, UK, BR, CA, AU, JP, CN.
- Exclude "parent of young children" interest clusters.

### 1.2 Google Ads

Keyword-driven (search intent), not audience-interest-driven.

- TR: `piyano pratik tarayıcı`, `online piyano çalış`, `bach envansiyon nota`, `midi piyano uygulaması`, `piyano parmak önerisi`.
- EN (targeting EU languages): `piano practice browser`, `online piano practice`, `bach inventions sheet music`, `midi piano practice tool`, `piano fingering suggestions`.
- DE: `klavier üben browser`, `bach zweistimmige inventionen noten`, `online klavier üben`.

Negative keywords: `kids`, `children`, `family`, `toy`, `lesson plan`, `school curriculum`, `job`, `career`.

---

## 2. Meta Creative Set (5 variants)

All lead to `/sprint0/landing`. All creatives have matching TR + DE + EN variants; each counts as one "creative" even if it ships in 2 languages.

### M-01 — "Real notation, real sound, in a tab"

- **Format:** Static image with notation close-up.
- **Hero image:** high-resolution close-up of BWV 772 score (Bach Inventions), with a soft AI-fingering overlay on bar 3 highlighting "1-2-3" fingers.
- **Copy (EN):** *Real engraved notation. Real sampled Steinway. Real MIDI. In a browser tab. Coming soon.*
- **Copy (TR):** *Gerçek notalar. Gerçek Steinway örneklemesi. Gerçek MIDI. Tarayıcı sekmesinde. Yakında.*
- **CTA:** `Get notified` / `Bilgilendir`.

### M-02 — "Score-follower, not metronome"

- **Format:** Short animated GIF / short MP4 (≤ 6 s, 9:16 for Reels / 1:1 for Feed).
- **Animation:** cursor advancing across score, correct notes highlighted in green, one wrong note pulsing red briefly.
- **Overlay text:** "Score-follower. Not a metronome."
- **Copy:** *A practice companion that knows the difference between a wrong note and a late note. Coming soon.* / *Hatalı notayı geç notadan ayırt eden bir pratik arkadaşı. Yakında.*

### M-03 — "Bach, one tab"

- **Format:** Static image, composer-portrait + typographic.
- **Visual:** a simple, sober Bach portrait (public domain) with a minimalist overlay: 15 thin rectangles representing the 15 Inventions.
- **Copy (EN):** *15 Bach Two-Part Inventions. One browser tab. €27 lifetime, once we launch.*
- **Copy (DE):** *15 Bach zweistimmige Inventionen. Ein Browser-Tab. €27 einmalig, wenn wir starten.*
- **CTA:** `Notify me` / `Benachrichtige mich`.

### M-04 — "Paying for practice tools, like sheet music"

- **Format:** Static, typographic (no imagery).
- **Layout:** big statement headline, small sub.
- **Headline (EN):** *We think pianists should pay for practice tools the way they pay for sheet music — once.*
- **Sub:** *A browser practice studio. €27 lifetime. Coming soon.*
- **Headline (TR):** *Piyanistler, pratik araçlarına nota defteri alır gibi — tek seferlik — ödemeli.*
- **Sub:** *Tarayıcı pratik stüdyosu. €27 ömür boyu. Yakında.*

### M-05 — "No app. No account."

- **Format:** Static, minimalist.
- **Visual:** subtle graphic of a browser chrome with a rendered score inside; no phone UI, no app icon.
- **Copy (EN):** *No app. No account. Just the score and your hands. Coming soon in Türkiye and the EU.*
- **Copy (TR):** *Uygulama yok. Hesap yok. Sadece nota ve elleriniz. Türkiye ve AB'de yakında.*

---

## 3. Google Ads Set

Single Search campaign, 3 ad groups, 2 responsive search ads each (headlines + descriptions that Google rotates).

### G-Group A — TR Piano practice (TR keywords)

**Headlines (15 variants, Google picks combinations):**
1. Tarayıcıda Piyano Pratiği
2. Gerçek Notalar, Gerçek MIDI
3. Bach İki-Sesli Envansiyonlar
4. Ömür Boyu, Abonelik Yok
5. Yakında — Bilgilendirme Listesi
6. Bach Envansiyon Pratiği
7. Hatalı Notayı Algılar
8. AI Parmak Önerisi
9. Public-Domain Edisyonlar
10. Türkiye'de Yakında
11. €27 Ömür Boyu
12. Uygulama Yok Hesap Yok
13. Piyano Pratik Stüdyosu
14. Web'de Piyano Pratiği
15. 16+ Yaş, TR + AB

**Descriptions (4 variants):**
- Gerçek notalarla, gerçek Steinway sesiyle, gerçek MIDI girişiyle. Tarayıcınızda.
- 15 Bach Envansiyonu, AI parmak önerileri, score-follower. Yakında.
- Tek seferlik €27 lisans. Abonelik yok. Türkiye'de ve AB'de.
- Bilgilendirme listesine e-postanızı bırakın. Lansmanda bir kez e-posta.

### G-Group B — EN Piano practice (EN keywords, EU-targeted)

**Headlines (15):**
1. Browser-Based Piano Practice
2. Real Score, Real Piano, Real MIDI
3. Bach Inventions, One Browser Tab
4. Lifetime License, No Subscription
5. Coming Soon in Türkiye and EU
6. AI Fingering, Public Domain
7. Score-Follower with Chord Logic
8. €27 Once. That's It.
9. No App, No Account
10. Practice Companion for Adults
11. Engraved Notation, Not Cartoons
12. Steinway Samples in Browser
13. Sign Up for Launch Email
14. Serious Practice Tool
15. 16+ Only, TR + EU

**Descriptions (4):**
- Real engraved notation, sampled Steinway, MIDI input — in a browser tab. Coming soon.
- 15 Bach Two-Part Inventions with AI + editorial fingering. One-time €27 lifetime.
- No app, no account. For serious adult learners. Türkiye and EU only.
- Leave your email — single launch update email, then silence.

### G-Group C — DE Piano practice

(Same structure as Group B, in German.)

---

## 4. Daily Monitoring Checklist (founder, ~25 min/day during the 7-day run)

- [ ] Spend vs. cap per platform (pause at 125% of daily cap).
- [ ] CTR ≥ 1.0% on Meta by Day 2 (if not, pause under-performing creatives).
- [ ] CPA running (signups / spend) trending ≤ €15.
- [ ] Intent-click rate on landing ≥ 3% Day 1, trending to ≥ 5% by Day 5.
- [ ] No spikes in negative feedback (Meta "Hide ad" score).
- [ ] No policy violations surfaced by platform.
- [ ] Legal-footer compliance re-checked after any copy edit.

Log daily in `docs/research/ADS-LOG.md` (daily append).

---

## 5. Creative Production

- **Images**: produced in a free tool (Figma free tier or Photopea). No stock photography (brand rule; avoids EU likeness-rights risk). Export PNG 1080 × 1080 Feed / 1080 × 1920 Reels / 1200 × 628 Link.
- **Animation (M-02)**: screen-recorded from the prototype once WS4-T03 passes; 6-second MP4 export at 2× speed. Keep ≤ 1 MB.
- **Typography**: Inter Bold for headlines, Inter Regular for body. Brand colours per `../design/DESIGN-SYSTEM.md §3`.
- **No third-party fonts loaded in the ad creative** (they're baked into the image).

---

## 6. Approval Review Before Upload

- [ ] Every creative passes the "16+ only" check.
- [ ] Every creative passes the "TR + EU only" check.
- [ ] Every creative includes the price context ("Coming soon" or "Lifetime €27") — not a live offer.
- [ ] Every creative's language matches its targeting language.
- [ ] UTM tagging configured: `utm_source={meta|google}&utm_medium=cpc&utm_campaign=sprint0&utm_content=<creative-id>`.

---

## 7. References

- `MASTER-PLAN.md §8.1` (Sprint 0 paid test)
- [`LANDING-COPY.md`](LANDING-COPY.md)
- [`../decisions/PHASE0-SETUP.md §4`](../decisions/PHASE0-SETUP.md) (analytics)
- ADR-017, ADR-018
