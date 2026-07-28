# MIHENK Leaderboard

Model results on the **public sample** (development split) — **80 items** (40 Turkish + 40 English), evaluated under standardized 0-shot conditions and graded with the reference scorer (`scoring/score.py`, exact-letter for MC, normalized canonical/alias + numeric tolerance for short answer).

_Last updated: 2026-07-28._

| # | Model | TR (/40) | EN (/40) | **Overall (/80)** | **Accuracy** |
|---|-------|:---:|:---:|:---:|:---:|
| 🥇 | **Gemini Pro 3.1** | 40 | 40 | **80** | **100.0%** |
| 🥈 | **Gemini Flash 3.6** | 40 | 39 | **79** | **98.8%** |
| 🥉 | **GPT-5.5** | 38\* | 39 | **77** | **96.2%** |

## Breakdown

| Model | MC (/40) | Short answer (/40) | TR acc. | EN acc. |
|---|:---:|:---:|:---:|:---:|
| Gemini Pro 3.1 | 40 | 40 | 100.0% | 100.0% |
| Gemini Flash 3.6 | 40 | 39 | 100.0% | 97.5% |
| GPT-5.5 | 39 | 38 | 95.0% | 97.5% |

## Notes

- **Difficulty coverage of this split.** By the difficulty-balanced split design, the public sample covers **TR = L1–L2** and **EN = L3–L4** (one difficulty tier per language×format). It is therefore a partial slice of the full L1–L4 range, not a complete difficulty profile per language. For a harder evaluation, export the full set: `python scripts/export_quiz.py --split all --language tr`.
- **Shared error signal.** On **EN Q24** (Logic, L4 — a knights-and-knaves item, "how many are knights?"), both Gemini Flash 3.6 and GPT-5.5 answered **1**; the correct answer is **2**. Two independent models making the same multi-step reasoning slip is the kind of signal MIHENK is designed to surface.
- **\* GPT-5.5 (TR).** Two items (Q32 "480" and Q33 "B") were **unanswered** in the submitted transcript due to a formatting truncation (two answers were merged onto one line), so they scored 0. This reflects the submission, not necessarily the model's capability.

## How results are produced

1. Export a quiz: `python scripts/export_quiz.py --split public --language {tr,en} --output quizzes/quiz_public_{tr,en}.md`.
2. Paste the questions (everything above the answer key) into the model; collect its ordered replies under `results/<model>-<lang>.md`.
3. Grade against the answer key with the reference scorer.

For fully automated evaluation via the Claude API, use `python scripts/evaluate.py --split public --model <id>` (see the main README).
