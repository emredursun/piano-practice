# UX Copy Guide — Piano-Practice

> **Scope:** Voice, tone, microcopy, error/empty/loading states for Phase 1. TR + EN.
> **Authority:** [`BRAND.md`](BRAND.md), [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md), [`ACCESSIBILITY.md`](ACCESSIBILITY.md).

---

## 1. Voice

Respectful, specific, and calm. The user is a serious pianist; we speak to them as one.

### 1.1 Principles

1. **Say what, then why.** Headline delivers the news; body explains.
2. **Evidence over enthusiasm.** Replace "Amazing!" with a number or a concrete outcome.
3. **No confetti language.** Avoid "awesome", "woohoo", exclamation-mark streaks.
4. **Assume adult, assume competent.** Don't over-explain the obvious.
5. **Honest about limits.** "No internet — you can still practise cached pieces" beats a vague "Offline".
6. **Same humanity, both locales.** TR is not a translation of EN — it reads native.
7. **Name your subject.** "Your license" > "It". "The score" > "This".

### 1.2 What we never say

- "Oops!", "Whoops!", "Something went wrong" — always specify what.
- "Unlimited" (unless literally true).
- "Simple" / "easy" — piano isn't; respect the reader.
- "Join the community!" — Phase 1 has none.
- "Amazing performance!" — use a stat instead.

### 1.3 Formality

- **English:** conversational second-person. "You've saved your changes."
- **Turkish:** polite second-person plural ("siz" / "-sınız" / "-siniz" forms). Never "sen" in product copy.
- Both: sentence case for labels, headings, buttons. Title Case only for proper nouns.

---

## 2. Global Microcopy (EN / TR)

### 2.1 Buttons

| Context | EN | TR |
|---|---|---|
| Primary buy CTA | Buy lifetime — $29 | Ömür boyu satın al — 29 $ |
| Secondary learn | How it works | Nasıl çalışır |
| Start session | Start | Başlat |
| Continue | Continue | Devam et |
| Save changes | Save changes | Değişiklikleri kaydet |
| Cancel | Cancel | Vazgeç |
| Back | Back | Geri |
| Retry | Try again | Tekrar dene |
| Close | Close | Kapat |
| Skip | Skip for now | Şimdilik geç |
| Download data | Export my data | Verilerimi dışa aktar |

### 2.2 States

| State | EN | TR |
|---|---|---|
| Loading | Loading | Yükleniyor |
| Saving | Saving | Kaydediliyor |
| Saved | Saved | Kaydedildi |
| No internet | You're offline | İnternet bağlantısı yok |
| Reconnecting | Reconnecting | Yeniden bağlanılıyor |
| Updated version available | Update available — reload to apply | Güncelleme hazır — yenilemek için sayfayı yenile |

---

## 3. Page-Specific Copy

### 3.1 `/` — Landing

**Hero (EN):**
> **Practise Bach on a real piano. In your browser.**
> Real engraved notation. Real sampled Steinway. Real MIDI. No apps, no accounts — just the score and your hands.

**Hero (TR):**
> **Gerçek piyanoda Bach çalışın. Tarayıcınızda.**
> Gerçek notalar. Gerçek Steinway örneklemesi. Gerçek MIDI. Uygulama yok, hesap yok — sadece nota ve elleriniz.

**Value-prop bullets (EN):**
- Score-follower that knows the difference between a wrong note and a late note.
- AI fingering suggestions trained only on public-domain editions — explainable, overridable.
- 15 Bach Two-Part Inventions, pedagogically graded.
- $29 once. Lifetime updates. 14-day refund.

**Footer disclaimer:** "Available in Türkiye and the EU/EEA. Minimum age 16."

### 3.2 `/buy/return`

- **Heading (EN):** Thanks for purchasing — we're emailing your license now.
- **Heading (TR):** Teşekkürler — lisans kodunuzu e-postayla gönderiyoruz.
- **Polling state:** "Checking for your license… (this usually takes under a minute)."
- **Success:** "License received. Redirecting to your library…"
- **Timeout:** "Your license is taking longer than expected. Check your inbox (and spam folder), then paste the code [here]."

