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

A bilingual (Turkish + English), multi-disciplinary, **reasoning-focused** LLM evaluation benchmark.

This HuggingFace repository holds the **public sample (dev) split** of the benchmark. To guard against contamination, the majority of questions are kept as a **private holdout** and are not distributed here. Canonical/full repository: <https://github.com/gorkemergune/mihenk-benchmark>

## What it measures

Not memorized recall, but **inference, careful reading, language mastery, and multi-step problem solving**. 20 disciplines, 4 difficulty tiers (L1–L4), two formats:

- `multiple_choice` — 4–5 options, one correct answer, exact-match scoring.
- `short_answer` — ≤7 words, normalized canonical/alias matching.

## Fields

| Field | Description |
|---|---|
| `id` | `MIHENK-{DISCIPLINE}-{LANG}-{DIFFICULTY}-{SEQ}` |
| `language` | `tr` / `en` |
| `discipline` | Canonical discipline name (e.g. `Matematik`) |
| `format` | `multiple_choice` / `short_answer` |
| `difficulty` | `L1`–`L4` |
| `question` | Question text |
| `choices` | `{A,B,C,D(,E)}` for MC; `null` for short answer |
| `answer` | Correct option letter for MC; `null` for short answer |
| `answer_short` | Canonical answer for short answer; `null` for MC |
| `answer_aliases` | (optional) accepted synonymous answers |
| `explanation` | Short rationale of the solution |
| `tags` | Tags |
| `source` | Always `orijinal-AI-üretim` (original AI generation) |
| `split` | Only `public` in this repository |

## Usage

```python
from datasets import load_dataset

ds = load_dataset("gorkemergune/mihenk-benchmark", split="public")
print(ds[0])

# Example: Turkish multiple-choice only
tr_mc = ds.filter(lambda r: r["language"] == "tr" and r["format"] == "multiple_choice")
```

## Scoring

- **MC:** the selected letter is parsed from the model output with a regex; an exact match with the correct letter scores 1.
- **Short answer:** lowercasing + punctuation/whitespace normalization + canonical/alias matching; numeric answers use a tolerance. Any answer exceeding 7 words or off-format automatically scores 0.

Reference scoring code lives in the `scoring/` directory of the GitHub repository.

## Transparency and originality

All questions are written **from scratch**. No copyrighted exam bank (ÖSYM, SAT, GRE, prep-school publications, etc.) is copied or paraphrased; only their style/difficulty is used as a reference. Accordingly, the `source` field is set transparently to `orijinal-AI-üretim` on every record.

## License

Data: **CC BY 4.0**. Code (GitHub): MIT.

## Citation

```bibtex
@misc{mihenk2026,
  title  = {MIHENK: Multilingual Intelligence, High-level Evaluation and Neural Knowledge Benchmark},
  year   = {2026},
  note   = {v1.0, Phase 1 Pilot Set},
  url    = {https://github.com/gorkemergune/mihenk-benchmark}
}
```
