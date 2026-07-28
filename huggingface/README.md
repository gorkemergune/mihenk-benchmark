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

## Abstract

MIHENK is a bilingual (Turkish–English), multi-disciplinary benchmark for evaluating large language models on **reasoning rather than memorized recall** — inference from given information, careful reading under distraction, language mastery, and multi-step problem solving — across 20 disciplines and four difficulty tiers (L1–L4). All items are automatically and objectively scorable.

This repository hosts the **public sample (development) split**. To limit training-data contamination, the majority of items are retained as a **private holdout** and are not distributed here. The canonical, full repository (all items, scorer, validators, evaluation harness) is at <https://github.com/gorkemergune/mihenk-benchmark>.

## Task and formats

Each item appears in both languages (localized, not literally translated), enabling per-language reporting and a language-consistency measure. Two formats:

- `multiple_choice` — 4–5 options, one correct answer, exact-letter scoring.
- `short_answer` — ≤ 7 words, normalized canonical/alias match with numeric tolerance.

Distractors encode common error patterns (arithmetic slips, hasty generalization, misremembering) rather than being random, so shallow heuristics are penalized.

## Fields

| Field | Description |
|---|---|
| `id` | `MIHENK-{DISCIPLINE}-{LANG}-{DIFFICULTY}-{SEQ}` |
| `language` | `tr` / `en` |
| `discipline` | Canonical discipline name (Turkish string, language-neutral key) |
| `format` | `multiple_choice` / `short_answer` |
| `difficulty` | `L1`–`L4` |
| `question` | Question text |
| `choices` | `{A,B,C,D(,E)}` for MC; `null` for short answer |
| `answer` | Correct option letter for MC; `null` for short answer |
| `answer_short` | Canonical answer for short answer; `null` for MC |
| `answer_aliases` | (optional) accepted synonymous answers |
| `explanation` | Short **English** rationale (metadata; never shown to the model) |
| `tags` | Topic tags |
| `source` | Always `orijinal-AI-üretim` (original AI generation) |
| `split` | `public` in this repository |

## Usage

```python
from datasets import load_dataset

ds = load_dataset("gorkemergune/mihenk-benchmark", split="public")
print(ds[0])

# Example: Turkish multiple-choice only
tr_mc = ds.filter(lambda r: r["language"] == "tr" and r["format"] == "multiple_choice")
```

## Evaluation

Standardized conditions: a fixed system prompt per format, 0-shot, a fixed decoding configuration, and a single run by default.

- **Multiple choice:** the selected letter is parsed by regex; an exact match with the correct letter scores 1.
- **Short answer:** lowercasing + punctuation/whitespace normalization + canonical/alias match with numeric tolerance; any answer exceeding 7 words or off-format automatically scores 0.

Reported metrics: overall, per-discipline, per-language, per-difficulty, and per-format accuracy, plus a language-consistency index (the mean absolute TR/EN accuracy gap). The reference scorer and a runnable evaluation harness (`scripts/evaluate.py`) are in the GitHub repository.

## Originality and transparency

All items are written from scratch. No copyrighted examination bank (ÖSYM, SAT, GRE, prep-school publications, etc.) is copied or paraphrased; only their style and difficulty calibration are used as a reference. Accordingly, `source` is set transparently to `orijinal-AI-üretim` on every record.

## Licensing

Data: **CC BY 4.0**. Code (GitHub): MIT.

## Citation

```bibtex
@misc{mihenk2026,
  title  = {MIHENK: Multilingual Intelligence, High-level Evaluation and Neural Knowledge Benchmark},
  year   = {2026},
  note   = {Version 1.0, Phase 1 Pilot Set},
  url    = {https://github.com/gorkemergune/mihenk-benchmark}
}
```
