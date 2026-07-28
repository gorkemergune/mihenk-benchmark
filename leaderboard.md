# MIHENK Leaderboard

Model results, graded with the reference scorer (`scoring/score.py`: exact-letter for MC, normalized canonical/alias + numeric tolerance for short answer). Standardized 0-shot conditions.

_Last updated: 2026-07-28._

---

## Full set (800 items, L1–L4)

Evaluated with `scripts/evaluate.py` over the complete data set (all splits, all difficulties).

| # | Model | Backend | Overall | TR | EN | MC | Short ans. | L1 / L2 / L3 / L4 | LCI |
|---|-------|---------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 🥇 | **gemma4:12b** | Ollama (local) | **97.6%** | 98.0% | 97.2% | 99.2% | 96.0% | 97.1 / 99.2 / 97.5 / 96.2 | 2.2 |

_LCI = language-consistency index (mean absolute TR/EN accuracy gap across disciplines; lower = more consistent)._

> **Observation.** Even a 12B local model scores ~98% across the full L1–L4 range (L4 = 96.2%). This confirms that the v1.0 pilot — authored by an LLM — is comparatively easy for modern LLMs and does not yet separate strong models. A genuinely hard tier (v1.1: multi-step, multi-domain, adversarial L4+) is the planned next step to restore discrimination.

---

## Public sample (80 items) — manual submissions

Earlier results collected by pasting the public quiz (`quizzes/`) into chat models (40 TR + 40 EN). By the balanced-split design this slice covers TR = L1–L2 and EN = L3–L4, so it is easier than the full set above.

| # | Model | TR (/40) | EN (/40) | Overall (/80) | Accuracy |
|---|-------|:---:|:---:|:---:|:---:|
| 🥇 | Gemini Pro 3.1 | 40 | 40 | 80 | 100.0% |
| 🥈 | Gemini Flash 3.6 | 40 | 39 | 79 | 98.8% |
| 🥉 | GPT-5.5 | 38\* | 39 | 77 | 96.2% |

\* GPT-5.5 (TR): two items were unanswered in the submitted transcript (formatting truncation), scored 0.

---

## Notes

- **Full set vs public sample are not directly comparable** — different item counts and difficulty coverage. Compare within a table, not across.
- **Reproduce (local, free):** `python scripts/evaluate.py --split all --backend openai --base-url http://localhost:11434/v1 --model <ollama-model> --max-tokens 1024 --output results/<model>.json`
- **Reproduce (cloud):** see `docs/MODEL-TESTI-YOL-HARITASI.md` (OpenRouter, one key → many models).
- **Reasoning models** need `--max-tokens` headroom (e.g. 1024–2048), otherwise the final answer can come back empty.
