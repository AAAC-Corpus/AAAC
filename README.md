# AAAC: An Algerian Arabic Adversarial Corpus for Dialect-Aware Hate Speech and Prompt Injection Detection in LLM Safety Pipelines

<p align="center">

[![Paper](https://img.shields.io/badge/Paper-Under%20Review-blue)](paper/AAAC.pdf)
[![Dataset](https://img.shields.io/badge/Samples-3090-success)]()
[![Language](https://img.shields.io/badge/Language-Algerian%20Arabic-green)]()
[![License](https://img.shields.io/badge/License-MIT-orange)]()

</p>

---

## Overview

**AAAC (Algerian Arabic Adversarial Corpus)** is a benchmark dataset for evaluating **LLM safety**, **content moderation**, and **intent classification** in **Algerian Arabic (Darja)**.

To our knowledge, no other public benchmark simultaneously (i) targets Algerian Arabic specifically, (ii) covers the three scripts commonly used online for the same intent categories, and (iii) uses a safety-oriented taxonomy that includes prompt injection and tool misuse. AAAC covers:

- Algerian Derja (Arabic script)
- Arabizi (Latin-digit transliteration, e.g. 3 = ع, 7 = ح)
- Franco-Arabic (French–Arabic code-switching)

The corpus contains **3,090 samples** across five safety-relevant intent categories, with a documented provenance and a fixed stratified train/validation/test split.

---

# Features

- Safety benchmark dedicated to Algerian Arabic
- Three writing systems
- Five intent categories
- Binary adversarial detection labels
- Fixed, stratified train/validation/test splits (seed 42)
- Manually reviewed and corrected (single annotator, see [Limitations](#limitations))
- Documented post-merge quality audit
- Datasheet included (`dataset/datasheet.json`)
- Baselines: fine-tuned transformers, classical ML, and two LLMs (zero-shot and few-shot)

---

# Dataset Statistics

| Property | Value |
|------------|-------|
| Total samples | **3,090** |
| Language | Algerian Arabic |
| Writing systems | 3 |
| Intent classes | 5 |
| Binary labels | 2 |
| Train | 2,472 |
| Validation | 309 |
| Test | 309 |
| Duplicate IDs / duplicate texts / missing values | 0 / 0 / 0 |

### Category distribution

| Category | Share of full dataset |
|----------|-----------------------|
| safe | 50.0% (1,545) |
| prompt_injection | 16.1% (~497) |
| hate_speech | 12.0% (372) |
| sensitive_request | 11.9% (368) |
| tool_misuse | 10.0% (308) |

The split is stratified on the joint key `category × language_variant`, so every (intent, script) combination appears in each split. Per-split category proportions are within ±0.3 percentage points of the full dataset, and there is zero text overlap across splits (exact or normalized).

---

# Intent Categories

| Label | Description |
|--------|-------------|
| safe | Benign user requests |
| hate_speech | Hate or offensive language |
| prompt_injection | Attempts to manipulate LLM instructions |
| sensitive_request | Requests involving sensitive personal information |
| tool_misuse | Unsafe or unauthorized tool-use requests |

All categories are considered adversarial except `safe`.

---

# Writing Systems

| Variant (`language_variant`) | Description |
|----------|-------------|
| `algerian_derja` | Arabic script |
| `arabizi` | Latin alphabet with digits (3, 7, 9, ...) |
| `franco_arabic` | Arabic-script text densely code-switched with French |

---

# Data Sources

| Source | Generation |
|--------|------------|
| Manual | `safe`, `hate_speech`, `sensitive_request`; authored by the authors |
| Prompt injection | Translated/adapted from the [zachz/prompt-injection-benchmark](https://huggingface.co/datasets/zachz/prompt-injection-benchmark) (MIT) |
| Tool misuse | Generated with the Claude API; manually reviewed |
| Groq | Generated with Llama-3.3-70B via the Groq API; manually reviewed |

---

# Quality Assurance

The merged dataset was manually checked and corrected by a single annotator. The audit found and fixed the following issues before release:

- **Franco-Arabic outputs were plain French** in the generated `tool_misuse` subset. They were augmented with real French–Arabic code-switching, and every `franco_arabic` row was verified to contain Arabic-script text.
- **Moroccan Darija features in Arabic-script samples** (e.g. ديال / ديالو, مزيان) in the generated `tool_misuse` subset. Affected samples were rewritten in Algerian Arabic, and no Moroccan-exclusive markers remain in the Arabic-script rows.
- **Label mismatch in the Groq-generated subset**: several `franco_arabic` samples that are substantively `sensitive_request` had been mislabeled as `hate_speech`; these were corrected.

---

# Dataset Format

Each record contains the following fields.

| Column | Description |
|---------|-------------|
| id | Sample identifier |
| text | User prompt |
| language_variant | Script variant |
| category | Intent category |
| tool | Tool the prompt refers to (e.g. terminal, web search, calculator); `not_applicable` if none |
| is_adversarial | Binary label (0 = safe, 1 = adversarial) |

Example:

```csv
id,text,language_variant,category,tool,is_adversarial
gr_0001,....,algerian_derja,tool_misuse,terminal,1
```

---

# Repository Structure

```
AAAC/
│
├── README.md
├── LICENSE
├── CITATION.cff
│
├── dataset/
│   ├── AAAC_dataset
│   ├── evaluation_data/
│   └── datasheet.json
│
├── notebooks/
│   ├── 01_AAAC_EDA.ipynb
│   ├── 02_data_split.ipynb
│   ├── 03_data_validation.ipynb
│   ├── 04a_intent-classification.ipynb
│   ├── 04b_adversarial-detection.ipynb
│   ├── 05a-llm-zero-shot.ipynb
│   └── 05b-llm-few-shot
│
├── results/
│   ├── outputs_main_experiments/
│   ├── outputs_EDA/
│   └── outputs_llm_baseline/
│
└── paper/
    ├── AAAC.pdf
    └── AAAC_final_paper
```

---

# Benchmark Tasks

**Task 1 — Five-way intent classification:** `safe`, `hate_speech`, `prompt_injection`, `sensitive_request`, `tool_misuse`

**Task 2 — Binary adversarial detection:** `safe` vs. `adversarial`

---

# Baseline Models

### Fine-tuned transformer encoders (5 seeds each: 42–46)

- DziriBERT (`alger-ia/dziribert`), Algerian-dialect pretrained
- AraBERT (`aubmindlab/bert-base-arabertv02`)
- MARBERT (`UBCNLP/MARBERT`)

Setup: learning rate 3e-5, batch size 16, weight decay 0.01, 10% warm-up, mixed precision, at most 8 epochs with early stopping (patience 2) on validation macro F1, weighted cross-entropy, max length 128. Trained on a single Kaggle NVIDIA Tesla T4.

### Classical machine learning

- TF-IDF (character n-grams) + Linear SVM
- TF-IDF (word uni/bigrams) + Random Forest

### Large language models (no fine-tuning, via the Groq API)

- ALLaM-2-7B (`allam-2-7b`)
- GPT-OSS-120B (`openai/gpt-oss-120b`, low reasoning effort)
- Zero-shot and few-shot (2 examples per class for five-way, 3 per class for binary; one fixed draw from the training split)

---

# Main Results

Macro F1 on the 309-sample test set, with 95% bootstrap confidence intervals (seed-42 predictions).

## Five-Way Intent Classification

| Model | Accuracy | Macro F1 [95% CI] |
|---------|---------|-----------|
| DziriBERT | 0.958 | **0.939** [0.901, 0.969] |
| TF-IDF + SVM | 0.935 | 0.914 [0.873, 0.950] |
| MARBERT | 0.919 | 0.895 [0.855, 0.931] |
| AraBERT | 0.900 | 0.887 [0.844, 0.921] |
| TF-IDF + Random Forest | 0.838 | 0.786 [0.730, 0.834] |

DziriBERT 5-seed mean: 0.931 ± 0.007.

## Binary Adversarial Detection

| Model | Accuracy | Macro F1 [95% CI] |
|---------|---------|-----------|
| DziriBERT | 0.974 | **0.974** [0.955, 0.990] |
| TF-IDF + SVM | 0.964 | 0.964 [0.942, 0.984] |
| MARBERT | 0.935 | 0.935 [0.906, 0.961] |
| AraBERT | 0.929 | 0.929 [0.900, 0.955] |
| TF-IDF + Random Forest | 0.916 | 0.916 [0.883, 0.948] |

## LLM Baselines vs. Best Fine-Tuned Models

LLM rows use strict scoring (unusable answers, such as refusals, count as errors).

| Model | Intent Macro F1 [95% CI] | Binary Macro F1 [95% CI] |
|-------|------------------|------------------|
| DziriBERT | 0.939 [0.901, 0.969] | 0.974 [0.955, 0.990] |
| TF-IDF + SVM | 0.914 [0.873, 0.950] | 0.964 [0.942, 0.984] |
| GPT-OSS-120B few-shot | 0.851 [0.804, 0.896] | 0.929 [0.899, 0.955] |
| GPT-OSS-120B zero-shot | 0.838 [0.785, 0.887] | 0.902 [0.870, 0.932] |
| ALLaM-2-7B few-shot | 0.684 [0.629, 0.734] | 0.903 [0.870, 0.935] |
| ALLaM-2-7B zero-shot | 0.710 [0.653, 0.765] | 0.851 [0.809, 0.890] |

## Per-Script Results (five-way task, macro F1)

Test subset sizes: Derja n = 102, Arabizi n = 126, Franco-Arabic n = 81.

| Model | Derja | Arabizi | Franco-Arabic |
|-------|-------|---------|---------------|
| MARBERT | 0.986 | 0.756 | 0.925 |
| AraBERT | 0.949 | 0.783 | 0.891 |
| DziriBERT | 0.977 | 0.894 | 0.942 |
| SVM | 0.914 | 0.889 | 0.929 |
| Random Forest | 0.781 | 0.693 | 0.704 |

## Efficiency

| Model | Train time (s) | Size (MB) |
|-------|---------------|-----------|
| MARBERT | 129.4 | 651.4 |
| AraBERT | 108.6 | 540.8 |
| DziriBERT | 90.3 | 497.8 |
| SVM | 0.16 | 0.87 |
| Random Forest | 2.11 | 43.9 |

(Five-way task, seed-42 wall-clock including model loading.)

---

# Key Findings

- **Best model:** DziriBERT (dialect-specific pretraining) obtains the highest observed macro F1 on both tasks (0.939 five-way, 0.974 binary). It is significantly better than AraBERT and Random Forest after Holm-corrected McNemar tests, but **not** significantly better than TF-IDF + SVM or MARBERT at this test-set size.
- **Strong lexical baseline:** TF-IDF + SVM is within 1.0–2.4 macro-F1 points of the best transformer and trains in under 0.2 s.
- **Fine-tuned encoders beat the tested LLMs** in both zero-shot and few-shot prompting. GPT-OSS-120B trails DziriBERT by 8.8 (five-way) and 4.6 (binary) points with few-shot examples; ALLaM-2-7B trails by 25.4 and 7.1 points. Few-shot results use a single example draw.
- **Binary is easier than five-way:** the weakest classes are `tool_misuse` and `sensitive_request`, which are most often confused with each other and with `prompt_injection`.
- **Arabizi is the hardest script on the five-way task:** every classifier scores lower on Arabizi than on Arabic-script Derja (by 2.5 to 23.0 macro-F1 points, point estimates), a potential blind spot for moderation systems. The SVM shows the smallest gap.

---

# Reproducibility

The repository includes

- The complete dataset and the fixed train/validation/test splits
- Data validation notebooks
- Experimental notebooks and evaluation outputs
- Figures and results reported in the paper

Random seed for the split and the example draw: `42`. Transformer experiments use seeds `42–46`.

---

# Installation

Clone the repository

```bash
git clone https://github.com/AAAC-Corpus/AAAC.git

cd AAAC
```

---

# Running the Experiments

| Notebook | Description |
|------------|------------|
| 01_AAAC_EDA.ipynb | Exploratory data analysis |
| 02_data_split.ipynb | Train/validation/test split |
| 03_data_validation.ipynb | Dataset validation |
| 04_main_experiments.ipynb | Transformer & classical ML experiments |
| 05_LLM_Baseline.ipynb | Zero-shot & few-shot LLM evaluation |

---

# Ethical Considerations

AAAC contains adversarial content in Algerian Arabic, both manually authored and synthetically generated, including hate speech, prompt injection attempts, sensitive personal-data requests, and unsafe tool-use instructions. No synthetic examples were generated by explicitly instructing a model to produce real-world targeted harassment against an identifiable individual.

The dataset is released **exclusively for research purposes**, including

- NLP research
- LLM safety
- Content moderation
- Benchmarking
- Robustness evaluation

It must **not** be used to facilitate malicious activities or to generate harmful content, including synthetic targeted harassment. A datasheet (in the style of Bender and Friedman, 2018) with intended and out-of-scope uses is provided in `dataset/datasheet.json`.

---

# Limitations

- **Single annotator:** labels were validated by one annotator, so inter-annotator agreement could not be measured; boundary cases such as `safe` vs. `sensitive_request` may be ambiguous.
- **Small test set:** the 309-sample test set has per-class support of 31–155; 95% bootstrap CIs are 6.8–10.4 points wide (five-way) and 3.6–6.5 points (binary). No CIs were computed for per-class or per-script scores.
- **LLM baselines:** two LLMs, zero-/few-shot as label-outputting classifiers, with a single few-shot example draw. Conversational refusal/compliance behavior is not evaluated.
- **LLM-generated data:** some adversarial examples were generated with Claude and Llama-3.3-70B and manually reviewed, which may limit attack diversity and introduce model-specific patterns.
- **Dialect coverage:** three written-script varieties are covered, but not regional dialectal variation, Amazigh varieties, register, or speaker demographics.

---

# Future Work

- Multi-annotator re-annotation of a subsample with inter-annotator agreement
- Live evaluation of refusal/compliance behavior of deployed conversational LLMs
- Arabizi-centric pretraining data or script normalization to close the Arabizi gap
- More LLMs, prompt variants, and multiple few-shot example draws
- Independently authored, human-generated adversarial examples, and larger `tool_misuse` and `sensitive_request` categories

---

# Citation

If you use AAAC in your work, please cite:

```bibtex
@article{dalache2026aaac,
  title={AAAC: An Algerian Arabic Adversarial Corpus for Dialect-Aware Hate Speech and Prompt Injection Detection in LLM Safety Pipelines},
  author={Dalache, Aya and Chebli, Nessrine and Harrag, Fouzi and Derdour, Makhlouf and Deriche, Mohamed and Shaalan, Khaled},
  year={2026},
  note={Under Review}
}
```

---

# License

The original AAAC dataset and code are released under the **MIT License**.

The `prompt_injection` subset contains samples translated and adapted from the [`prompt-injection-benchmark`](https://huggingface.co/datasets/zachz/prompt-injection-benchmark) dataset, which is also distributed under the MIT License. The original authors are acknowledged in accordance with the license requirements.

---

# Authors

**Aya Dalache**  
Department of Computer Science, University Ferhat Abbas Sétif 1, Algeria

**Nessrine Chebli**  
Department of Computer Science, University Ferhat Abbas Sétif 1, Algeria

**Fouzi Harrag**  
Department of Computer Science, University Ferhat Abbas Sétif 1, Algeria

**Makhlouf Derdour**  
Computer Sciences Department, University of Oum El Bouaghi, Algeria

**Mohamed Deriche**  
Artificial Intelligence Research Centre, Ajman University, UAE

**Khaled Shaalan**  
Department of Informatics, The British University in Dubai, UAE

---

# Contact

For questions, suggestions, or collaborations, please contact:

**Aya Dalache** — 📧 ayadalache9@gmail.com

---

# Acknowledgements

This work builds upon previous open-source resources, including:

- DziriBERT, AraBERT, MARBERT
- prompt-injection-benchmark (zachz)
- Anthropic Claude (tool-misuse data generation)
- Groq-hosted models: Llama-3.3-70B (data generation), ALLaM-2-7B and GPT-OSS-120B (LLM baselines)

We gratefully acknowledge the authors of these resources for enabling further research in Arabic NLP and LLM safety.
