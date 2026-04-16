# 🎹 Piyano Stüdyosu — Görsel Pratik

> Tek dosyada çalışan, sıfır bağımlılık gerektiren, görsel ve sesli piyano pratik stüdyosu.
> Tarayıcıda açın, çalışsın — kurulum yok, hesap yok, internet bağlantısı bile gerekmez.

[![Live Demo](https://img.shields.io/badge/demo-live-success?logo=github)](https://emredursun.github.io/piano-practice/)
[![Status](https://img.shields.io/badge/status-stable-success)]()
[![License](https://img.shields.io/badge/license-MIT-blue)](#-lisans)
[![Made with](https://img.shields.io/badge/made%20with-vanilla%20JS-yellow)]()
[![No Dependencies](https://img.shields.io/badge/dependencies-0-brightgreen)]()
[![Browser Support](https://img.shields.io/badge/browser-modern-informational)]()

**🌐 Canlı Demo:** [emredursun.github.io/piano-practice](https://emredursun.github.io/piano-practice/)

---

## 📖 İçindekiler

- [Özet](#-özet)
- [Özellikler](#-özellikler)
- [Hızlı Başlangıç](#-hızlı-başlangıç)
- [Kullanım Kılavuzu](#-kullanım-kılavuzu)
- [Klavye Kısayolları](#-klavye-kısayolları)
- [Dersler](#-dersler)
- [Görsel İpucu Sistemi](#-görsel-ipucu-sistemi)
- [Teknik Mimari](#-teknik-mimari)
- [Tarayıcı Desteği](#-tarayıcı-desteği)
- [Erişilebilirlik](#-erişilebilirlik)
- [Yol Haritası](#-yol-haritası)
- [Katkıda Bulunma](#-katkıda-bulunma)
- [Sıkça Sorulan Sorular](#-sıkça-sorulan-sorular)
- [Lisans](#-lisans)

---

## 🎯 Özet

**Piyano Stüdyosu**, piyano öğrenmeye yeni başlayanlar için tasarlanmış, tarayıcıda çalışan bir görsel pratik aracıdır. Hangi tuşa basacağınızı, hangi parmağı kullanacağınızı ve sıradaki notayı tek bakışta görmenizi sağlar. Web Audio API ile gerçek zamanlı ses sentezi yapar; herhangi bir ses dosyasına ihtiyaç duymaz.

**Neden bu proje?**

Piyano öğrenmek isteyen yeni başlayanların büyük kısmı iki şeyi bir arada çözmek zorunda: (1) hangi nota nerede, (2) hangi parmak nereye. Geleneksel uygulamalar genellikle ya çok karmaşık (DAW seviyesi) ya da çok satıştır (subscription duvarı). Bu proje, **bir HTML dosyası** ile bu iki temel ihtiyacı görselleştirip pratik döngüsünü kapatmayı amaçlar.

---

## ✨ Özellikler

| Özellik | Açıklama |
|---|---|
| 🎼 **2 Oktav Sanal Piyano** | C4'ten C6'ya 15 beyaz + 10 siyah tuş |
| 🎵 **Web Audio API Sentezi** | Triangle + sine osilatörlü, harmonik zenginlikli ton üretimi |
| 👆 **Parmak Numarası Rehberi** | Her tuşun üzerinde kullanılacak parmak (1=başparmak, 5=serçe) |
| 🔵 **Sıradaki Tuş İpucu** | Mavi yanıp sönen tuş bir sonraki adımı gösterir |
| 🟠 **Şu Anki Tuş Vurgusu** | Turuncu vurgu ile aktif notayı işaret eder |
| ⏯️ **Otomatik Çalma** | Melodiyi izle, sonra kendin tekrarla |
| 🎚️ **Ayarlanabilir Tempo** | 400ms–1500ms arası özelleştirilebilir hız |
| 📊 **İlerleme Takibi** | Görsel progress bar ve adım sayacı |
| ⌨️ **Bilgisayar Klavyesi Desteği** | A–L tuşlarıyla canlı çalma |
| 📱 **Tamamen Responsive** | Masaüstü, tablet ve mobil uyumlu |
| 🎨 **Premium Tasarım** | Cormorant Garamond + JetBrains Mono tipografi |
| 🚀 **Sıfır Bağımlılık** | Build adımı yok, paket yöneticisi yok, framework yok |
| 🔒 **%100 Çevrimdışı** | İlk yüklemeden sonra internet gerektirmez |

---

## 🚀 Hızlı Başlangıç

### Yöntem 1: Doğrudan Açma (En Kolay)

```bash
# Repoyu klonla
git clone https://github.com/emredursun/piano-practice.git
cd piano-practice

# Tarayıcıda aç
# Windows
start index.html
# macOS
open index.html
# Linux
xdg-open index.html
```

### Yöntem 2: Yerel Sunucuyla Çalıştırma (Önerilen)

Bazı tarayıcılar `file://` protokolünde Web Audio API'yi kısıtlayabilir. Yerel sunucu kullanmak en güvenli yoldur:

```bash
# Python 3
python -m http.server 8000

# Node.js (npx ile)
npx serve .

# PHP
php -S localhost:8000
```

Ardından tarayıcıda `http://localhost:8000/` adresini açın.

### Yöntem 3: GitHub Pages (Sıfır Kurulum)

Bu projeyi GitHub Pages üzerinden yayınlamak için:

1. Repo `Settings` → `Pages` sekmesine gidin
2. **Source** olarak `main` branch ve `/ (root)` seçin
3. Birkaç saniye sonra **[https://emredursun.github.io/piano-practice/](https://emredursun.github.io/piano-practice/)** üzerinden erişilebilir olur

---

## 📚 Kullanım Kılavuzu

### Adım 1: Bir Ders Seçin

Üstteki ders seçici barından çalışmak istediğiniz dersi tıklayın. Aktif ders turuncu olarak işaretlenir.

### Adım 2: Önce Demo İzleyin

`▶` (Oynat) düğmesine basın. Sistem:

- Notaları sırayla çalar
- **Turuncu** ile şu an çalınan tuşu vurgular
- **Mavi yanıp sönen** ile sıradaki tuşu gösterir
- Her tuş üzerinde **parmak numarasını** belirtir

### Adım 3: Tempoyu Yavaşlatın

Karmaşık dersler için tempo kaydırıcısını sağa çekin (örneğin 1200ms). Yavaş tempoda göz ile takip etmek çok daha kolaydır.

### Adım 4: Kendiniz Deneyin

`↻` (Sıfırla) ile başa dönün. `◀` (Geri) ile önceki adıma gidin. Gerçek piyanonuzla veya bilgisayar klavyesinde A–L tuşlarıyla aynı sırayı tekrarlayın.

### Adım 5: Hızı Kademeli Artırın

Bir desen ezberlendiğinde, tempoyu hızlandırın (700ms → 500ms → 400ms). Bu, **klasik konservatuvar metodolojisinin** kalbidir.

---

## ⌨️ Klavye Kısayolları

| Tuş | Aksiyon |
|---|---|
| `Space` | Çal / Duraklat |
| `A` | C4 (Do) |
| `W` | C#4 (Do diyez) |
| `S` | D4 (Re) |
| `E` | D#4 (Re diyez) |
| `D` | E4 (Mi) |
| `F` | F4 (Fa) |
| `T` | F#4 (Fa diyez) |
| `G` | G4 (Sol) |
| `Y` | G#4 (Sol diyez) |
| `H` | A4 (La) |
| `U` | A#4 (La diyez) |
| `J` | B4 (Si) |
| `K` | C5 (Do oktav üst) |
| `O` | C#5 |
| `L` | D5 |

> 💡 **İpucu:** Beyaz tuşlar `A-S-D-F-G-H-J-K-L` ile, siyah tuşlar üst sıradaki `W-E-T-Y-U-O` ile eşleşir — gerçek bir piyano düzenini taklit eder.

---

## 🎼 Dersler

| Ders | Zorluk | İçerik |
|---|---|---|
| **C Majör Gamı** | ⭐ Başlangıç | Tüm beyaz tuşlar — piyanonun temel gamı, parmak değiştirme pratiği |
| **G Majör Gamı** | ⭐⭐ Başlangıç+ | F# içerir — siyah tuş geçişine giriş |
| **Parlak Yıldız** | ⭐ Başlangıç | "Twinkle Twinkle Little Star" — klasik melodi pratiği |
| **Neşeye Övgü** | ⭐⭐ Başlangıç+ | Beethoven 9. Senfoni teması — basitleştirilmiş |
| **C Majör Akoru** | ⭐⭐ Akor | C-E-G üçlü akoru — birden fazla tuşa eşzamanlı basma temeli |
| **Kromatik** | ⭐⭐⭐ Orta | Tüm 12 tuş ardışık — parmak bağımsızlığı ve çeviklik |

---

## 🎨 Görsel İpucu Sistemi

Renk kodları öğrenme sürecini görsel olarak yönlendirir:

| Renk | Anlam |
|---|---|
| 🟠 **Turuncu (parlak)** | Şu an çalınan tuş |
| 🔵 **Mavi (yanıp sönen)** | Sıradaki tuş — hazırlanın |
| 🟢 **Açık turuncu** | Vurgulanan tuş (statik gösterim) |
| ⚪ **Beyaz** | Pasif beyaz tuş |
| ⚫ **Siyah** | Pasif siyah tuş |
| ⚪→🟠 (basıldığında) | Çalma anı animasyonu |

Parmak numaraları her aktif tuşun üzerinde **siyah daire içinde turuncu rakam** olarak görünür.

---

## 🏗️ Teknik Mimari

### Dosya Yapısı

```
piano-practice/
├── index.html             # Tüm uygulama (HTML + CSS + JS)
└── README.md              # Bu dosya
```

**Tek dosya yaklaşımının nedenleri:**

- ✅ Sıfır build adımı, sıfır toolchain
- ✅ E-postaya iliştirip paylaşılabilir
- ✅ USB'ye atılıp başka bilgisayarda çalıştırılabilir
- ✅ GitHub'da tek tıkla raw görüntülenebilir
- ✅ Beginner için "neyin nerede olduğu" sorusu yok

### Teknoloji Yığını

| Katman | Teknoloji | Kullanım |
|---|---|---|
| **Görsel** | HTML5 + CSS3 (Grid, Flexbox, Custom Properties) | Layout ve tasarım |
| **Etkileşim** | Vanilla JavaScript (ES6+) | Tüm uygulama mantığı |
| **Ses** | Web Audio API (Oscillator + Gain) | Ses sentezi |
| **Tipografi** | Google Fonts (Cormorant Garamond, JetBrains Mono) | Premium görünüm |
| **Animasyon** | CSS Transitions + Keyframes | Yumuşak geçişler |

### Ses Sentezi Detayı

Her nota iki osilatörden üretilir:

```javascript
// Temel ton: Triangle dalga (yumuşak, piyano benzeri)
osc.type = 'triangle';
osc.frequency.value = freq;

// Harmonik ek: Sine dalga (1 oktav üstü, parlaklık katar)
osc2.type = 'sine';
osc2.frequency.value = freq * 2;

// ADSR zarfı (Attack: 10ms, Decay: tempo'ya bağlı)
gain.gain.linearRampToValueAtTime(0.3, now + 0.01);
gain.gain.exponentialRampToValueAtTime(0.01, now + duration);
```

Bu yaklaşım, ses dosyası indirmeden, gerçek piyano tonuna yakın bir ses verir.

### Veri Modeli

Dersler basit bir JS objesi olarak tanımlıdır — yeni ders eklemek için:

```javascript
const LESSONS = {
  'my-lesson': {
    title: 'Yeni Dersim',
    description: 'Açıklama metni',
    notes: [
      { note: 'C4', finger: 1 },
      { note: 'E4', finger: 3 },
      // ... daha fazla nota
    ]
  }
};
```

**Geçerli notalar:** `C4` – `C6` arası tüm doğal ve diyez notalar
**Parmak numaraları:** `1` (başparmak) – `5` (serçe parmak)

---

## 🌐 Tarayıcı Desteği

| Tarayıcı | Minimum Sürüm | Durum |
|---|---|---|
| Chrome / Edge | 90+ | ✅ Tam destek |
| Firefox | 88+ | ✅ Tam destek |
| Safari | 14+ | ✅ Tam destek |
| Opera | 76+ | ✅ Tam destek |
| Mobil Safari (iOS) | 14+ | ✅ Tam destek |
| Chrome Mobile (Android) | 90+ | ✅ Tam destek |
| Internet Explorer | — | ❌ Desteklenmiyor |

> **Not:** Web Audio API bazı tarayıcılarda kullanıcı etkileşimi olmadan başlatılamaz. İlk tıklamadan sonra ses devreye girer (bu, tarayıcı güvenlik politikasının bir parçasıdır).

---

## ♿ Erişilebilirlik

- **Klavye gezinimi:** Tüm dersler ve tuşlar klavye ile erişilebilir
- **Görsel kontrast:** Turuncu vurgu rengi (WCAG AA uyumlu kontrast oranı)
- **Responsive ölçek:** Mobilde tuş boyutu otomatik ayarlanır
- **Animasyon:** `prefers-reduced-motion` desteği yol haritasında

---

## 🛣️ Yol Haritası

### v1.1 — Yakında
- [ ] LocalStorage ile ilerleme kaydetme
- [ ] `prefers-reduced-motion` desteği
- [ ] Daha fazla ders (D, A, E, F majör gamları)
- [ ] Minör gamlar (A minör, E minör)

### v1.2 — Planlı
- [ ] MIDI klavye girişi (Web MIDI API)
- [ ] Metronom / tempo modu
- [ ] Kendi dersini oluştur arayüzü
- [ ] JSON olarak ders dışa/içe aktarma

### v2.0 — Vizyon
- [ ] Çoklu dil desteği (TR / EN)
- [ ] Akor kütüphanesi (majör, minör, 7'li)
- [ ] Mikrofon girişi ile çaldığını dinleme (pitch detection)
- [ ] PWA (Progressive Web App) sürümü

---

## 🤝 Katkıda Bulunma

Katkılar memnuniyetle karşılanır! Lütfen şu adımları izleyin:

1. **Fork** edin
2. Yeni bir feature branch açın (`git checkout -b feature/yeni-ders`)
3. Değişikliklerinizi commit edin (`git commit -m 'feat: D majör gamı dersi eklendi'`)
4. Branch'i push edin (`git push origin feature/yeni-ders`)
5. **Pull Request** açın

### Katkı Türleri

| Tür | Etiket |
|---|---|
| Yeni ders eklemek | `lesson` |
| UI/UX iyileştirmesi | `ui` |
| Bug fix | `bug` |
| Dokümantasyon | `docs` |
| Erişilebilirlik | `a11y` |
| Performans | `perf` |

### Commit Mesaj Formatı

[Conventional Commits](https://www.conventionalcommits.org/) standardı kullanılır:

```
<tip>: <kısa açıklama>

[opsiyonel gövde]
```

**Tipler:** `feat`, `fix`, `refactor`, `docs`, `style`, `perf`, `chore`

---

## ❓ Sıkça Sorulan Sorular

**S: Sayfa açılıyor ama ses çıkmıyor?**
C: Tarayıcılar, kullanıcı etkileşimi olmadan ses çalmayı engeller. Herhangi bir tuşa basın veya `▶` düğmesine tıklayın.

**S: Mobilde nasıl kullanırım?**
C: Tarayıcıda direkt açın. Tuşlara dokunarak çalabilirsiniz. Bilgisayar klavyesi kısayolları haricinde tüm özellikler çalışır.

**S: Yeni ders nasıl eklerim?**
C: `index.html` dosyasını açın, `LESSONS` objesini bulun ve [Veri Modeli](#veri-modeli) bölümündeki formatı izleyin.

**S: Gerçek piyano sesine yakın mı?**
C: Web Audio API'nin sentezlediği bir ton — gerçek piyano sample'ı değil. Ancak öğrenme amaçlı yeterince temsilî.

**S: Notaları nasıl okurum (do, re, mi, ...)?**
C: Uluslararası notasyon kullanılır: C=Do, D=Re, E=Mi, F=Fa, G=Sol, A=La, B=Si. `#` işareti diyez (yarım ton yukarı) anlamına gelir.

**S: MIDI klavyem var, kullanabilir miyim?**
C: Şu an için hayır — yol haritasında. v1.2 ile gelecek.

**S: Verilerim nereye kaydediliyor?**
C: Hiçbir yere. Tüm uygulama %100 client-side çalışır. Hiçbir sunucu çağrısı, çerez, analytics yoktur.

---

## 📜 Lisans

MIT Lisansı © 2026 [Emre Dursun](https://github.com/emredursun)

```
Bu yazılım, MIT lisansı altında ücretsiz olarak dağıtılmaktadır.
Ticari ve şahsi projelerinizde özgürce kullanabilir, değiştirebilir ve dağıtabilirsiniz.
```

---

## 🙏 Teşekkürler

- **Google Fonts** — Cormorant Garamond ve JetBrains Mono tipografi
- **Web Audio API spesifikasyonu** — W3C
- **Klasik piyano metodu** — Beyer, Czerny, Hanon (ilham kaynağı)

---

<div align="center">

**🎹 İyi pratikler! 🎹**

[Bug Bildir](https://github.com/emredursun/piano-practice/issues) ·
[Özellik İste](https://github.com/emredursun/piano-practice/issues/new) ·
[Tartışmalar](https://github.com/emredursun/piano-practice/discussions)

</div>
