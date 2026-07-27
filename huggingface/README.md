---
license: cc-by-4.0
language:
  - tr
  - en
pretty_name: MIHENK
size_categories:
  - n<1K
task_categories:
  - question-answering
  - multiple-choice
  - text-classification
tags:
  - benchmark
  - reasoning
  - turkish
  - bilingual
  - evaluation
  - llm
configs:
  - config_name: default
    data_files:
      - split: public
        path: data/mihenk_public.jsonl
---

# MIHENK — Multilingual Intelligence, High-level Evaluation and Neural Knowledge Benchmark

İki dilli (Türkçe + İngilizce), çok disiplinli, **akıl yürütme odaklı** bir LLM değerlendirme benchmark'ı.

Bu HuggingFace deposu benchmark'ın **public sample (dev) split**'idir. Kirlenmeyi (contamination) önlemek için soruların çoğunluğu **private holdout** olarak tutulur ve burada dağıtılmaz. Kanonik/tam repo: <https://github.com/gorkemergune/mihenk-benchmark>

## Ne ölçer?

Ezberlenmiş bilgiyi değil; **çıkarım, dikkatli okuma, dil hakimiyeti ve çok adımlı problem çözme** becerisini. 20 disiplin, 4 zorluk seviyesi (L1–L4), iki format:

- `multiple_choice` — 4-5 şık, tek doğru cevap, exact-match puanlama.
- `short_answer` — ≤7 kelime, normalize edilmiş kanonik/eş anlamlı eşleşme.

## Alanlar

| Alan | Açıklama |
|---|---|
| `id` | `MIHENK-{DISIPLIN}-{DIL}-{ZORLUK}-{SIRA}` |
| `language` | `tr` / `en` |
| `discipline` | Kanonik disiplin adı (ör. `Matematik`) |
| `format` | `multiple_choice` / `short_answer` |
| `difficulty` | `L1`–`L4` |
| `question` | Soru metni |
| `choices` | MC için `{A,B,C,D(,E)}`; short_answer'da `null` |
| `answer` | MC için doğru şık harfi; short_answer'da `null` |
| `answer_short` | short_answer için kanonik cevap; MC'de `null` |
| `answer_aliases` | (ops.) kabul edilebilir eş anlamlı cevaplar |
| `explanation` | Çözümün kısa gerekçesi |
| `tags` | Etiketler |
| `source` | Her zaman `orijinal-AI-üretim` |
| `split` | Bu depoda yalnızca `public` |

## Kullanım

```python
from datasets import load_dataset

ds = load_dataset("gorkemergune/mihenk-benchmark", split="public")
print(ds[0])

# Örnek: yalnızca Türkçe çoktan seçmeli
tr_mc = ds.filter(lambda r: r["language"] == "tr" and r["format"] == "multiple_choice")
```

## Puanlama

- **MC:** model çıktısından seçilen harf regex ile ayrıştırılır; doğru harfle birebir eşleşme 1 puan.
- **Short answer:** küçük harf + noktalama/boşluk normalize + kanonik/eş anlamlı eşleşme; sayısal cevaplarda tolerans. 7 kelimeyi aşan veya format dışı yanıt otomatik 0 puan.

Referans puanlama kodu GitHub reposundaki `scoring/` dizinindedir.

## Şeffaflık ve orijinallik

Tüm sorular **sıfırdan özgün** üretilmiştir. Hiçbir telifli sınav bankası (ÖSYM, SAT, GRE, dershane yayınları vb.) kopyalanmamış veya paraphrase edilmemiştir; yalnızca stil/zorluk referansı alınmıştır. Bu nedenle `source` alanı her kayıtta `orijinal-AI-üretim` olarak şeffafça belirtilir.

## Lisans

Veri: **CC BY 4.0**. Kod (GitHub): MIT.

## Alıntı

```bibtex
@misc{mihenk2026,
  title  = {MIHENK: Multilingual Intelligence, High-level Evaluation and Neural Knowledge Benchmark},
  year   = {2026},
  note   = {v1.0, Faz 1 Pilot Set},
  url    = {https://github.com/gorkemergune/mihenk-benchmark}
}
```
