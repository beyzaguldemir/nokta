# Nokta — Community-Driven mobile.md

> 1 dosya, 0 insan review. CI karar verir.

## Ne?

`mobile.md` Nokta mobil uygulamasının Karpathy-tarzı spec dosyasıdır. Bu dosya, AI agent'ların (Claude Code / Codex) uygulamayı sıfırdan inşa edebileceği tam bir spesifikasyon içerir.

**İnsan spec yazar, makine kod yazar.**

## Nasıl Çalışır?

```
Bir section seçersin
    ↓
Branch açarsın: section/04-data-contracts
    ↓
mobile.md'nin ilgili section'ını yazarsın
    ↓
PR açarsın
    ↓
CI otomatik skorlar (0-100)
    ↓
Skor ≥ main'deki mevcut skor → ✅ Otomatik merge
Skor < main'deki mevcut skor → ❌ Otomatik reject
    ↓
Düzelt, push et, tekrar dene
```

**İnsan review yok. Metrik karar veriyor. Score asla düşemez.**

## Hızlı Başlangıç

```bash
# 1. Repo'yu fork'la ya da clone'la
git clone https://github.com/seyyah/nokta.git
cd nokta

# 2. Branch oluştur (section numaranı seç)
git checkout -b section/04-data-contracts

# 3. mobile.md'yi aç, ilgili section'ı bul, yaz
# Her section'ın içinde <!-- YORUM --> olarak talimatlar var

# 4. Skorunu kontrol et (opsiyonel, local test)
pip install pyyaml
python scripts/section_score.py --section 4

# 5. Commit + push
git add mobile.md
git commit -m "feat(section-04): add data contracts"
git push origin section/04-data-contracts

# 6. GitHub'da PR aç → CI otomatik çalışır → sonucu bekle
```

## Section Listesi

| # | Section | İçerik |
|---|---------|---------|
| 0 | IMMUTABLE INFRA | Dokunulamaz dosyalar listesi |
| 1 | IDENTITY | ✅ Referans olarak dolu |
| 2 | SETUP PROTOCOL | Sıfırdan çalışan app'e kurulum adımları |
| 3 | NON-GOALS | v0.1'de yapılmayacaklar |
| 4 | DATA CONTRACTS | TypeScript interface'leri ve storage şeması |
| 5 | SCREEN & FEATURE SPEC | 3 ekran, UX akışı, component listesi |
| 6 | UI CONFIG | Local JSON-driven UI config sistemi |
| 7 | LLM CONTRACT | Mock LLM, prompt template, golden transcripts |
| 8 | OBJECTIVE FUNCTION | Hard gate'ler + tek skaler metrik |
| 9 | THE RATCHET LOOP | PR → CI → measure → keep/revert döngüsü |
| 10 | CONTRIBUTION PROTOCOL | Branch, commit, PR kuralları |
| 11 | ARCHITECTURAL INVARIANTS | Kod yapısı kuralları |
| 12 | TESTING PHILOSOPHY | Test standartları ve anti-pattern'ler |

## Skorlama

Her section için bir checklist vardır (`checklists/section_XX.yml`). CI bu checklist'i okuyarak section'ı değerlendirir:

- **Yapısal kontroller:** Gerekli başlıklar var mı? Minimum kelime sayısı geçildi mi?
- **İçerik kontrolleri:** Belirli kavramlar/pattern'ler var mı? (ör: TypeScript code block, JSON schema)
- **Her kontrol bir ağırlık taşır.** Toplam 100 puan üzerinden skorlanır.

**Puanlama kuralı:** Skorun main branch'teki mevcut skordan **düşük olamaz.** Eşit veya yüksekse merge olur.

### Local'de Test Et

```bash
# Tek section
python scripts/section_score.py --section 4

# Tüm section'lar
python scripts/section_score.py --all

# CI formatında çıktı
python scripts/section_score.py --all --ci-comment
```

## Kurallar

1. **Sadece `mobile.md` dosyasını düzenle.** Checklist YAML'ları, CI workflow'u ve scripts'i düzenleme — bunlar IMMUTABLE INFRA.
2. **Section 1 (IDENTITY) zaten dolu.** Referans olarak kullan, formatını takip et.
3. **Birden fazla kişi aynı section'ı yazabilir.** En yüksek skoru alan merge edilir.
4. **TODO placeholder'ları sil.** `> TODO:` satırları kaldığı sürece section skoru düşük kalır.
5. **AI araçları kullanabilirsin** — vibe-writing serbest. Ama checklist'i geç.
6. **Türkçe veya İngilizce** yazabilirsin. Checklist her iki dili de destekler.

## SSS

**S: Aynı section'ı başka biri de yazıyorsa ne olur?**
İlk merge olan kazanır. Sonra gelen PR, o skoru geçmek zorunda. Ratchet.

**S: PR'ım reddedildi, ne yapmalıyım?**
CI comment'ına bak — hangi checklist item'ları fail olmuş göreceksin. Düzelt, push et, CI tekrar koşar.

**S: Section 1 zaten dolu, onu geliştirebilir miyim?**
Evet — ama mevcut skoru (100/100) geçmen lazım. İçeriği silersen skor düşer, reject olursun.

**S: Birden fazla section katkısı verebilir miyim?**
Evet, her biri ayrı branch + ayrı PR.
