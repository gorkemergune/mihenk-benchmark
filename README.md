# MIHENK

**M**ultilingual **I**ntelligence, **H**igh-level **E**valuation and **N**eural **K**nowledge Benchmark

A bilingual (Turkish + English), multi-disciplinary, **reasoning-focused** LLM evaluation benchmark. MIHENK measures not a model's ability to recall memorized facts, but its capacity for **inference, careful reading, language mastery, and multi-step problem solving**.

> Version 1.0 • Phase 1 (Pilot Set) • 20 disciplines × 2 languages × 2 formats × 10 questions = **800 questions**

## Design principles

- **Bilingual:** Every question is authored in both `tr` and `en` — not as a literal translation, but localized to each language's natural structure (idioms, syntax, cultural context).
- **Two formats:** Multiple choice (`multiple_choice`, 4–5 options) and short answer (`short_answer`, ≤7 words). Both are automatically and objectively scorable.
- **Reasoning first:** Questions are solved by inference from the given information; they include irrelevant/distracting details and distractors that mirror common error patterns.
- **Difficulty tiers:** L1 (basic) → L4 (expert), balanced across the four levels.
- **Originality & transparency:** All questions are written from scratch. No copyrighted exam bank (ÖSYM, SAT, GRE, prep-school publications, etc.) is copied or paraphrased — only their *style and difficulty calibration* are used as a reference. Every record's `source` field is `"orijinal-AI-üretim"` (original AI generation).

## Disciplines (20)

Mathematics · Logic · Programming · Debugging · Turkish Grammar · English Grammar · History · Geography · Physics · Chemistry · Biology · Law · Economics · Philosophy · Everyday Reasoning · Numerical Reasoning · Table & Data Interpretation · Chart Analysis · Multi-step Reasoning · Scientific Paper Comprehension

## Repository layout

```
data/{tr,en}/{discipline-slug}/{multiple_choice,short_answer}.jsonl
schema/question_schema.json      # JSON Schema (draft-07)
scoring/                         # automatic scoring (score.py, normalize.py)
scripts/validate.py              # schema + rule validation
scripts/build_hf.py              # builds the HuggingFace public sample
config/disciplines.json          # discipline registry (slug/abbr/name)
huggingface/                     # HF dataset card + public split
versions/CHANGELOG.md
```

The discipline slug ↔ abbreviation mapping lives in `config/disciplines.json` (e.g. `matematik` → `MATH`). The canonical `discipline` field stores the Turkish name in every record (a language-neutral key that pairs the TR/EN versions of the same discipline).

## Question schema

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
  "explanation": "Short rationale of the solution",
  "tags": ["multi-step", "ratio"],
  "source": "orijinal-AI-üretim",
  "version_added": "1.0",
  "split": "public"
}
```

- `id`: `MIHENK-{DISCIPLINE}-{LANG}-{DIFFICULTY}-{SEQ}`.
- For short-answer questions `choices` and `answer` are `null`; the canonical answer goes in `answer_short`, with acceptable variants in the `answer_aliases` array.
- `split`: `public` (HF dev/sample split, ~10–15%) or `private` (holdout kept back against contamination).

## Evaluation

- **MC:** The selected letter is parsed from the model output with a regex; an exact match with the correct letter scores 1 (`scoring/score.py`).
- **Short answer:** Lowercasing + punctuation/whitespace normalization + canonical/alias matching; numeric answers use a tolerance. Any answer exceeding 7 words or otherwise off-format automatically scores 0.
- Metrics: overall / per-discipline / per-language / per-difficulty / per-format accuracy, plus a language-consistency index.

## Usage

```bash
pip install -r requirements.txt

# Validate all data (falls back to a built-in checker if jsonschema is absent)
python scripts/validate.py --stats

# Build the HuggingFace public sample
python scripts/build_hf.py
```

Scoring example:

```python
from scoring.score import score_item
score_item(record, model_output_str)  # -> 0 or 1
```

## Contamination note

Only the `public` split is uploaded to HuggingFace; the `private` holdout is not distributed. This GitHub repository is the canonical source and contains all questions — teams wanting stricter holdout isolation should keep the repo private or move the holdout to a separate private repository.

## License

- **Code** (scripts, scoring, schema): MIT (`LICENSE`).
- **Data** (`data/`): CC BY 4.0 recommended — free use with attribution.

## Citation

```bibtex
@misc{mihenk2026,
  title  = {MIHENK: Multilingual Intelligence, High-level Evaluation and Neural Knowledge Benchmark},
  year   = {2026},
  note   = {v1.0, Phase 1 Pilot Set},
  url    = {https://github.com/gorkemergune/mihenk-benchmark}
}
```
