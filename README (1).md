# When Specialization Loses: A Decision Boundary Study of Sarvam-1 vs. Translate-Test for Hindi NLP

📄 **ICML 2026 — Global South ML Workshop** | Paper #136

> *Does a 2B Indic-specialized model beat a simple translate-test pipeline? We tested it. The answer might surprise you.*

---

## Overview

This repository contains the code, data, and results for our paper submitted to the **ICML 2026 Global South ML Workshop**.

We empirically compare two approaches for Hindi NLP on commodity hardware:

| Approach | Description |
|---|---|
| **Sarvam-1 (Native)** | 2B-parameter Indic-specialized base model, 3-shot prompted in Hindi |
| **Translate-Test** | IndicTrans2 (Hi→En) → Qwen2.5-7B-Instruct (4-bit) → IndicTrans2 (En→Hi) |

**Key finding:** The translate-test pipeline outperforms native Sarvam-1 on every non-trivial task category — including Indian-cultural questions, where specialization should help most.

| Category | Sarvam-1 | Translate-Test | Δ |
|---|---|---|---|
| Factoid | 0.867 | 0.933 | +0.066 |
| Reasoning | 0.533 | 0.800 | **+0.267** |
| Cultural (Indian) | 0.733 | 0.900 | **+0.167** |
| Cultural (Universal) | 1.000 | 1.000 | 0.000 (tie) |
| **Overall** | **0.783** | **0.908** | **+0.125** |

---

## Repository Structure

```
.
├── icml-code.ipynb          # Full experiment notebook (Kaggle-ready)
├── data/
│   └── test_set.csv         # 60-question Hindi diagnostic suite
├── paper/
│   ├── 136_WhenSpecializationLoses_AD.pdf   # Submitted paper
│   └── source/              # LaTeX source files
├── results/                 # Pre-computed outputs (scores, summaries)
└── README.md
```

---

## Reproduce in One Click

The notebook is designed to run end-to-end on a **free Kaggle session** (2× Tesla T4, ~90 minutes total).

[![Open in Kaggle](https://kaggle.com/static/images/open-in-kaggle.svg)](https://www.kaggle.com/)

### Steps

1. Upload `icml-code.ipynb` to Kaggle
2. Enable GPU accelerator (T4 × 2) in Settings
3. Add your HuggingFace token to cell 4 (`HF_TOKEN = 'your_token_here'`)
4. Run All

> **No paid APIs. No proprietary models. No expensive GPUs.** Reproducible on hardware accessible to Global South practitioners.

### Models Used

- [`sarvamai/sarvam-1`](https://huggingface.co/sarvamai/sarvam-1) — 2B base model (fp16)
- [`ai4bharat/indictrans2-indic-en-1B`](https://huggingface.co/ai4bharat/indictrans2-indic-en-1B) — Hindi → English translation
- [`Qwen/Qwen2.5-7B-Instruct`](https://huggingface.co/Qwen/Qwen2.5-7B-Instruct) — English reasoning (4-bit NF4)
- [`ai4bharat/indictrans2-en-indic-1B`](https://huggingface.co/ai4bharat/indictrans2-en-indic-1B) — English → Hindi translation

---

## The Diagnostic Suite

We constructed a **60-question Hindi test set** (Devanagari script) stratified across 4 categories (n=15 each):

- **Factoid** — Universal knowledge with short, unambiguous answers
- **Reasoning** — Multi-step arithmetic, ratios, transitive logic, temporal reasoning
- **Cultural (Indian)** — India-specific cultural, historical, and civic knowledge
- **Cultural (Universal)** — Western/globally-known cultural facts

All questions are natively written in Hindi. The full set is in [`data/test_set.csv`](data/test_set.csv).

---

## Evaluation

We use **LLM-as-judge** (Qwen2.5-7B-Instruct) with a Hindi-aware rubric:
- `1.0` — Correct (semantically equivalent, lenient on phrasing/transliteration)
- `0.5` — Partially correct
- `0.0` — Wrong, irrelevant, or unanswered

Manual validation on a 15-sample random subset showed **86.7% agreement** with the automated judge.

---

## Practitioner Decision Guide

```
What task are you solving?
│
├── Multi-step reasoning (math, logic) ──────────→ USE TRANSLATE-TEST (+26.7 pts)
├── Indian cultural knowledge ────────────────────→ USE TRANSLATE-TEST (+16.7 pts)  
├── Factoid recall ───────────────────────────────→ USE TRANSLATE-TEST (slight edge)
├── Universal cultural knowledge ─────────────────→ EITHER (tied at 100%)
│
└── Special cases where native may win:
    ├── Long-form generation (summarization, dialogue)
    ├── Real-time/latency-critical apps
    └── Lower-resource Indic languages (Tamil, Bengali, etc.)
```

---

## Citation

```bibtex
@inproceedings{whenspecialization2026,
  title     = {When Specialization Loses: A Decision Boundary Study of Sarvam-1 vs. Translate-Test for Hindi NLP},
  author    = {Anonymous},
  booktitle = {ICML 2026 Workshop on Machine Learning for the Global South},
  year      = {2026}
}
```

---

## License

Code: MIT License. See [LICENSE](LICENSE).

The diagnostic test set is released for research use. The paper PDF is © the authors; distribution follows ICML workshop policies.
