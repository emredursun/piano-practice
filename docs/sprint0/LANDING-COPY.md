# Landing Page Copy — `/sprint0/landing` (Phase 0, disposable)

> **Scope:** Final copy for the Phase-0 ad landing page. **Disposable** per ADR-020 — deleted after G1 decision.
> **Authority:** `MASTER-PLAN.md §8.1`; `../design/UX-COPY.md §9.1`; `../decisions/PHASE0-SETUP.md §3`.
> **Constraint review:** every string passes the age-gate check (no under-16 targeting per ADR-017), the geo check (TR + EU only per ADR-018), and the "no live offer" check (Phase 0 does not sell — intent only).

---

## Page Structure (top to bottom)

1. Hero (H1 + sub + intent question + email form)
2. 3 feature tiles (with SVG icons, no photos)
3. FAQ (4 questions)
4. Legal footer

One page. Single column on `xs–sm`; two-column hero on `lg+`. No nav bar.

---

## 1. Hero

### 1.1 EN

**H1 (top of fold):**
> Practise Bach in your browser — coming soon.

**Sub-headline:**
> A new browser-based piano practice studio for serious adult learners. Real engraved notation. Real sampled Steinway. Real MIDI. No apps, no accounts.

**Visual:**
- Auto-playing, muted, 30-second WebM demo of the prototype (fallback: annotated SVG score).
- Caption below video: "Demo recorded on the prototype. Sound off by default — tap to unmute."

**Intent question block:**

> **Would you pay $29 (one-time, lifetime) for this?**
> ( ☐ Yes ) ( ☐ Maybe ) ( ☐ No )

*(Three large buttons; selection fires `landing.intent.click` Plausible event with `variant=<yes|maybe|no>`.)*

**Email capture (under intent):**

> **Email me when it launches**
> [ you@example.com ] [ **Notify me** ]

*(Single field + button. On submit: POST to MailerLite EU API; fire `landing.email.submit` Plausible event; inline success message.)*

**Inline success message (after submit):**

> Thanks — we'll email you once in about 12 weeks when the real thing launches. Unsubscribe is a one-line reply.

**Inline error message (generic):**

> Couldn't save that — try again in a moment.

**Inline error message (invalid email):**

> That email doesn't look valid. Double-check it and try again.

---

### 1.2 TR

**H1:**
> Tarayıcınızda Bach çalışın — yakında.

**Alt başlık:**
> Ciddi yetişkin öğrenciler için tarayıcıda çalışan yeni bir piyano pratik stüdyosu. Gerçek notalar. Gerçek Steinway örneklemesi. Gerçek MIDI. Uygulama yok, hesap yok.

**Niyet sorusu:**

> **$29'a (tek seferlik, ömür boyu) bunu alır mısınız?**
> ( ☐ Evet ) ( ☐ Belki ) ( ☐ Hayır )

**E-posta yakalama:**

> **Lansmanda bana haber ver**
> [ e-posta@örnek.com ] [ **Haber ver** ]

**Başarı mesajı:**
> Teşekkürler — yaklaşık 12 hafta içinde, ürün yayına girdiğinde tek bir e-posta göndereceğiz. Abonelikten çıkmak tek satırlık yanıttır.

**Hata mesajı (genel):**
> Kaydedemedik — biraz sonra tekrar deneyin.

**Hata mesajı (geçersiz e-posta):**
> Bu e-posta adresi geçerli görünmüyor. Kontrol edip tekrar deneyin.

---

## 2. Feature Tiles (EN, TR)

Three side-by-side on `md+`, stacked on `xs–sm`. Each is icon (SVG) + bolded title + 1-sentence description.

### Tile 1 — Score-follower

- **EN:** *Score-follower that knows the difference between a wrong note and a late note.*
- **TR:** *Hatalı notayı geç notadan ayırt eden score-follower.*

### Tile 2 — AI fingering, explainable

- **EN:** *AI fingering suggestions trained only on public-domain editions. Always overridable.*
- **TR:** *Yalnızca public-domain edisyonlar üzerinde eğitilmiş AI parmak önerileri. Her zaman üzerine yazılabilir.*

### Tile 3 — Offline-first

- **EN:** *Installable as a PWA. Works offline after first load.*
- **TR:** *PWA olarak kurulabilir. İlk yüklemeden sonra çevrimdışı çalışır.*

---

## 3. FAQ (EN + TR)

Four questions, accordion format. Each answer ≤ 2 sentences.

### Q1 — What's in v1.0?

- **EN:** 15 Bach Two-Part Inventions (BWV 772–786), engraved from the public-domain Bach-Gesellschaft 1853 edition, with editorial and AI fingering. Broader repertoire ships later if enough people want it.
- **TR:** 15 Bach İki-Sesli Envansiyon (BWV 772–786), public-domain Bach-Gesellschaft 1853 edisyonundan yeniden dizilmiş, editöryel + AI parmaklama ile. Daha geniş repertuar, yeterli talep oluşursa sonradan eklenir.

