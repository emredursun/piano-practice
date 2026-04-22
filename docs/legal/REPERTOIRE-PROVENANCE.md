# Repertoire Provenance — Piano-Practice

> **Scope:** Every piece in the corpus has a row here. PD status is checked per jurisdiction.
> **Authority:** `MASTER-PLAN.md §5.10`, §7.1; ADR-011.
> **Review cadence:** annually by legal counsel + on every corpus addition.

---

## 1. Phase 1 Corpus — Bach Two-Part Inventions

**Composer:** Johann Sebastian Bach (1685–1750). In the public domain globally.
**Source edition:** Bach-Gesellschaft Ausgabe, Band III (1853). PD globally (editor and publisher long past any applicable term).
**Engraving:** re-engraved in MuseScore 4 by the founder; exportable MusicXML. Original engraving is our own work.
**Editorial fingering bundled:** Czerny (1840), Busoni (1894). Both PD in EU/TR/UK/US (editor terms elapsed).
**AI fingering:** computed by `pianoplayer` (MIT) implementing Parncutt 1997 (academic algorithm, no IP encumbrance).
**Reference recording:** founder's own performance, released CC-BY-NC 4.0.

### 1.1 Piece rows (initial)

| Piece ID | Work | Editor fingerings | Engraver | License | PD (TR/EU/UK/US) | Notes |
|---|---|---|---|---|---|---|
| `bwv772` | Invention No. 1 in C major | Czerny 1840, Busoni 1894 | Founder (MuseScore 4) | CC-BY 4.0 (engraving) | ✅ / ✅ / ✅ / ✅ | |
| `bwv773` | Invention No. 2 in C minor | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv774` | Invention No. 3 in D major | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv775` | Invention No. 4 in D minor | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv776` | Invention No. 5 in E♭ major | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv777` | Invention No. 6 in E major | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv778` | Invention No. 7 in E minor | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv779` | Invention No. 8 in F major | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv780` | Invention No. 9 in F minor | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv781` | Invention No. 10 in G major | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv782` | Invention No. 11 in G minor | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv783` | Invention No. 12 in A major | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv784` | Invention No. 13 in A minor | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv785` | Invention No. 14 in B♭ major | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |
| `bwv786` | Invention No. 15 in B minor | Czerny 1840, Busoni 1894 | Founder | CC-BY 4.0 | ✅ / ✅ / ✅ / ✅ | |

Rows completed as each piece passes [`../content/PIPELINE.md`](../content/PIPELINE.md).

---

## 2. Third-Party Assets

### 2.1 Salamander Grand V3 samples

- **License:** CC-BY 3.0.
- **Attribution:** "Salamander Grand Piano V3 by Alexander Holm, CC-BY 3.0."
- **Published at:** `/legal/subprocessors`.
- **Cloudflare R2 storage path:** `samples/salamander-v3/`.

### 2.2 OpenSheetMusicDisplay

- **License:** BSD-3.
- Attribution via npm package metadata and CycloneDX SBOM.

### 2.3 Tone.js / @tonejs/piano / @tonejs/midi

- **License:** MIT.

### 2.4 WebMidi.js

- **License:** Apache-2.0.

### 2.5 pianoplayer

- **License:** MIT.

### 2.6 Inter + JetBrains Mono

- **License:** SIL Open Font License 1.1.

All third-party licenses allowed per the CI allowlist (`MASTER-PLAN.md §4.2`).

---

## 3. DMCA / DSM Directive Art. 17

- DMCA agent registered with the US Copyright Office before launch ($6 fee). Even with ADR-018 (no US users), US-hosted CDN points still benefit from the safe harbour.
- EU DSM Directive Art. 17 takedown procedure published at `/legal/takedown`.
- Response target: acknowledge within **1 business day**; remove or counter-notice within **3 business days**.

---

## 4. Re-Verification Calendar

- **Annually:** legal counsel reviews PD status for the full corpus.
- **Ad-hoc:** on every corpus addition.
- **Event-triggered:** if the term of protection changes in any jurisdiction (unlikely but documented as a risk — see [`../risks/REGISTER.md`](../risks/REGISTER.md) R-11).

---

## 5. Phase 2 / 3 Additions

When new pieces are added, each gets a row in §1 with identical fields. Any piece that depends on a non-PD edition (even if the composition itself is PD) goes through the Phase-2 licensing flow (see `MASTER-PLAN.md §7.3`).

---

## 6. References

- `MASTER-PLAN.md §5.10`, §7.1, §7.4
- ADR-011
- [`../content/PIPELINE.md`](../content/PIPELINE.md)
- [`../risks/REGISTER.md`](../risks/REGISTER.md)
