# 🔬 Quality Gate Report — Piano Practice → Premium Platform

> **Versiyon:** 1.0.0 — ⛔ **SUPERSEDED BY AUDIT**
> **Tarih:** 2026-04-16
> **Sahip:** Senior Staff Engineer (Architectural Authority)
> **Workflow:** `quality-gate` (Devran AI Kit, v2.1.0)
> **Durum:** ⛔ **CONDITIONAL REJECTION** — bkz. `docs/AUDIT-REPORT-V1.md`
> **Gizlilik:** Internal — strategic & technical decision document

---

## ⚠️ TIER-1 AUDIT NOTICE — READ BEFORE PROCEEDING

Bu rapor v1.0 olarak yazıldı; sonrasında Senior Staff Engineer-level **Tier-1 production-grade audit** çalışması başlatıldı. 3 paralel adversarial agent (Architect, Security/Compliance DPO, Devil's Advocate) **60+ bulgu** tespit etti:

- **3 PROJECT-KILLER risk** (curriculum cost off 50-100×, fictional CAC, delusional 12-week timeline)
- **11 CRITICAL gap** (5 architecture + 6 security/compliance)
- **28 HIGH severity issue** (16 security, 12 architecture/operations)
- **17 MEDIUM** + 5 LOW

**Compliance Debt Score: 2.5 / 10** (target: ≥6.0 paid launch, ≥8.0 SOC 2)

**Verdict**: `/plan` workflow'una geçiş **BLOKE**. Tam audit raporu için: [`docs/AUDIT-REPORT-V1.md`](./AUDIT-REPORT-V1.md)

Bu v1.0 rapor referans olarak korunuyor; **revize edilmiş v2.0** kullanıcı kararları sonrası `docs/QUALITY-GATE-REPORT-V2.md` olarak yazılacak.

---

## 📋 İçindekiler

1. [Executive Summary](#1-executive-summary)
2. [Mevcut Durum (Baseline)](#2-mevcut-durum-baseline)
3. [Pazar Araştırması (10 Rakip)](#3-pazar-araştırması-10-rakip)
4. [Karşılaştırmalı Analiz Matrisi](#4-karşılaştırmalı-analiz-matrisi)
5. [Gap Analizi](#5-gap-analizi)
6. [Stratejik Konumlandırma & Diferansiyasyon](#6-stratejik-konumlandırma--diferansiyasyon)
7. [Teknoloji Yığını Kararları (ADR'ler)](#7-teknoloji-yığını-kararları-adrler)
8. [Hedef Mimari (Target Architecture)](#8-hedef-mimari-target-architecture)
9. [Pedagojik Çerçeve (25-Seviyeli Curriculum)](#9-pedagojik-çerçeve-25-seviyeli-curriculum)
10. [Yol Haritası (v0.1 → v3.0)](#10-yol-haritası-v01--v30)
11. [Risk Matrisi](#11-risk-matrisi)
12. [Etik & Güvenlik İncelemesi](#12-etik--güvenlik-incelemesi)
13. [Erişilebilirlik (WCAG 2.1 AA)](#13-erişilebilirlik-wcag-21-aa)
14. [İş Modeli & Ticari Analiz](#14-iş-modeli--ticari-analiz)
15. [Karar (Verdict)](#15-karar-verdict)
16. [Kaynaklar & Referanslar](#16-kaynaklar--referanslar)

---

## 1. Executive Summary

### Tek Cümle Tezi
> **Piyano öğrenme uygulamaları pazarında, mutlak başlayandan (middle C) konser piyanistine (Liszt *Mephisto*) tek bir koheran platformla hizmet veren ÜRÜN YOKTUR. Bu boşluk, "real engraved notation + Web MIDI + AI parmak numarası + spaced repetition + open-format ingestion + dual-mode UI" kombinasyonu ile kapatılabilir.**

### Üç Kritik Bulgu

1. **Pazar `intermediate plateau`'da kilitli.** Simply Piano, Flowkey, Skoove, Yousician, Playground, La Touche — beş üründen oluşan grup birbirinin neredeyse karbon kopyası: pop şarkıları + falling notes + simplified notation + microphone/USB MIDI. Hiçbiri Bach Inventions / Chopin Préludes seviyesini geçemiyor.

2. **Pro segment için tek seçenek var: Piano Marvel.** UI'sı 2010 seviyesinde ve teacher-bağımlı; self-learner'a düşman. "Modern UX + ciddi pedagoji" boşluğu **stratejik atak vektörü.**

3. **Teknoloji şu an mümkün:** Safari 18.4 (Nisan 2025) Web MIDI'yi shipledi → cross-platform browser-native MIDI artık yok-engel. OSMD + @tonejs/piano (Salamander V3) + ts-fsrs + pianoplayer DP fingering = production-grade stack, ~1.5 MB gzipped.

### Onaylanması Gereken Stratejik Karar
- **Hedef kitle:** Beginner + Pro (dual-track)
- **Dağıtım:** Browser-native (PWA), zero-install, MIDI-first
- **İş modeli:** Freemium + Pro tier (~$9/mo veya $89/yıl) — Avrupa fiyatlamasıyla La Touche'a paralel
- **MVP zaman çizelgesi:** 12 hafta (Phase 1)
- **Pre-1.0 yatırım hedefi:** repertuvar lisanslaması + AI fingering modeli (~150 saat eng + ~40 saat content)

### Quality Gate Verdict (Önizleme)
✅ **APPROVED — pending user sign-off** — bkz. [§15 Karar](#15-karar-verdict).

---

## 2. Mevcut Durum (Baseline)

### Dosya Yapısı
```
piano-practice/
├── index.html  (937 satır — tek dosya, HTML+CSS+JS inline)
├── README.md   (Tier-1 production-grade, TR)
└── docs/
    └── QUALITY-GATE-REPORT.md  (bu dosya)
```

### Mevcut Yetenekler
- 2-oktav sanal klavye (C4–C6, 15 beyaz + 10 siyah tuş)
- 6 hard-coded ders: C/G major scale, Twinkle, Ode to Joy, C chord, Chromatic
- Web Audio API ile **basit triangle+sine osilatör** ses üretimi
- Parmak numarası overlay (1–5)
- Sıradaki nota mavi pulse, aktif nota turuncu vurgu
- Tempo kontrol (400–1500ms), keyboard shortcuts (A–L)
- Responsive design (Cormorant Garamond + JetBrains Mono)

### Kritik Eksiklikler
| # | Eksik | Etki |
|---|---|---|
| 1 | Gerçek müzik notası (sheet music) yok | Pro segment imkânsız |
| 2 | MIDI keyboard desteği yok | "Pratik" iddiası boş |
| 3 | Ses kalitesi sentez (oscillator) — gerçek piyano değil | Premium algısı yok |
| 4 | Repertuvar 6 derse kilitli, MusicXML/MIDI import yok | Ölçeklenmez |
| 5 | İlerleme kaydedilmiyor (LocalStorage yok) | User retention yok |
| 6 | Sight-reading, theory, ear-training yok | Pedagoji boş |
| 7 | Build sistemi yok (vanilla HTML) | Kütüphane entegrasyonu zor |
| 8 | Test yok | Quality gate fail |
| 9 | Erişilebilirlik audit yok | WCAG 2.1 AA compliance belirsiz |
| 10 | Analytics/telemetri yok | Ürün kararları veri-bazlı değil |

---

## 3. Pazar Araştırması (10 Rakip)

| # | Ürün | Tier | Yıllık Fiyat | MIDI | Notation | Sight-Read Engine | Theory | Repertuvar | Pro-Grade |
|---|---|---|---|---|---|---|---|---|---|
| 1 | **Simply Piano** (JoyTunes) | Beginner | $120 | Mic + USB | Simplified | ❌ | Embedded | 1k+ | ❌ |
| 2 | **Flowkey** | Beginner-Int | $120 | USB + Mic | Real notation | ❌ | Light | 1.5k+ | ❌ |
| 3 | **Skoove** | Beginner-Int | $120 | USB + Mic | Real notation | ❌ | Best of trio | 400 | ❌ |
| 4 | **Yousician** (piano) | Beginner-Int | $180 | USB + Mic | Scrolling | ❌ | Gamified | Pop-heavy | ❌ |
| 5 | **Piano Marvel** | **Int-Pro** | $120 | **USB only** | **Engraved** | **✅ SASR** | **Dedicated** | **25k+** | **✅** |
| 6 | **Synthesia** | Hobbyist | $40 lifetime | **Best** | Falling notes | ❌ | ❌ | ∞ via MIDI | ❌ |
| 7 | **Pianote** (Musora) | Beg-Adv | $240 | Limited | PDF/video | Course | Strong | Yüzler | Partial |
| 8 | **Hoffman Academy** | Kids/Beg | $180 | Limited | PDF | Embedded | Embedded | 300+ | ❌ |
| 9 | **Playground Sessions** | Beg-Int | $120 | USB | Real notation | Limited | Light | 700+ | ❌ |
| 10 | **La Touche Musicale** | Beg-Int | ~€90 | **Web MIDI** | Falling+notation | ❌ | Light | 3k+ | ❌ |

### Detaylı Profiller (özet)

**Simply Piano** — `simplypiano.com`
- ✅ Microphone-based note detection (zero-hardware onboarding)
- ❌ Mic detection chord/fast pasajlarda fail; aggressive subscription dark patterns; intermediate ceiling

**Flowkey** — `flowkey.com`
- ✅ Kategorinin en temiz UX'i; "wait function" iyi implement
- ❌ Pedagoji şuursuz; sight-reading/theory yok; repertuvar easy arrangements'a saplandı

**Skoove** — `skoove.com`
- ✅ Trio'nun en iyi pedagojisi (gerçek method book hissi); en entegre theory
- ❌ "AI feedback" pazarlama puffery; küçük kütüphane; zayıf intermediate

**Yousician** — `yousician.com`
- ✅ Cross-instrument (gitar/bass/uke/piano/voice) tek subscription; en cilalı gamification
- ❌ Piano afterthought; non-standard scrolling notation real sheet music'e transfer etmiyor

**Piano Marvel** — `pianomarvel.com`
- ✅ **SASR (Standard Assessment of Sight Reading)** — sektörün altın standardı
- ✅ 25k+ piece library (Faber, Alfred, klasik literatur)
- ❌ UI 2010, self-learner'a hostile, gamification yok

**Synthesia** — `synthesiagame.com`
- ✅ One-time $40, infinite repertuvar (MIDI ecosystem), best MIDI support
- ❌ Öğretmen değil — kullanıcılar reading skills geliştiremiyor, kötü fingering

**Pianote** — `pianote.com`
- ✅ Live coaching, real teachers, community — gerçek derslere en yakın
- ❌ Real-time feedback yok; pahalı ($240/yıl); true beginner için scalable değil

**Hoffman Academy** — `hoffmanacademy.com`
- ✅ Free YouTube tier funnel; çocuklar için genuinely excellent pedagoji
- ❌ Video-only feedback loop; yetişkin için tone childlike

**Playground Sessions** — `playgroundsessions.com`
- ✅ Real-time scoring, robust desktop apps (Quincy Jones angle)
- ❌ Eşit olmayan pedagoji; brand momentum düşük

**La Touche Musicale** — `latouchemusicale.com`
- ✅ **Browser-native Web MIDI** (zero install); 3k+ song library; daha ucuz
- ❌ Light pedagoji — Synthesia-with-a-library; brand recognition düşük (Fransa dışında)

---

## 4. Karşılaştırmalı Analiz Matrisi

### Pedagoji Derinliği vs Gamification (2x2)

```
                        Pedagoji Derinliği (yüksek)
                              ▲
                              │
           Pianote ●          │          Piano Marvel ●
           (human-led)        │          (industry std)
                              │
                              │
                              │
          ◄─────────────── ★ DEVRAN ─────────────────►
       Düşük Gamification    │      Yüksek Gamification
                              │
                              │
                              │
       Synthesia ●            │          Yousician ●
       (MIDI player)          │          (most game-like)
                              │
              Hoffman ●       │     Skoove ●  Flowkey ●
              (kids)          │     (method) (UX)
                              │     Simply ●  Playground ●
                              │     La Touche ●
                              ▼
                        Pedagoji Derinliği (düşük)
```

**Beyaz alan:** Yüksek pedagoji + akıllı gamification + adult/serious-learner tone. Burada **DEVRAN PIANO** konumlanır.

### Beginner-Pro Bridge (skor 1-10)

| Ürün | Beginner Onboarding | Intermediate (Bach) | Advanced (Chopin) | Pro (Liszt) |
|---|---|---|---|---|
| Simply | 9 | 4 | 1 | 0 |
| Flowkey | 9 | 6 | 2 | 0 |
| Skoove | 8 | 5 | 1 | 0 |
| Piano Marvel | 3 | 9 | 9 | 8 |
| Pianote | 6 | 7 | 7 | 5 |
| Synthesia | 2 | 7 | 8 | 8 |
| **DEVRAN (target)** | **9** | **9** | **9** | **8** |

---

## 5. Gap Analizi

| # | Alan | Mevcut Pazar Standardı | Gap | Stratejik Önem |
|---|---|---|---|---|
| 1 | Real engraved notation + real-time MIDI feedback (advanced rep.) | Piano Marvel only | **Modern UX'li hiçbir ürün yok** | 🔴 Critical |
| 2 | AI-generated context-aware fingering | El ile curated, in-app catalog'da kilitli | **Hiçbir rakip credible yapamıyor** | 🔴 Critical |
| 3 | Adaptive sight-reading at scale | Sadece Piano Marvel SASR (teacher-walled) | **Self-learner için yok** | 🟡 High |
| 4 | Browser-native Bluetooth + USB Web MIDI | La Touche en yakın, pedagoji zayıf | **Açık pazar** | 🟢 Medium |
| 5 | Spaced repetition for repertoire maintenance | Hiçbir ürün uygulamıyor | **"Anki for pieces" yok** | 🟡 High |
| 6 | Bring-your-own MusicXML/PDF + real-time tracking | Soundslice (öğretmen tool, learning değil) | **Pro segment için açık** | 🟢 Medium |
| 7 | Theory + ear training + repertuvar tek graf | Bolt-on modüller her yerde | **Cohesive pedagoji açığı** | 🟡 High |
| 8 | Cancellation experience (no dark patterns) | Simply en kötü; hepsi sorunlu | **Reputational moat** | 🟢 Medium |
| 9 | Open-format ingestion (MusicXML/MIDI/MuseScore) | Vendor lock-in standart | **Trust & longevity** | 🟢 Medium |
| 10 | WCAG 2.1 AA full compliance | Hiçbir rakip iddia etmiyor | **Erişilebilirlik wedge** | 🟡 High |

---

## 6. Stratejik Konumlandırma & Diferansiyasyon

### "Devran Piano" Ürün Tezi

> **The piano practice platform that grows with you from middle C to Mephisto Waltz** — gerçek notalı, MIDI-first, AI-fingered, self-paced, ve kapatması kolay.

### Yedi Diferansiyatör (Ranked)

1. **AI Fingering Engine** (wedge feature)
   - Parncutt (1997) DP modeli + Nakamura (2020) HMM hibridi
   - User hand-span profiline adapt eder
   - El-ile düzeltmeleri öğrenir (kişisel preference modeli)
   - Hiçbir rakipte yok → defensible moat

2. **Dual-Mode UI** (technical solution to pro/beginner divide)
   - **"Guided Lane"**: falling notes + assistance + simplified notation (beginner)
   - **"Studio Lane"**: pure engraved notation + analytics + raw MIDI input (advanced)
   - Aynı engine, aynı user account, aynı progression graph

3. **SRS-Driven Repertoire Maintenance** (advanced players için pain point)
   - ts-fsrs algoritması passage-level granularity'de
   - "Bach Invention No. 4'ü 6 ay önce öğrenmiştin, şu pasajı tekrar et"
   - Anki-for-pieces — hiç kimse yapmıyor

4. **Open-Format Ingestion**
   - MusicXML (.xml/.mxl), MIDI (.mid), MuseScore (.mscz) — hepsi ingest
   - Optional: PDF + OMR (Audiveris veya commercial API ile)
   - User catalog'a kilitli değil → trust signal

5. **Browser-Native + Web MIDI First**
   - Zero install, PWA, offline-capable
   - Safari 18.4+ Web MIDI sayesinde cross-platform
   - La Touche'tan pedagoji ve UX olarak ileri

6. **Cohesive Pedagogy Graph**
   - Theory + technique + repertoire + ear-training + sight-reading **tek graph**
   - Her lesson birden fazla skill node'unu unlock eder
   - musictheory.net + Faber + ABRSM/RCM grading mapping

7. **Ethical UX**
   - 1-tıkla iptal (App Store complaint vector kapatılır)
   - Tüm telemetry opt-in, on-device first
   - Açık fiyat, hidden fee yok, "free trial → silent renewal" yok
   - Reputational moat

### Ne YAPMAYACAĞIZ (anti-positioning)

- ❌ "AI feedback" pazarlama puffery (Skoove'un yaptığı)
- ❌ Microphone-based detection (Simply'nin core IP'si — chord/hızlı pasajlarda zaten broken)
- ❌ Pop song catalog yarışı (lisanslama maliyeti, hukuki risk, commodity)
- ❌ Gamification XP/level/badge feature creep (Yousician trap)
- ❌ App Store-only (browser PWA stratejik avantaj)

---

## 7. Teknoloji Yığını Kararları (ADR'ler)

### ADR-001: Notation Rendering — OpenSheetMusicDisplay (OSMD)

| Decision | OpenSheetMusicDisplay v1.9+ |
|---|---|
| **Rejected alternatives** | VexFlow (no MusicXML), Verovio (2-4MB WASM), AlphaTab (guitar-focused), Flat.io (vendor lock-in), abcjs (ABC-only) |
| **Rationale** | MusicXML first-class + Cursor API + drawFingerings native + BSD-3 license + TypeScript-first |
| **Trade-offs** | 500-700KB gzipped (bundle hit), bazı complex tuplets MuseScore kalitesinin altında |
| **Risks** | Layout engine large scores için yavaş — passage-level rendering ile mitigate |
| **Code sketch** | `osmd.cursor.next()` + `osmd.cursor.NotesUnderCursor()` |

### ADR-002: Audio Synthesis — Tone.js + @tonejs/piano (Salamander V3)

| Decision | Tone.js (MIT) + @tonejs/piano (MIT) + Salamander Grand Piano V3 (CC-BY 3.0) |
|---|---|
| **Rejected alternatives** | smplr SplendidGrand (8.0/10 vs Salamander 9.5), SoundFont (synthetic feel), pure FM synthesis (impossible for grand piano) |
| **Rationale** | Highest realism (16 velocity layers, Yamaha C5 samples), production-proven, MIT license, AudioWorklet-based low latency |
| **Trade-offs** | 50-150MB sample library — progressive loading + CacheStorage ile mitigate |
| **Hosting** | **Cloudflare R2** (zero egress, predictable cost) + Cloudflare CDN |
| **Reverb** | Tone.Convolver + Aalto Pori concert hall IR @ 20% wet (CC-attribution) |

### ADR-003: MIDI Input — WebMidi.js + Web MIDI API

| Decision | WebMidi.js (djipco) wrapper + native Web MIDI API |
|---|---|
| **Rejected alternatives** | JZZ.js (overweight), raw Web MIDI (no abstraction) |
| **Rationale** | Browser-native, Safari 18.4+ tam destek (Apr 2025), USB + BLE-MIDI, sysex permission opsiyonel |
| **Trade-offs** | Bluetooth MIDI Windows Chrome'da yok (yıllardır known limitation) — UX'te belirt |
| **Latency target** | < 20ms end-to-end (USB MIDI in → audio out) |
| **Pedal** | CC#64 + Spacebar fallback |

### ADR-004: MIDI File Parsing — @tonejs/midi

| Decision | @tonejs/midi (parse) + MidiWriterJS (export) |
|---|---|
| **Rejected alternatives** | midi-parser-js (too low-level), JZZ (too heavy) |
| **Rationale** | Notes already converted from ticks (.time/.duration/.midi/.velocity), Tone.js native integration, ~50KB |

### ADR-005: AI Fingering — pianoplayer (Python, server-side) + custom JS DP

| Decision | **Hybrid:** pre-computed via pianoplayer (Marco Musy, Hart DP) for canonical repertoire; on-the-fly JS DP for user uploads |
|---|---|
| **Rejected alternatives** | Pure JS port (~500 LOC effort), neural Ramoneda model (PyTorch, ~50MB, server-only) |
| **Rationale** | pianoplayer mature + cited, JS DP "good enough" for live import, neural model deferred to v2.0 |
| **Algorithm** | Parncutt (1997) cost function: span, weak-finger, thumb-on-black, finger crossing |
| **Hand size** | User-configurable (default 14 semitones for adults, 10-12 for children) |

### ADR-006: Spaced Repetition — ts-fsrs (FSRS algorithm)

| Decision | open-spaced-repetition/ts-fsrs |
|---|---|
| **Rejected alternatives** | Custom SM-2 (Anki classic), Leitner box (too coarse) |
| **Rationale** | FSRS = SM-2'nin modern halefi, daha doğru retention modeli, MIT license, native TypeScript |
| **Granularity** | Passage-level (4-8 bar fragments), not whole-piece |

### ADR-007: Score Following — Custom DP + Chord Aggregation

| Decision | Custom JavaScript score follower (~200 LOC) |
|---|---|
| **Tolerance windows** | Perfect 30ms / Great 60ms / Good 120ms / Miss >250ms |
| **Chord aggregation window** | 50ms (industry standard) |
| **Wait Mode** | Cursor advance only on correct note, no timing penalty (beginner mode) |
| **Scoring formula** | `0.5 × accuracy + 0.3 × timing + 0.2 × tempo` |

### ADR-008: Build & Deployment

| Decision | **Vite** + **TypeScript** + **PWA** + **Cloudflare Pages** |
|---|---|
| **Rejected alternatives** | Webpack (slower), Next.js (overkill for PWA), Vercel (egress cost) |
| **Rationale** | Vite ESM dev fast, TypeScript prod safety, Cloudflare Pages = static hosting + free tier + R2 sample CDN aynı vendor |
| **Bundle target** | < 200KB gzipped initial JS, < 50MB cached samples after warm-up |

### ADR-009: State Management

| Decision | **Zustand** + IndexedDB (Dexie.js wrapper) |
|---|---|
| **Rejected alternatives** | Redux (boilerplate), Jotai (atom proliferation), LocalStorage (5MB limit) |
| **Rationale** | Zustand 1KB, hooks-based, IndexedDB = unbounded local storage for MIDI files + practice history |

### ADR-010: Repertoire Sources

| Source | License | Use |
|---|---|---|
| **MutopiaProject** | CC/PD/GFDL | **Primary** — fully redistributable, ~2k pieces |
| **OpenScore Lieder** | CC0 | **Primary** — manually proofed, vocal+piano |
| **OpenScore String Quartets** | CC0 | Secondary |
| **IMSLP** | Mostly PD (US/EU) | PDF (OMR'lı), curated MusicXML section |
| **Custom arrangements** | Devran-owned | Beginner-friendly Beethoven/Mozart simplifications |
| **MuseScore.com** | Mixed (uploaders) | ❌ **AVOID** — copyright risk |
| **Hal Leonard / Sheet Music Direct API** | Commercial | v2.0+ for modern repertoire |

---

## 8. Hedef Mimari (Target Architecture)

### Yüksek Seviyeli Diyagram

```
┌─────────────────────────────────────────────────────────────────┐
│                       USER (Browser, PWA)                        │
└─────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  PRESENTATION LAYER                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │  Guided Lane │  │ Studio Lane  │  │  Theory & SR Lane    │  │
│  │ (falling     │  │ (engraved    │  │  (musictheory.net    │  │
│  │  notes +     │  │  notation +  │  │   style drills)      │  │
│  │  beginner UX)│  │  pro UX)     │  │                      │  │
│  └──────────────┘  └──────────────┘  └──────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│  APPLICATION LAYER (TypeScript)                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Lesson Engine  │  Score Follower  │  Practice Scheduler │  │
│  │  (state mgmt)   │  (DP cursor)     │  (FSRS)             │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
       │                   │                    │
       ▼                   ▼                    ▼
┌─────────────┐    ┌──────────────┐    ┌──────────────────┐
│ NOTATION    │    │ AUDIO        │    │ INPUT            │
│ OSMD        │    │ Tone.js +    │    │ WebMidi.js +     │
│ (MusicXML   │    │ @tonejs/piano│    │ Web MIDI API +   │
│  → SVG +    │    │ (Salamander) │    │ Computer kbd     │
│  Cursor)    │    │              │    │ (A-L mapping)    │
└─────────────┘    └──────────────┘    └──────────────────┘
       │                   │                    │
       └───────────────────┴────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│  DATA LAYER                                                      │
│  ┌────────────────┐  ┌──────────────┐  ┌─────────────────────┐ │
│  │ MusicXML CDN   │  │  IndexedDB   │  │  Telemetry API      │ │
│  │ (Cloudflare R2)│  │  (Dexie.js)  │  │  (opt-in, on-device │ │
│  │  + Salamander  │  │  - Practice  │  │   first; minimal)   │ │
│  │    samples     │  │    history   │  │                     │ │
│  │                │  │  - User MIDI │  │                     │ │
│  │                │  │    uploads   │  │                     │ │
│  │                │  │  - FSRS      │  │                     │ │
│  │                │  │    schedule  │  │                     │ │
│  └────────────────┘  └──────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│  BACKEND (minimal, only when necessary)                          │
│  ┌───────────────────┐  ┌──────────────────────────────────┐   │
│  │  Auth (Supabase)  │  │  Fingering Pre-compute Worker     │   │
│  │  Subscription     │  │  (Python, pianoplayer, scheduled  │   │
│  │  Stripe webhook   │  │   batch processing on R2 uploads) │   │
│  └───────────────────┘  └──────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

### Klasör Yapısı (Hedef)

```
piano-practice/
├── src/
│   ├── main.ts                      # Entry point
│   ├── app/
│   │   ├── App.tsx                  # Root component (or vanilla shell)
│   │   ├── routes.ts
│   │   └── pwa-register.ts
│   ├── lanes/
│   │   ├── guided/                  # Beginner UI (falling notes)
│   │   │   ├── GuidedView.tsx
│   │   │   ├── FallingNotes.ts
│   │   │   └── BeginnerHints.ts
│   │   ├── studio/                  # Pro UI (engraved notation)
│   │   │   ├── StudioView.tsx
│   │   │   ├── ScoreRenderer.ts     # OSMD wrapper
│   │   │   └── PracticeAnalytics.ts
│   │   └── theory/                  # Theory & sight-reading
│   │       ├── TheoryDrills.ts
│   │       └── SightReadGenerator.ts
│   ├── engine/
│   │   ├── audio/
│   │   │   ├── PianoSynth.ts        # @tonejs/piano wrapper
│   │   │   ├── ReverbChain.ts       # Tone.Convolver
│   │   │   └── PedalManager.ts
│   │   ├── input/
│   │   │   ├── MidiInput.ts         # WebMidi.js wrapper
│   │   │   ├── KeyboardInput.ts     # Computer keyboard fallback
│   │   │   └── InputRouter.ts
│   │   ├── notation/
│   │   │   ├── OSMDAdapter.ts
│   │   │   ├── MusicXMLLoader.ts
│   │   │   └── MidiToScore.ts
│   │   ├── scoring/
│   │   │   ├── ScoreFollower.ts     # DP cursor + grading
│   │   │   ├── ChordAggregator.ts
│   │   │   └── GradingBands.ts
│   │   ├── fingering/
│   │   │   ├── ParncuttDP.ts        # Client-side DP
│   │   │   ├── FingeringCache.ts
│   │   │   └── HandSpanProfile.ts
│   │   └── srs/
│   │       ├── FSRSScheduler.ts     # ts-fsrs wrapper
│   │       └── PassageQueue.ts
│   ├── data/
│   │   ├── store.ts                 # Zustand
│   │   ├── repository.ts            # Dexie.js IndexedDB
│   │   └── repertoire.ts
│   ├── pedagogy/
│   │   ├── curriculum.ts            # 25-level skill graph
│   │   ├── theory-content.ts        # musictheory.net-style content
│   │   └── lesson-builder.ts
│   ├── ui/
│   │   ├── components/              # Reusable UI components
│   │   ├── hooks/
│   │   └── styles/
│   └── i18n/
│       ├── tr.json                  # Turkish (default)
│       └── en.json                  # English
├── public/
│   ├── samples/                     # Salamander (lazy-loaded from R2)
│   ├── ir/                          # Impulse responses
│   └── scores/                      # MusicXML lesson library
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/                         # Playwright
├── docs/
│   ├── QUALITY-GATE-REPORT.md       # this file
│   ├── ARCHITECTURE.md
│   ├── CURRICULUM.md
│   ├── ROADMAP.md
│   └── architecture/
│       ├── ADR-001-notation.md
│       └── ...
├── package.json
├── vite.config.ts
├── tsconfig.json
├── playwright.config.ts
├── README.md
├── LICENSE
└── index.html
```

---

## 9. Pedagojik Çerçeve (25-Seviyeli Curriculum)

### Üç Track + Sandbox

#### Track 1: **Foundation** (Levels 1-5) — "Temel"

| Level | Theory | Technique | Repertoire | Skill Unlocks |
|---|---|---|---|---|
| 1 | Staff, note names (treble), durations | 5-finger position C major, finger numbers | Twinkle, Hot Cross Buns, Ode to Joy (simple) | grand_staff_basic |
| 2 | Time signatures, rests, dynamics, bass clef intro | 5-finger major patterns C/G/D/A/E | Folk songs, Faber Primer pieces | both_clefs |
| 3 | Key signatures (1-2♯/♭), intervals 2nd-5th | Tetrachord scales, simple HT coordination | Schumann *Album* easier, Bartók *First Term* | hands_together_basic |
| 4 | Major/minor distinction, tonic/dominant function | One-octave scales C/G/F, broken triads | Burgmüller Op. 100 (early), Gurlitt Op. 117 | scales_one_octave |
| 5 | Triads + inversions, primary chords I-IV-V | Two-octave scales, chord progressions, simple Alberti | Bach *Notebook* Minuets, Clementi Sonatina Op. 36 No. 1 | chord_inversions |

#### Track 2: **Literacy & Style** (Levels 6-15) — "Okuryazarlık"

| Level | Theory | Technique | Repertoire |
|---|---|---|---|
| 6 | All major keys, circle of fifths | All major scales 2 oct, arpeggios | Bach Inventions (1, 4, 8) |
| 7 | Natural minor scales | Czerny Op. 599 selections | Mozart K. 545 (1st mvt) |
| 8 | Harmonic + melodic minor | Hanon 1-10 (musical context, not mechanical) | Chopin Préludes Op. 28 No. 7, 20 |
| 9 | Modes (Ionian-Locrian) | Czerny Op. 849 selections | Bach Sinfonias (easier) |
| 10 | Modulation (closely related keys) | All minor scales, double thirds intro | Beethoven Op. 49 No. 1 |
| 11 | Cadence types (PAC, IAC, half, deceptive) | Czerny Op. 299 selections, octave technique | Schumann *Kinderszenen* |
| 12 | Sonata form analysis | Trills, ornamentation | Beethoven middle sonatas (Op. 27 No. 2 *Moonlight* I) |
| 13 | Counterpoint basics (species 1-2) | Cortot Rational Principles intro | Chopin Nocturnes (Op. 9 No. 2) |
| 14 | Secondary dominants | Pischna selections | Debussy *Children's Corner* (Doctor Gradus) |
| 15 | Jazz harmony intro (ii-V-I) | Hanon 21-40 if needed | Brahms Intermezzi (Op. 117 No. 1) |

#### Track 3: **Mastery** (Levels 16-25) — "Ustalık"

| Level | Theory | Technique | Repertoire |
|---|---|---|---|
| 16 | Augmented sixth chords, Neapolitan | Czerny Op. 740 | Chopin Études Op. 10 No. 3, 6, 9 |
| 17 | Schenkerian intro | Brahms 51 Exercises | Beethoven Op. 81a *Les Adieux* |
| 18 | Modal interchange, chromatic harmony | Octaves (Liszt-style) | Liszt *Années de Pèlerinage* |
| 19 | Post-tonal theory intro | Liszt *Paganini Études* | Beethoven late sonatas (Op. 109) |
| 20 | Set theory, twelve-tone basics | Godowsky studies (selections) | Ravel *Sonatine* |
| 21 | Score reading (orchestral reduction) | Chopin Études (full Op. 10) | Liszt *Sonata B minor* |
| 22 | Analytical mastery | Chopin Études (Op. 25) | Ravel *Gaspard de la nuit* |
| 23 | Voice leading at concert level | Liszt *Transcendental Études* (selected) | Rachmaninoff Concerto No. 2 (selections) |
| 24 | Contemporary harmony | Liszt *Transcendental* (full) | Ligeti Études |
| 25 | Improvisation, cadenzas | Composer-level technique | Free recital prep |

#### Sandbox: **Professional Mode**

- **MusicXML/MIDI/MuseScore import** — any score in
- **Auto-fingering engine** — Henle/Cortot/Paderewski editorial corpora-trained
- **Practice modes:**
  - Loop selector (bar-level)
  - Tempo ramp (auto-increase when accuracy > 95% × 3 runs)
  - Hands-separate isolation
  - Rhythmic variation generator (dotted, reverse-dotted, triplets)
  - Click-track + recording playback comparison
- **Sight-reading generator** — procedural, parametric difficulty (Henle 1-9)
- **Analytics:**
  - Practice journal (focus duration, error pattern heatmap)
  - Plateau detection
  - SRS-driven repertoire maintenance dashboard
- **Repertoire database** — searchable by Henle/RCM/ABRSM grade, era, length, technical focus
- **Theory module** — adaptive (musictheory.net + piano-specific drills)

### Pedagojik İlkeler (Research-Grounded)

1. **Deliberate Practice** (Ericsson 1993) — focused, goal-directed, immediate feedback
2. **Slow Practice** — neurological consolidation requires under-tempo accuracy first
3. **Mental Practice** — Pascual-Leone (1995) fMRI: imagined practice activates motor cortex
4. **Spaced Repetition** — Bjork "desirable difficulties" (3×20min beats 1×60min)
5. **Chunking** — Sweller cognitive load: 2-4 bar units, master, then connect
6. **Variation Practice** — prevents motor pattern rigidity
7. **Random > Blocked Practice** for retention (Schmidt & Lee 2019)
8. **External focus** (sound goal) > internal focus (finger position)

---

## 10. Yol Haritası (v0.1 → v3.0)

### Phase 1 — **Foundation** (Hafta 1-12) → v1.0

**Hedef:** Mevcut HTML/CSS/JS prototipi production-grade Vite + TypeScript + PWA stack'ine taşı, OSMD + Tone.js entegre et, MIDI desteği ekle.

| Sprint | Hafta | Deliverables |
|---|---|---|
| 1 | 1-2 | Vite + TypeScript + ESLint + Prettier + Vitest scaffold; mevcut UI'yı componentize et; PWA manifest |
| 2 | 3-4 | Tone.js + @tonejs/piano + Salamander samples + Cloudflare R2 hosting; sustain pedal; spacebar fallback |
| 3 | 5-6 | OSMD entegrasyonu; ilk MusicXML lesson (C major scale, Twinkle as MusicXML); cursor sync |
| 4 | 7-8 | WebMidi.js + Web MIDI API; MIDI input → score follower; chord aggregation; grading bands |
| 5 | 9-10 | IndexedDB (Dexie.js) + Zustand; practice history; basic FSRS scheduler |
| 6 | 11-12 | E2E tests (Playwright); WCAG 2.1 AA audit; deploy to Cloudflare Pages; v1.0 release |

**v1.0 Definition of Done:**
- ✅ 6 mevcut ders MusicXML formatına dönüştürüldü
- ✅ MIDI keyboard plug-and-play çalışıyor
- ✅ Real-time score following + grading
- ✅ Basic SRS scheduler
- ✅ PWA installable
- ✅ Lighthouse: Performance ≥ 90, A11y ≥ 95, SEO ≥ 90
- ✅ Test coverage ≥ 80%
- ✅ TR + EN i18n

### Phase 2 — **Curriculum Build-Out** (Hafta 13-24) → v1.5

**Hedef:** Foundation track tam, intermediate'in yarısı, AI fingering MVP.

| Sprint | Hafta | Deliverables |
|---|---|---|
| 7 | 13-14 | Curriculum graph engine (skill node DAG); Levels 1-5 content (Foundation track) |
| 8 | 15-16 | Theory drills (musictheory.net-style); ear training MVP (interval/chord ID) |
| 9 | 17-18 | Sight-reading generator (procedural, Henle 1-3) |
| 10 | 19-20 | AI Fingering Engine (Parncutt DP, JS port) — wedge feature |
| 11 | 21-22 | Levels 6-10 content (Literacy track first half) |
| 12 | 23-24 | "Studio Lane" UI (engraved-only mode for advanced users); v1.5 release |

### Phase 3 — **Pro Sandbox** (Hafta 25-36) → v2.0

**Hedef:** MusicXML/MIDI import, full intermediate + advanced curriculum, neural fingering.

| Sprint | Hafta | Deliverables |
|---|---|---|
| 13 | 25-26 | Bring-your-own-MusicXML + on-the-fly fingering |
| 14 | 27-28 | MIDI file import + auto-staff-split (LH/RH) |
| 15 | 29-30 | Levels 11-15 (Literacy 2nd half) + Levels 16-20 (Mastery 1st half) |
| 16 | 31-32 | Practice analytics dashboard (heatmap, plateau detection) |
| 17 | 33-34 | Neural fingering model (server-side Python, Ramoneda-style) — A/B vs Parncutt DP |
| 18 | 35-36 | Subscription + Stripe + Supabase auth; Pro tier launch; v2.0 |

### Phase 4 — **Scale & Polish** (Hafta 37-52) → v3.0

| Sprint | Hafta | Deliverables |
|---|---|---|
| 19-20 | 37-40 | Levels 21-25 (Mastery completion); virtuoso repertoire |
| 21 | 41-42 | OMR (PDF → MusicXML) integration (Audiveris or commercial API) |
| 22 | 43-44 | Bluetooth MIDI workarounds for Windows; mobile UX optimization |
| 23-24 | 45-48 | Community features: share annotated scores, fingering corrections crowd-sourced |
| 25 | 49-50 | Hal Leonard / Sheet Music Direct API integration (modern licensed repertoire) |
| 26 | 51-52 | v3.0 launch + iOS/Android wrapper (Capacitor) for App Store presence |

### Backlog (Future / v4.0+)

- 🔮 **Audio-from-microphone fallback** (for users without MIDI keyboards) — pitch detection (CREPE / SPICE)
- 🔮 **Real-time collaborative practice** (WebRTC, duet mode)
- 🔮 **Live teacher sessions** marketplace
- 🔮 **AI-generated practice plans** (LLM-powered diagnostic intervention)
- 🔮 **Composition mode** (notation editor + Tone.js playback)
- 🔮 **VST plugin** (offline desktop integration)

---

## 11. Risk Matrisi

| # | Risk | Olasılık | Etki | Severity | Mitigation |
|---|---|---|---|---|---|
| 1 | OSMD bundle size (700KB) initial load yavaş | Orta | Orta | 🟡 | Code-split, OSMD'yi sadece studio lane'de lazy-load |
| 2 | Salamander samples 50-150MB — mobile data plan riski | Yüksek | Yüksek | 🔴 | Progressive loading + smplr SoundFont fallback (`navigator.connection.saveData` detect) |
| 3 | Web MIDI Safari 18.4'ten önceki sürümlerde yok (~%15 user base) | Orta | Orta | 🟡 | Computer keyboard fallback (A-L); UI uyarısı |
| 4 | Bluetooth MIDI Windows Chrome'da yok | Düşük | Düşük | 🟢 | Documentation; USB MIDI önerisi |
| 5 | Repertoire copyright (modern songs için) | Düşük (PD focus) | Yüksek (lawsuit) | 🟡 | v1.0-2.0 sadece PD; Hal Leonard API v3.0+ |
| 6 | AI Fingering accuracy düşük (Parncutt DP ~71% match human) | Yüksek | Orta | 🟡 | User correction mechanism; learn from edits; "AI suggestion" framing not "correct" |
| 7 | Piano Marvel competitive response (modern UI redesign) | Orta | Yüksek | 🟡 | Speed to market; defensible AI/SRS moat |
| 8 | Subscription churn (industry avg 3-7%/month) | Yüksek | Orta | 🟡 | Ethical UX (1-click cancel), value-driven pricing, free tier |
| 9 | Audio latency > 50ms in some browsers/OS | Orta | Yüksek | 🔴 | AudioContext.outputLatency compensation; Web Audio Workers; user latency calibration tool |
| 10 | Curriculum content production cost (25 levels × 10 lessons = 250+ pieces) | Yüksek | Yüksek | 🔴 | OpenScore + Mutopia + IMSLP automated pipeline; community contributions; v1.0 ships with Levels 1-5 only |
| 11 | Pedagogical correctness — wrong fingering taught | Düşük | Yüksek (reputation) | 🟡 | Editorial review by certified piano teacher; user feedback loop |
| 12 | Browser breaking changes (e.g., autoplay policy tightens) | Düşük | Orta | 🟢 | Progressive enhancement; user-gesture pattern enforced |
| 13 | OSMD project abandonment | Düşük | Yüksek | 🟡 | BSD license = forkable; VexFlow alternative ready |
| 14 | Cloudflare R2 outage | Düşük | Yüksek | 🟡 | Multi-CDN (R2 primary, jsDelivr fallback for samples) |

---

## 12. Etik & Güvenlik İncelemesi

### AI Bias Assessment
- ✅ **Fingering algorithm:** Parncutt model adult Western pianist hand assumptions üzerine kurulu. **Hand size profile** kullanıcıya configurable — children, smaller hands (often female pianists / Asian populations) için bias mitigated.
- ✅ **Curriculum:** Western classical-heavy. v2.0+ jazz, world music, contemporary genres. **Bias risk acknowledged.**
- ✅ **Sight-reading generator:** Procedural — no training data bias.

### GDPR / KVKK / Privacy Implications
- ✅ **Data minimization:** Practice history default on-device (IndexedDB). Cloud sync **opt-in**.
- ✅ **Consent:** Explicit opt-in for telemetry, separate from account creation.
- ✅ **Right to erasure:** Account delete = full data wipe (R2 + Supabase + IndexedDB).
- ✅ **Right to portability:** Export practice history as JSON; export user MIDI files as .mid bundle.
- ✅ **Cookie policy:** Functional only (auth session); no tracking cookies; no third-party analytics by default.
- ✅ **Subprocessor list:** Supabase (auth/db), Stripe (payments), Cloudflare (CDN). Documented in privacy policy.
- ✅ **Data residency:** EU users → EU region (Supabase EU + Cloudflare EU PoP).

### Automation Safety
- ✅ **Score follower failure mode:** Silent fail → user keeps playing without grading. **Visible UI indicator** when grading is paused/failed.
- ✅ **Subscription auto-renewal:** Email reminder 7 days before charge; 1-click cancel from account page (no "5 menus deep" dark pattern).
- ✅ **MIDI device disconnect:** Visible UI indicator + auto-reconnect attempt.

### User Autonomy
- ✅ **No dark patterns:** No "Are you sure you want to leave?" guilt-trip; no fake countdowns; no hidden cancel buttons.
- ✅ **No forced flows:** Free tier fully functional (5 lessons/day cap); premium upsell non-blocking.
- ✅ **Adversarial design principles:** Designed to **maximize user mastery, not session time.** No "daily streak guilt" gamification (Yousician anti-pattern).
- ✅ **Open formats:** User-owned data exportable — no lock-in.

### Human-in-the-Loop
- ✅ **Subscription changes:** Explicit confirmation modal for tier changes/cancellations.
- ✅ **Account deletion:** Explicit confirmation + 7-day grace period + email reminder.
- ✅ **AI Fingering:** "Suggested" framing, not "Correct" — user override always preserved.

### Security
- ✅ **Authentication:** Supabase Auth (battle-tested, OAuth support, MFA)
- ✅ **Payment:** Stripe (PCI DSS Level 1)
- ✅ **Secrets management:** Cloudflare environment variables, never committed
- ✅ **CSP headers:** Strict Content Security Policy (no inline scripts, no eval)
- ✅ **HTTPS only:** PWA requires secure context anyway
- ✅ **MIDI sysex:** Disabled by default (Web MIDI security model)
- ✅ **Subresource Integrity (SRI):** All CDN dependencies pinned + hashed

### Rejection Triggers — Evaluation
| Trigger | Status | Notes |
|---|---|---|
| Harmful patterns detected | ❌ Not triggered | Ethical UX explicit principle |
| Missing research | ❌ Not triggered | 5+ competitors analyzed in depth |
| Privacy violation | ❌ Not triggered | Data minimization, opt-in, exportable |
| Accessibility failure | ❌ Not triggered | WCAG 2.1 AA target documented |
| Security regression | ❌ Not triggered | Security improvements over baseline |

✅ **No rejection triggers fired.**

---

## 13. Erişilebilirlik (WCAG 2.1 AA)

### Hedef: WCAG 2.1 AA tam uyum + AAA dilediğince

| Criterion | Target | Implementation |
|---|---|---|
| **1.1.1 Non-text Content** | A | Alt text for all icons; aria-label for all interactive elements |
| **1.3.1 Info and Relationships** | A | Semantic HTML; ARIA landmarks; proper heading hierarchy |
| **1.4.3 Contrast (Minimum)** | AA | 4.5:1 text, 3:1 UI elements; current orange (#d97757) vs dark bg verified |
| **1.4.4 Resize Text** | AA | All text scales to 200% without loss of content/function |
| **1.4.10 Reflow** | AA | Mobile breakpoints; no horizontal scroll at 320px |
| **1.4.11 Non-text Contrast** | AA | UI components ≥ 3:1 contrast |
| **1.4.12 Text Spacing** | AA | No fixed line-height; supports user style overrides |
| **2.1.1 Keyboard** | A | All functionality via keyboard (already partially done) |
| **2.1.4 Character Key Shortcuts** | A | A-L shortcuts can be disabled/remapped (avoid conflict with screen readers) |
| **2.3.1 Three Flashes** | A | Pulse animation < 3Hz; `prefers-reduced-motion` respected |
| **2.4.6 Headings and Labels** | AA | Descriptive headings/labels |
| **2.4.7 Focus Visible** | AA | 2px focus ring, high contrast |
| **2.5.1 Pointer Gestures** | A | All multi-pointer/path gestures have single-pointer alternative |
| **2.5.5 Target Size** | AAA | 44×44px minimum touch target (mobile) |
| **3.1.2 Language of Parts** | AA | `lang="tr"` / `lang="en"` per i18n |
| **3.3.1 Error Identification** | A | Form errors clearly identified |
| **4.1.2 Name, Role, Value** | A | All custom controls have proper ARIA roles |
| **4.1.3 Status Messages** | AA | aria-live for "Note correct!", "Wrong note", etc. |

### Tooling
- **axe-core** in Vitest unit tests
- **Lighthouse CI** in GitHub Actions (a11y score ≥ 95)
- **NVDA / JAWS / VoiceOver** manual testing per release
- **Keyboard-only navigation** test in Playwright

### Audio Accessibility
- **Visual-only mode:** All audio cues paired with visual cues (already partial)
- **Captions for video tutorials** (when introduced in Pianote-style content)
- **Hearing-impaired user mode:** Visual metronome (flashing LED-style indicator)

---

## 14. İş Modeli & Ticari Analiz

### Pricing Strategy

| Tier | Aylık | Yıllık | Lifetime | Audience |
|---|---|---|---|---|
| **Free** | $0 | $0 | — | 5 lessons/day, no MIDI import, basic 6 lessons, ad-free |
| **Pro** | $9/mo | $89/yr (17% off) | $249 once | Unlimited lessons, MIDI import, AI fingering, SRS dashboard, all tracks 1-25, theory module |
| **Studio** (v2.5+) | $19/mo | $179/yr | $499 once | Pro + neural fingering, OMR, advanced analytics, priority support |

**Rationale:**
- Beats La Touche (~€90) on functionality, matches on price
- Half of Flowkey/Skoove ($120/yr) — undercut beginner segment
- Lifetime tier captures pro-skeptic-of-subscription segment (Synthesia $40 lifetime as anchor)

### Unit Economics (Hypothetical Year 1)

- **Sample/CDN cost:** ~$0.001/user/month (R2 free tier covers first 10TB egress + zero-egress to Cloudflare's edge)
- **Supabase cost:** Free tier up to 50k MAU; Pro $25/mo at scale
- **Stripe fees:** 2.9% + $0.30 per transaction → ~3.2% blended
- **Customer Acquisition Cost (CAC) target:** $15 (organic SEO + content marketing focus)
- **LTV target:** $50 (avg 7-month subscription at $9/mo)
- **LTV:CAC = 3.3:1** — healthy SaaS metric

### Go-To-Market

1. **Content marketing first** — YouTube tutorials, blog posts (musictheory.net SEO), free tier demo
2. **Reddit r/piano** & piano teacher forum engagement (organic, value-first)
3. **MusicXML community** outreach (MuseScore.com, OpenScore project) — partnerships
4. **Piano teacher referral program** (15% commission for referred Pro signups)
5. **App Store presence v3.0+** (Capacitor wrapper) for discovery

### Defensibility (Moats)

1. **AI Fingering** — 12-18 month tech lead vs nearest competitor
2. **SRS Dashboard** — no competitor; high switching cost once built
3. **Curriculum graph** — 6+ months content production; pedagogical correctness reputation
4. **Ethical UX reputation** — App Store reviews highlight cancellation experience
5. **Open-format** — anti-lock-in trust signal compounds over time
6. **Browser-native PWA** — escape App Store fees + faster iteration

---

## 15. Karar (Verdict)

### ✅ APPROVED — Conditional on User Sign-Off

**Decision:** Proceed to `/plan` for Phase 1 (v1.0) implementation.

**Rationale:**
1. **Pazar fırsatı net:** Pro/beginner divide unbridged — defensible white space.
2. **Teknoloji yığını mature:** Tüm ADR'ler MIT/BSD/permissive license, production-proven libraries, 2025/2026 browser desteği yeterli.
3. **Pedagoji araştırma-temelli:** Ericsson, Bjork, Schmidt & Lee, Parncutt — establish edilmiş ilkeler üzerine kurulu.
4. **Etik temiz:** No rejection triggers fired, GDPR/KVKK uyumlu, dark pattern yok, accessibility-first.
5. **Risk profili kabul edilebilir:** En kritik 3 risk (sample size, audio latency, curriculum production cost) için somut mitigation planları var.
6. **İş modeli sağlıklı:** LTV:CAC 3.3:1 (target), Pro tier defensible, lifetime opt available.

### Karar Şartları (User Approval Gate)

User aşağıdakileri onaylamalıdır:

- [ ] **Stratejik konumlandırma:** "Beginner + Pro dual-track, browser-native, PWA, MIDI-first"
- [ ] **Teknoloji yığını:** Vite + TypeScript + OSMD + Tone.js + @tonejs/piano + Salamander + WebMidi.js + ts-fsrs + IndexedDB + Cloudflare R2/Pages + Supabase + Stripe
- [ ] **Pedagoji çerçevesi:** 25-level curriculum, 3 track + Pro Sandbox
- [ ] **Yol haritası:** v1.0 in 12 hafta, v2.0 in 36 hafta, v3.0 in 52 hafta
- [ ] **İş modeli:** Free + Pro ($9/mo) + Studio ($19/mo, v2.5+) + Lifetime opsiyonel
- [ ] **Etik & erişilebilirlik:** WCAG 2.1 AA, GDPR/KVKK, no dark patterns
- [ ] **İlk Phase yatırım:** ~150 saat engineering + ~40 saat content (12 hafta)

### Onay Seçenekleri

```
[ ] APPROVE FULL — proceed to /plan with all decisions as documented
[ ] APPROVE WITH CHANGES — list changes you want before proceeding
[ ] REJECT — explain rejection reason; revise this report
[ ] DEFER — request additional research on specific topic
```

---

## 16. Kaynaklar & Referanslar

### Pazar & Rakipler
- simplypiano.com, flowkey.com, skoove.com, yousician.com, pianomarvel.com, synthesiagame.com, pianote.com, hoffmanacademy.com, playgroundsessions.com, latouchemusicale.com
- Reddit r/piano discussions, App Store / Play Store reviews
- Piano teacher forum reception (Piano Marvel standing notably)

### Teknoloji
- [OpenSheetMusicDisplay](https://github.com/opensheetmusicdisplay/opensheetmusicdisplay) — BSD-3
- [Tone.js](https://tonejs.github.io/) — MIT
- [@tonejs/piano](https://github.com/tambien/Piano) — MIT
- [Salamander Grand Piano V3](https://sfzinstruments.github.io/pianos/salamander/) — CC-BY 3.0
- [WebMidi.js](https://github.com/djipco/webmidi) — Apache-2.0
- [@tonejs/midi](https://github.com/Tonejs/Midi) — MIT
- [ts-fsrs](https://github.com/open-spaced-repetition/ts-fsrs) — MIT
- [pianoplayer (Marco Musy)](https://github.com/marcomusy/pianoplayer) — MIT
- [Aalto Pori concert hall IRs](http://legacy.spa.aalto.fi/projects/poririrs/)
- [Mutopia Project](https://www.mutopiaproject.org/) — CC/PD/GFDL
- [OpenScore Lieder Corpus](https://github.com/OpenScore/Lieder) — CC0
- [IMSLP](https://imslp.org/) — Mostly PD

### Pedagoji & Müzik Teorisi
- [musictheory.net/lessons](https://www.musictheory.net/lessons) — Ricci Adams
- Beyer, F. *Vorschule im Klavierspiel Op. 101* (Schott)
- Faber, N. & R. *Piano Adventures* — pianoadventures.com
- Neuhaus, H. *The Art of Piano Playing* (1958)
- Cortot, A. *Rational Principles of Piano Technique* (1928)
- Bernstein, S. *With Your Own Two Hands* (1981)
- Klickstein, G. *The Musician's Way* (2009)
- Kostka & Payne. *Tonal Harmony* 8th ed. (2017)
- Roskell, P. *The Art of Piano Fingering* (2020)
- abrsm.org, rcmusic.com, trinitycollege.com, henle.de, pianostreet.com

### Akademik
- Parncutt, R. et al. (1997). "An ergonomic model of keyboard fingering for melodic fragments." *Music Perception* 14(4)
- Ericsson, K.A. et al. (1993). "The Role of Deliberate Practice in the Acquisition of Expert Performance." *Psychological Review* 100(3)
- Pascual-Leone, A. et al. (1995). "Modulation of muscle responses..." *Journal of Neurophysiology* 74(3)
- Herholz, S.C. & Zatorre, R.J. (2012). "Musical Training as a Framework for Brain Plasticity." *Neuron* 76(3)
- Nakamura et al. (2020). "Statistical Learning and Estimation of Piano Fingering." *Information Sciences*
- Ramoneda et al. (2023). Automatic fingering with GNN/transformers. UPF Barcelona.
- Bjork, R.A. (1994). "Memory and metamemory considerations in the training of human beings." *Metacognition: Knowing about knowing*
- Schmidt, R.A. & Lee, T.D. (2019). *Motor Control and Learning* (6th ed.)
- Altenmüller, E. & Furuya, S. (2017). "Brain Plasticity and the Concept of Metaplasticity in Skilled Musicians."

### Standartlar
- [Web Audio API](https://www.w3.org/TR/webaudio/) — W3C
- [Web MIDI API](https://www.w3.org/TR/webmidi/) — W3C
- [MusicXML 4.0](https://www.w3.org/2021/06/musicxml40/) — W3C Music Notation Community Group
- [WCAG 2.1](https://www.w3.org/TR/WCAG21/) — W3C
- [GDPR](https://gdpr.eu/) — EU
- [KVKK](https://www.kvkk.gov.tr/) — Türkiye

---

**End of Quality Gate Report v1.0.0**

> **Next step:** User onayı → `/plan` workflow → Phase 1 Sprint 1 başlangıç.
