# Sentiment Preservation in Multilingual Machine Translation: GPT vs Gemini

A dissertation research project examining whether **GPT-5.2** and **Google Gemini-2.5-Flash** preserve the sentiment of source texts when translating from Spanish, French, and Italian back into English.

---

## Project Overview

| Item | Detail |
|---|---|
| **Research focus** | Sentiment preservation across multilingual MT |
| **Models compared** | `openai/gpt-5.2` vs `google/gemini-2.5-flash` |
| **Source languages** | Spanish · French · Italian |
| **Target language** | English (back-translation) |
| **Classifier** | `cardiffnlp/twitter-xlm-roberta-base-sentiment` (3-class) |
| **Dataset** | UGSC Multilingual Sentiment Package — 200 sentences × 3 languages = **600 pairs** |
| **Sentiment classes** | Positive · Neutral · Negative |

---

## Pipeline

```
01_prepare_data.ipynb   →   data/sentences.csv          (200 rows)
02_translate.ipynb      →   outputs/translations_gpt.csv
                            outputs/translations_gemini.csv
03_classify.ipynb       →   outputs/classifications.csv
04_analysis.ipynb       →   outputs/summary_results.csv
                            outputs/figures/
```

---

## Research Questions & Results

### RQ1 — Overall sentiment preservation accuracy

> *Which model better preserves the original sentiment label across all language pairs?*

Accuracy = proportion of translated texts where predicted sentiment matches the reference English sentiment.

| Model | Correct / Total | Accuracy |
|---|---|---|
| GPT-5.2 | 477 / 600 | **79.50%** |
| Gemini-2.5-Flash | 484 / 600 | **80.67%** |

**Finding:** Gemini-2.5-Flash outperforms GPT-5.2 by **1.17 percentage points** overall.

---

### RQ2 — Accuracy by sentiment class

> *Does preservation accuracy differ across Positive, Neutral, and Negative sentiment classes?*

| Sentiment Class | Reference Count | GPT Accuracy | Gemini Accuracy | Winner |
|---|---|---|---|---|
| Positive | 233 | 86.43% | 86.43% | Tie |
| Neutral | 105 | 63.33% | 65.83% | **Gemini** |
| Negative | 195 | 80.18% | 81.98% | **Gemini** |

**Finding:** Both models struggle most with **Neutral** sentiment (lowest accuracy in both). Gemini edges out GPT on Neutral and Negative classes; Positive is a tie.

---

### RQ3 — Sentiment shift patterns

> *When sentiment is not preserved, what types of shifts occur and are they systematic?*

Total analysable pairs: **533** (after merging reference × GPT × Gemini).

#### Shift rate

| Model | Shifts | Total Pairs | Shift Rate |
|---|---|---|---|
| GPT-5.2 | 56 | 533 | **10.51%** |
| Gemini-2.5-Flash | 49 | 533 | **9.19%** |

#### Shift breakdown — GPT-5.2

| Original → Predicted | Count |
|---|---|
| Neutral → Positive | 21 |
| Negative → Neutral | 9 |
| Negative → Positive | 7 |
| Neutral → Negative | 7 |
| Positive → Negative | 5 |
| Positive → Neutral | 5 |

#### Shift breakdown — Gemini-2.5-Flash

| Original → Predicted | Count |
|---|---|
| Neutral → Positive | 20 |
| Negative → Neutral | 7 |
| Negative → Positive | 6 |
| Neutral → Negative | 6 |
| Positive → Negative | 5 |
| Positive → Neutral | 5 |

**Finding:** The dominant shift pattern for **both models** is **Neutral → Positive** (≈ 36–43% of all shifts). Neutral sentences are the hardest to preserve; they drift toward Positive most often. Gemini produces fewer total shifts (49 vs 56).

---

### RQ4 — Accuracy by source language

> *Does sentiment preservation vary across Spanish, French, and Italian?*

| Language | GPT Accuracy | Gemini Accuracy | Winner |
|---|---|---|---|
| Spanish | 83.5% | 83.5% | Tie |
| French | 92.5% | 93.0% | **Gemini** |
| Italian | 90.5% | 93.5% | **Gemini** |

**Finding:** **French and Italian** are much better preserved than **Spanish** by both models (Spanish ~10 pp lower). Gemini gains the largest advantage on Italian (+3 pp). Spanish is the only language where both models tie.

---

## Summary

| Dimension | Winner |
|---|---|
| Overall accuracy | **Gemini** (80.67% vs 79.50%) |
| Positive class | Tie |
| Neutral class | **Gemini** |
| Negative class | **Gemini** |
| Shift rate (lower = better) | **Gemini** (9.19% vs 10.51%) |
| Spanish | Tie |
| French | **Gemini** |
| Italian | **Gemini** |

**Gemini-2.5-Flash is the stronger sentiment-preserving model** across nearly all dimensions, with the most notable advantages on Neutral sentiment and Italian/French language pairs.

---

## File Structure

```
Thesis Quynh/
├── data/
│   └── sentences.csv                  # 200 source sentences (EN + ES/FR/IT)
├── outputs/
│   ├── translations_gpt.csv           # 600 GPT translations
│   ├── translations_gemini.csv        # 600 Gemini translations
│   ├── classifications.csv            # Sentiment labels for all texts
│   ├── summary_results.csv            # Aggregated accuracy table
│   └── figures/
│       ├── rq1_overall.png
│       ├── rq2_by_class.png
│       ├── rq3_confusion_matrices.png
│       ├── rq3_shift_types.png
│       └── rq4_by_language.png
├── 01_prepare_data.ipynb
├── 02_translate.ipynb
├── 03_classify.ipynb
└── 04_analysis.ipynb
```

---

## Environment

| Package | Version |
|---|---|
| Python | 3.10 (conda `py310`) |
| PyTorch | 2.6.0+cu118 |
| Transformers | latest |
| CUDA | 11.8 (GTX 1650) |
| protobuf | 3.20.3 |
| networkx | 3.2.1 |
