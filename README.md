# MIHENK

**M**ultilingual **I**ntelligence, **H**igh-level **E**valuation and **N**eural **K**nowledge Benchmark

İki dilli (Türkçe + İngilizce), çok disiplinli, **akıl yürütme odaklı** bir LLM değerlendirme benchmark'ı. MIHENK; modellerin ezberlenmiş bilgiyi geri çağırma kapasitesini değil, **çıkarım yapma, dikkatli okuma, dil hakimiyeti ve çok adımlı problem çözme** becerilerini ölçer.

> Sürüm 1.0 • Faz 1 (Pilot Set) • 20 disiplin × 2 dil × 2 format × 10 soru = **800 soru**

## Tasarım prensipleri

- **İki dilli:** Her soru hem `tr` hem `en` olarak kurgulanır — birebir çeviri değil, her dilin kendi doğal yapısına (deyim, sözdizimi, kültürel bağlam) göre ayrı ayrı yerelleştirilir.
- **İki format:** Çoktan seçmeli (`multiple_choice`, 4-5 şık) ve kısa cevaplı (`short_answer`, ≤7 kelime). İkisi de otomatik ve objektif puanlanabilir.
- **Akıl yürütme önceliği:** Sorular verilen bilgiden çıkarımla çözülür; gereksiz/dikkat dağıtıcı bilgi ve yaygın hata örüntülerini yansıtan çeldiriciler içerir.
- **Zorluk katmanları:** L1 (temel) → L4 (uzman), dört seviyeye dengeli dağılım.
- **Orijinallik ve şeffaflık:** Tüm sorular sıfırdan üretilmiştir. Hiçbir telifli sınav bankası (ÖSYM, SAT, GRE, dershane yayınları vb.) kopyalanmamış veya paraphrase edilmemiştir — yalnızca **stil ve zorluk referansı** alınmıştır. Her kaydın `source` alanı `"orijinal-AI-üretim"`tir.

## Disiplinler (20)

Matematik · Mantık · Programlama · Debugging · Türkçe Dil Bilgisi · İngilizce Dil Bilgisi · Tarih · Coğrafya · Fizik · Kimya · Biyoloji · Hukuk · Ekonomi · Felsefe · Günlük Muhakeme · Sayısal Akıl Yürütme · Tablo ve Veri Yorumu · Grafik Analizi · Çok Adımlı Muhakeme · Bilimsel Makale Anlama

## Repo yapısı

```
data/{tr,en}/{disiplin-slug}/{multiple_choice,short_answer}.jsonl
schema/question_schema.json      # JSON Schema (draft-07)
scoring/                         # otomatik puanlama (score.py, normalize.py)
scripts/validate.py              # şema + kural doğrulaması
scripts/build_hf.py              # HuggingFace public sample üretimi
config/disciplines.json          # disiplin kaydı (slug/abbr/ad)
huggingface/                     # HF dataset card + public split
versions/CHANGELOG.md
```

Disiplin slug ↔ kısaltma eşlemesi `config/disciplines.json` içindedir (örn. `matematik` → `MATH`).

## Soru şeması

```json
{
  "id": "MIHENK-MATH-TR-L3-0001",
  "language": "tr",
  "discipline": "Matematik",
  "format": "multiple_choice",
  "difficulty": "L3",
  "question": "...",
  "choices": { "A": "...", "B": "...", "C": "...", "D": "..." },
  "answer": "C",
  "answer_short": null,
  "explanation": "Çözümün kısa gerekçesi",
  "tags": ["çok-adımlı", "oran-orantı"],
  "source": "orijinal-AI-üretim",
  "version_added": "1.0",
  "split": "public"
}
```

- `id`: `MIHENK-{DISIPLIN}-{DIL}-{ZORLUK}-{SIRA}`.
- Kısa cevaplı sorularda `choices` ve `answer` `null`; kanonik cevap `answer_short`, kabul edilebilir varyasyonlar `answer_aliases` dizisinde.
- `split`: `public` (HF örnek/dev split, ~%10-15) veya `private` (contamination'a karşı holdout).

## Değerlendirme

- **MC:** Model çıktısından seçilen harf regex ile ayrıştırılır; doğru harfle birebir eşleşme 1 puan (`scoring/score.py`).
- **Short answer:** Küçük harf + noktalama/boşluk normalize + kanonik/eş anlamlı eşleşme; sayısal cevaplarda tolerans. 7 kelimeyi aşan veya format dışı yanıt otomatik 0 puan.
- Metrikler: genel / disiplin / dil / zorluk / format bazlı doğruluk + dil tutarlılık endeksi.

## Kullanım

```bash
pip install -r requirements.txt

# Tüm veriyi doğrula (jsonschema yoksa dahili manuel kontrole düşer)
python scripts/validate.py --stats

# HuggingFace public sample'ı üret
python scripts/build_hf.py
```

Puanlama örneği:

```python
from scoring.score import score_item
score_item(record, model_output_str)  # -> 0 veya 1
```

## Contamination (kirlenme) notu

HuggingFace'e **yalnızca `public` split** yüklenir; `private` holdout dağıtılmaz. Bu GitHub reposu kanonik kaynaktır ve tüm soruları içerir — daha katı holdout izolasyonu isteyen ekipler repoyu private tutmalı veya holdout'u ayrı bir private repoya taşımalıdır.

## Lisans

- **Kod** (scripts, scoring, schema): MIT (`LICENSE`).
- **Veri** (`data/`): CC BY 4.0 önerilir — atıf ile serbest kullanım.

## Alıntı

```bibtex
@misc{mihenk2026,
  title  = {MIHENK: Multilingual Intelligence, High-level Evaluation and Neural Knowledge Benchmark},
  year   = {2026},
  note   = {v1.0, Faz 1 Pilot Set},
  url    = {https://github.com/gorkemergune/mihenk-benchmark}
}
```
