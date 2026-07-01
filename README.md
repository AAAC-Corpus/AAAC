# AAAC: An Algerian Arabic Adversarial Corpus for Dialect-Aware Hate Speech and Prompt Injection Detection in LLM Safety Pipelines

<p align="center">

[![Paper](https://img.shields.io/badge/Paper-Coming%20Soon-blue)]()
[![Dataset](https://img.shields.io/badge/Samples-3090-success)]()
[![Language](https://img.shields.io/badge/Language-Algerian%20Arabic-green)]()
[![License](https://img.shields.io/badge/License-MIT-orange)]()

</p>

---

## Overview

**AAAC (Algerian Arabic Adversarial Corpus)** is the first publicly available benchmark dataset designed for evaluating **LLM safety**, **content moderation**, and **intent classification** in **Algerian Arabic (Darja)**.

Unlike existing Arabic safety datasets, AAAC simultaneously covers

- Algerian Arabic (Darja)
- Arabizi (Latin-digit transliteration)
- Franco-Arabic (French–Arabic code-switching)

making it suitable for evaluating robustness against script variation in real-world user-generated content.

The corpus contains **3,090 manually reviewed samples** covering five safety-relevant intent categories.

---

# Features

- First safety benchmark dedicated to Algerian Arabic
- Three writing systems
- Five intent categories
- Binary adversarial detection labels
- Fixed train/validation/test splits
- Human-reviewed annotations
- Ready-to-use benchmark for transformer models and LLMs

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

---

# Intent Categories

| Label | Description |
|--------|-------------|
| safe | Benign user requests |
| hate_speech | Hate or offensive language |
| prompt_injection | Attempts to manipulate LLM instructions |
| sensitive_request | Requests involving sensitive personal information |
| tool_misuse | Unsafe or unauthorized tool-use requests |

---

# Writing Systems

AAAC supports the three writing styles commonly used by Algerian speakers online.

| Variant | Description |
|----------|-------------|
| Algerian Derja | Arabic script |
| Arabizi | Latin alphabet with digits (3,7,9...) |
| Franco-Arabic | French–Arabic code-switching |

---

# Dataset Format

Each record contains six fields.

| Column | Description |
|---------|-------------|
| id | Sample identifier |
| text | User prompt |
| language_variant | Script variant |
| category | Intent category |
| tool | Tool applicability |
| is_adversarial | Binary label |

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
│   ├── 01_Before_Merge/
│   ├── 02_After_Merge/
│   ├── 03-evaluation_data/
│   └── datasheet.json
│
├── notebooks/
│   ├── 01_AAAC_EDA.ipynb
│   ├── 02_data_split.ipynb
│   ├── 03_data_validation.ipynb
│   ├── 04_main_experiments.ipynb
│   └── 05_LLM_Baseline.ipynb
│
├── results/
│   ├── outputs_main_experiments/
│   ├── outputs_EDA/
│   └── outputs_llm_baseline/
│
├── figures/
│
└── paper/
    └── AAAC.pdf
```

---

# Benchmark Tasks

## Task 1 — Five-Way Intent Classification

```
safe

hate_speech

prompt_injection

sensitive_request

tool_misuse
```

---

## Task 2 — Binary Adversarial Detection

```
Safe

vs

Adversarial
```

---

# Baseline Models

The benchmark includes the following baseline models.

### Transformer Models

- DziriBERT
- AraBERT
- MARBERT

### Classical Machine Learning

- TF-IDF + Linear SVM
- TF-IDF + Random Forest

### Large Language Model

- Llama-3.1-8B-Instant
  - Zero-shot
  - Few-shot

---

# Main Results

## Five-Way Intent Classification

| Model | Macro F1 |
|---------|-----------|
| DziriBERT | **0.937** |
| TF-IDF + SVM | 0.928 |
| AraBERT | 0.909 |
| MARBERT | 0.908 |
| Random Forest | 0.858 |

---

## Binary Adversarial Detection

| Model | Macro F1 |
|---------|-----------|
| DziriBERT | **0.987** |
| MARBERT | 0.968 |
| TF-IDF + SVM | 0.948 |
| AraBERT | 0.945 |
| Random Forest | 0.938 |

---

# Key Findings

- AAAC is the first benchmark specifically targeting Algerian Arabic LLM safety.
- Dialect-specific pretraining (DziriBERT) achieved the highest overall performance.
- Fine-tuned encoders substantially outperform prompting-only LLM baselines.
- Arabizi is consistently the most difficult writing system.
- Script variation introduces measurable robustness gaps for moderation systems.

---

# Reproducibility

The repository includes

- Complete dataset
- Fixed train/validation/test splits
- Data validation notebooks
- Experimental notebooks
- Evaluation outputs
- Figures
- Results reported in the paper

Random seed used throughout the experiments:

```
42
```

---

# Installation

Clone the repository

```bash
git clone https://github.com/AAAC-Corpus/AAAC.git

cd AAAC
```





---

# Running the Experiments

The experiments can be reproduced using the provided notebooks.

| Notebook | Description |
|------------|------------|
| 01_AAAC_EDA.ipynb | Exploratory Data Analysis |
| 02_data_split.ipynb | Train/Validation/Test split |
| 03_data_validation.ipynb | Dataset validation |
| 04_main_experiments.ipynb | Transformer & ML experiments |
| 05_LLM_Baseline.ipynb | Zero-shot & Few-shot LLM evaluation |

---

# Ethical Considerations

AAAC contains adversarial prompts, hate speech, prompt injection attempts, sensitive requests, and unsafe tool-use instructions.

The dataset is released **exclusively for research purposes**, including

- NLP research
- LLM safety
- Content moderation
- Benchmarking
- Robustness evaluation

It must **not** be used to facilitate malicious activities or generate harmful content.

---

# Limitations

- Single-expert annotation
- Limited regional dialect coverage
- One LLM baseline
- Does not evaluate conversational refusal behavior

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

This repository is released under the **MIT License**.

Some prompt injection samples are adapted from the **prompt-injection-benchmark** dataset and retain the attribution requirements of the original MIT license.

---

# Authors

**Aya Dalache**  
Department of Computer Science  
University Ferhat Abbas Sétif 1, Algeria

**Nessrine Chebli**  
University Ferhat Abbas Sétif 1, Algeria

**Fouzi Harrag**  
University Ferhat Abbas Sétif 1, Algeria

**Makhlouf Derdour**  
University of Oum El Bouaghi, Algeria

**Mohamed Deriche**  
Ajman University, UAE

**Khaled Shaalan**  
The British University in Dubai, UAE

---

# Contact

For questions, suggestions, or collaborations, please contact:

**Aya Dalache**

📧 ayadalache9@gmail.com

---

# Acknowledgements

This work builds upon previous open-source resources, including:

- DziriBERT
- AraBERT
- MARBERT
- prompt-injection-benchmark
- Anthropic Claude
- Groq Llama models

We gratefully acknowledge the authors of these resources for enabling further research in Arabic NLP and LLM safety.