### 3.3 `/unlock`

- **Label:** License code
- **Placeholder:** Paste your license from the email
- **Error — invalid format:** "That doesn't look like a license code. It should start with `pp-`."
- **Error — invalid signature:** "This license isn't valid. [Recover license from email](…)."
- **Error — revoked:** "This license has been revoked. If that's a mistake, contact support."
- **Help link:** Can't find your license? → email re-send flow.

### 3.4 `/app` — Library empty state (should never happen but handled)

- "No pieces available. This is a bug — please [report it](…)."

### 3.5 `/app/practice/:id` — Prominent states

- **Audio-locked:**
  - Headline (EN): **Tap Start to enable audio and MIDI**
  - Headline (TR): **Sesi ve MIDI'yi etkinleştirmek için Başlat'a dokunun**
  - Body (EN): Your browser waits for your first action before making sound.
  - Body (TR): Tarayıcınız ses çıkarmadan önce ilk dokunuşunuzu bekler.
- **MIDI denied:**
  - "Playback-only — no MIDI access. [Enable MIDI](…) to play along."
  - "Yalnızca dinleme — MIDI erişimi yok. Birlikte çalmak için [MIDI'yi etkinleştirin](…)."
- **Wrong-note SR announcement (sparingly, if enabled):** "Missed" / "Hatalı".
- **Completion:** "Complete — 87 % accuracy. [See summary](…)." / "Tamamlandı — %87 doğruluk. [Özeti gör](…)."

### 3.6 `/app/session/:id/summary`

- Avoid "Amazing!". Use a number: "You played 87 % of notes correctly across 16 bars."
- "Try again" CTA; "Back to library" secondary.

### 3.7 `/app/settings` — Data export

- **Explainer:** "Your practice history lives only on this device. Export it as JSON to keep a copy or move to another device. Samples aren't exported — they re-download when you import."
- **Confirm import (destructive):** "Importing replaces your current practice history. Export first if you want a backup."

### 3.8 Legal footer

- "© 2026 Piano-Practice. [Privacy](…) · [Terms](…) · [Accessibility](…) · [Security](…)."

---

## 4. Error Copy Catalogue

Templates — variables in `{braces}`.

| ID | EN | TR |
|---|---|---|
| `err.network.generic` | Can't reach the server. Check your connection, then retry. | Sunucuya ulaşılamıyor. Bağlantınızı kontrol edip tekrar deneyin. |
| `err.audio.context.suspended` | Audio paused by the browser. Tap anywhere to resume. | Tarayıcı sesi duraklattı. Devam etmek için bir yere dokunun. |
| `err.audio.sample.load.failed` | Sample set couldn't load. Using the lighter set instead. | Örnek set yüklenemedi; daha hafif set kullanılıyor. |
| `err.midi.permission.denied` | MIDI permission denied. You can still hear playback — grant MIDI to play along. | MIDI izni reddedildi. Dinlemeye devam edebilirsiniz — birlikte çalmak için MIDI izni verin. |
| `err.midi.device.disconnected` | MIDI device "{device}" disconnected. Session continues in playback-only. | "{device}" MIDI aygıtı bağlantısı kesildi. Oturum yalnızca dinleme olarak sürüyor. |
| `err.license.invalid` | This license isn't valid. [Recover from email](…). | Bu lisans geçerli değil. [E-postadan kurtar](…). |
| `err.license.revoked` | This license has been revoked. Contact support if this is a mistake. | Bu lisans iptal edilmiş. Yanlışlıkla olduğunu düşünüyorsanız destekle iletişime geçin. |
| `err.notation.render` | Couldn't render this piece. [Reload](…); if it keeps happening, [report it](…). | Bu parça görüntülenemedi. [Yenileyin](…); sorun sürerse [bildirin](…). |
| `err.storage.quota` | Your browser is out of storage. [Export your data](…) then clear some pieces. | Tarayıcı depolama alanı doldu. [Verilerinizi dışa aktarın](…) ve bazı parçaları temizleyin. |
| `err.stripe.checkout` | Checkout couldn't start. Try again, or [contact support](…). | Ödeme başlatılamadı. Tekrar deneyin veya [destekle iletişime geçin](…). |