### Q2 — Do I need a MIDI keyboard?

- **EN:** Yes for the full experience (score-follower listens via MIDI). Without MIDI you still hear the reference recordings and read the score.
- **TR:** Tam deneyim için evet (score-follower MIDI üzerinden dinler). MIDI olmadan da referans kayıtlarını duyar ve notayı okursunuz.

### Q3 — Why only $29 once?

- **EN:** We think pianists should pay for practice tools the way they pay for sheet music — once. No subscriptions in v1.0.
- **TR:** Piyanistlerin pratik araçlarına, nota defteri alır gibi — tek seferlik — ödeme yapmasının doğru olduğunu düşünüyoruz. v1.0'da abonelik yok.

### Q4 — Where is it available?

- **EN:** v1.0 launches in Türkiye and the EU/EEA only. Minimum age 16. Other regions come later.
- **TR:** v1.0 yalnızca Türkiye ve AB/EEA'da sunulur. Asgari yaş 16. Diğer bölgeler sonradan.

---

## 4. Legal Footer

### EN

> © 2026 <Founder Full Name>, <City>, TR. Controller for KVKK / GDPR purposes.
> Contact: `privacy@<domain>`.
>
> Available in Türkiye and the EU/EEA. **Minimum age 16.**
>
> [ Privacy notice ] · [ Terms ] · [ Accessibility statement ]

### TR

> © 2026 <Kurucu Tam Ad>, <Şehir>, TR. KVKK / GDPR kapsamında veri sorumlusu.
> İletişim: `privacy@<domain>`.
>
> Türkiye ve AB/EEA'da sunulur. **Asgari yaş 16.**
>
> [ Gizlilik bildirimi ] · [ Koşullar ] · [ Erişilebilirlik bildirimi ]

---

## 5. Legal Pages Linked from Footer

Minimal Phase-0 versions live at `/sprint0/privacy`, `/sprint0/terms`, `/sprint0/accessibility`. They reuse the Phase-1 templates but scoped to "Phase 0 research only". Draft content is founder's responsibility during Week-1 Day-2; boilerplate from [`../SECURITY.md`](../SECURITY.md) + [`../design/ACCESSIBILITY.md`](../design/ACCESSIBILITY.md).

Privacy notice Phase-0-scoped content must cover:
- Controller identity (natural person; founder name + contact).
- Purpose: landing-page email capture + Plausible anonymous analytics; nothing else.
- Legal basis: consent (GDPR Art. 6(1)(a) / KVKK Madde 5(1)(a)).
- Data collected: email address; anonymous page-view events.
- Retention: emails retained until launch + 90 days post-launch for single update email, then deleted unless user opts into the Phase-1 newsletter.
- Sub-processors: MailerLite EU, Plausible EU, Cloudflare Pages (CDN).
- Subject rights: KVKK Madde 11 / GDPR Art. 15–22.

---

## 6. Copy Constraints (audit before ship)

- [ ] No language implying a current sale ("Buy now", "Purchase", "Order").
- [ ] No child-oriented imagery or words ("kids", "family", "for every age").
- [ ] No country claim outside TR + EU.
- [ ] Controller identity visible in footer.
- [ ] Minimum age 16 visible in footer.
- [ ] Email form has double opt-in (MailerLite default).
- [ ] Plausible events firing (verified in DevTools on staging).

---

## 7. SEO / Metadata

```html
<title>Piano-Practice — Practise Bach in your browser (coming soon)</title>
<meta name="description" content="A browser-based piano practice studio for serious adult learners. Real engraved notation, sampled Steinway, MIDI input. Coming soon in Türkiye and the EU." />
<meta name="robots" content="index, follow" />
<link rel="canonical" href="https://<domain>/" />
<meta property="og:title" content="Piano-Practice — Practise Bach in your browser" />
<meta property="og:description" content="Real engraved notation. Sampled Steinway. MIDI input. Coming soon." />
<meta property="og:image" content="/og-image.png" />
<meta property="og:locale" content="en_GB" />
<meta property="og:locale:alternate" content="tr_TR" />
<meta name="twitter:card" content="summary_large_image" />
```

`robots.txt` allows indexing. Ads do **not** use `utm_content` names that could be indexed.

---

## 8. References

- `MASTER-PLAN.md §8.1`
- [`../design/UX-COPY.md §9.1`](../design/UX-COPY.md) — canonical Phase-0 copy (this file is the production-ready version)
- [`AD-CREATIVES-BRIEF.md`](AD-CREATIVES-BRIEF.md) — ad creatives that route here
- [`../decisions/PHASE0-SETUP.md`](../decisions/PHASE0-SETUP.md)