---

## 5. Empty State Copy

| Screen | EN | TR |
|---|---|---|
| No recent sessions | No sessions yet. Pick a piece to begin. | Henüz oturum yok. Başlamak için bir parça seçin. |
| No cached pieces (offline, fresh device) | You're offline and no pieces are cached yet. Connect once to download the set you'll practise. | İnternetiniz yok ve henüz önbellekte parça yok. Çalışacağınız seti indirmek için bir kez bağlanın. |
| No MIDI devices | No MIDI devices detected. Plug one in — we'll pick it up automatically. | MIDI aygıtı bulunamadı. Bir tane takın — otomatik algılanacak. |

---

## 6. Onboarding Microcopy (first session)

Short, inline, never modal-heavy.

- **Before Start button:** "First time? Tap Start once — your browser needs permission to make sound and receive MIDI."
- **After first correct note:** "Nice. Keep going to the next bar."
- **After first completion (ever):** "You've completed your first session. Exporting your progress in Settings is a good habit."

---

## 7. Notification / Email Templates

### 7.1 License email (sent by Stripe receipt + our Worker)

**Subject (EN):** Your piano-practice license
**Subject (TR):** Piano-practice lisansınız

**Body (EN):**
> Thanks for buying piano-practice.
>
> Your license code: **{license}**
>
> Open it here: {unlock-url}
>
> Keep this email — you'll need it on any new device. If you lose it, you can always recover the license using your purchase email.

**Body (TR):** [localised equivalent]

### 7.2 Refund confirmation

- Acknowledges refund, warns access ends, encourages data export **before** access revocation.

---

## 8. Accessibility-Specific Copy

- SR announcements per [`ACCESSIBILITY.md §6.2`](ACCESSIBILITY.md).
- Alt text template for score figure: "Engraved score of {title} by {composer}, {bars} bars."
- Skip-to-content label: "Skip to main content" / "Ana içeriğe geç".

---

## 9. Phase-0 Specific Copy (disposable)

### 9.1 `/sprint0/landing`

**Headline (EN):** Practise Bach in your browser — coming soon.
**Headline (TR):** Bach'ı tarayıcınızda çalışın — yakında.

**Intent question (EN):** *Would you pay $29 for lifetime access to a browser-based piano practice platform with real engraved notation, sampled Steinway, MIDI input, and AI fingering suggestions?*

**Thank-you (EN):** Thanks — we'll email you when we launch.

(Never says "Buy now" — no offer. Intent only. See `MASTER-PLAN.md §8.1`.)

---

## 10. Localisation Mechanics

- Source of truth: `apps/web/src/locales/{tr,en}/*.json` — ICU MessageFormat.
- Extraction via `@formatjs/cli` or equivalent (decided at Phase-1 Week 4).
- Each string has a `description` annotation so translators have context.
- No concatenation of localised fragments.
- **Build fails** if a key exists in `en` but not `tr` (or vice versa).

---

## 11. Tone Sensitivities

- **Music vocabulary** (composers, tempos, dynamics) stays in the canonical musical language (usually Italian). Explanations may be localised.
- **Difficulty terms** — "advanced" vs. "intermediate" resists translation nuance; use ABRSM grade numbers where exact.
- **Payment language** — avoid "subscribe" in Phase 1 (we don't subscribe).

---

## 12. Review Process

- **Every new string** reviewed against the principles in §1.
- **Every error string** paired with a recovery action.
- **Every locale pair** reviewed by a native speaker (founder covers TR; EN reviewed by a qualified second-pass before launch).
- **Copy audit** quarterly.

---

## 13. References

- [`BRAND.md`](BRAND.md) — voice and brand tone
- [`INTERACTION-PATTERNS.md`](INTERACTION-PATTERNS.md) — states these strings populate
- [`ACCESSIBILITY.md`](ACCESSIBILITY.md) — SR copy requirements
- [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md) — components using these strings
- `MASTER-PLAN.md §8.1` (Phase 0 landing copy constraints)
