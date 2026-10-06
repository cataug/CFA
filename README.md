# CFA — Causal Counterfactual Augmentation for Scientific QA

<p align="center">
  <b>From scientific text to structured causal models and validated counterfactual reasoning.</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-blue?logo=python&logoColor=white">
  <img src="https://img.shields.io/badge/Jupyter-Notebooks-orange?logo=jupyter">
  <img src="https://img.shields.io/badge/LLM-Causal%20Reasoning-purple">
  <img src="https://img.shields.io/badge/Task-Counterfactual%20QA-green">
  <img src="https://img.shields.io/badge/Validation-Human%20%2B%20LLM-red">
</p>

---

## Overview

**CFA** is a research framework for transforming scientific text into **structured causal representations and counterfactual question–answer pairs**.

Instead of generating synthetic questions through unconstrained paraphrasing, CFA explicitly introduces a causal intermediate representation. Scientific abstracts are decomposed into entities, events, and relations, converted into a lightweight **Structural Causal Model (SCM)**, and subsequently used to generate controlled counterfactual interventions.

The resulting QA pairs therefore capture not only *what is stated in a scientific text*, but also *how an answer should change when an underlying causal condition is modified*.

The framework combines:

- scientific information extraction;
- causal structure induction;
- factual QA generation;
- intervention-based counterfactual generation;
- consistency filtering;
- LLM-based evaluation;
- independent human expert validation;
- inter-rater agreement analysis.

> **CFA turns scientific text into an explicit reasoning space: extract → structure → intervene → reason → validate.**

---

## Pipeline

```mermaid
flowchart LR
    A[Scientific Abstract] --> B[Entity & Event Extraction]
    B --> C[Causal Relations]
    C --> D[Structural Causal Model]

    A --> E[Factual QA Generation]

    D --> F[Counterfactual Intervention]
    E --> F

    F --> G[Counterfactual QA]
    G --> H[Consistency Filtering]

    H --> I[LLM Validation]
    H --> J[Expert Validation]

    I --> K[Evaluation Metrics]
    J --> K
```

### 1. Scientific information extraction

For each abstract, CFA identifies structured information including:

- **entities** — methods, datasets, models, metrics, domains;
- **events** — training, evaluation, interventions, comparisons;
- **relations** — explicit causal or dependency relations.

### 2. Structural causal modeling

The extracted information is converted into an SCM containing:

- causal variables;
- directed causal edges;
- intervenable variables.

This representation provides an explicit constraint on subsequent counterfactual generation.

### 3. Factual QA generation

CFA first generates questions whose answers are directly supported by the source abstract.

These factual pairs form the reference state from which counterfactual examples are constructed.

### 4. Counterfactual intervention

For each candidate QA pair, CFA selects an intervenable variable and applies a plausible intervention.

Conceptually,

\[
X=x
\quad\longrightarrow\quad
do(X=x')
\]

induces a transition from the factual answer

\[
Y
\]

to the corresponding counterfactual answer

\[
Y_{do(X=x')}.
\]

The objective is not merely to rewrite the question, but to produce an answer that is **consistent with the modified causal state**.

### 5. Consistency filtering

Generated counterfactual examples are filtered to remove cases that are:

- logically inconsistent;
- unsupported or unanswerable;
- incompatible with the specified intervention.

---

## Validation

CFA includes a dedicated validation pipeline combining **automated LLM assessment and human expert annotation**.

Validation criteria cover multiple stages of the framework:

| Component | Evaluated aspect |
|---|---|
| Entities | extraction quality |
| Events | event identification |
| Relations | causal/dependency consistency |
| Original QA | factual faithfulness |
| Counterfactual QA | intervention consistency |
| Global | end-to-end coherence |

Human annotations are collected independently and analyzed using multiple agreement measures, including:

- **Cohen's κ**
- **Fleiss' κ**
- **Krippendorff's α**
- pairwise percentage agreement

The repository also contains utilities for comparing human judgments with automated LLM validation.

---

## Repository Structure

```text
CFA/
│
├── Generating.ipynb
│   └── Main CFA generation pipeline
│
├── validation.ipynb
│   └── Evaluation, validation, agreement analysis,
│       and visualization
│
├── для аннотаторов.ipynb
│   └── Utilities for expert annotation
│
├── annotated_docs/
│   └── Annotated evaluation material
│
├── figures_metrics_pdf/
│   └── Evaluation and validation figures
│
├── all_metrics_long.csv
│   └── Long-form evaluation results
│
├── all_metrics_summary.xlsx
│   └── Aggregated validation metrics
│
├── interrater_agreement_results.xlsx
│   └── Human inter-rater agreement results
│
├── вопросы (Gusarova).xlsx
├── вопросы (Kostromin).xlsx
├── вопросы (Martysyuk).xlsx
│   └── Independent expert annotations
│
└── pipe2.pdf
    └── CFA pipeline overview
```

---

## Output

For each processed scientific document, CFA produces structured artifacts such as:

```text
paper/
├── entities_events.json
├── scm.json
├── qa_original.json
├── qa_counterfactual.json
└── qa_filtered.json
```

A counterfactual sample contains both the factual and intervened states:

```json
{
  "original_question": "...",
  "counterfactual_question": "...",
  "original_answer": "...",
  "counterfactual_answer": "...",
  "intervention": "..."
}
```

This makes each generated example explicitly traceable to the intervention that produced it.

---

## Getting Started

Clone the repository:

```bash
git clone https://github.com/cataug/CFA.git
cd CFA
```

Install the main dependencies:

```bash
pip install pandas numpy scikit-learn matplotlib openpyxl openai
```

Set your OpenAI API key as an environment variable:

```bash
export OPENAI_API_KEY="your-api-key"
```

and initialize the client in Python with:

```python
import os
from openai import OpenAI

client = OpenAI(api_key=os.environ["OPENAI_API_KEY"])
```

Then open:

```text
Generating.ipynb
```

to run the generation pipeline and:

```text
validation.ipynb
```

to reproduce validation and agreement analysis.

---

## Research Use

CFA is intended for research on:

- **counterfactual reasoning**
- **causal NLP**
- **scientific NLP**
- **LLM evaluation**
- **question answering**
- **synthetic data generation**
- **causal data augmentation**
- **human–LLM agreement**
- **structured reasoning with LLMs**

The framework is particularly suited to experiments where synthetic QA data must preserve an explicit connection between an intervention and its downstream consequences.

---

## Design Principle

Conventional synthetic QA generation often follows:

```text
Text → LLM → Synthetic QA
```

CFA instead introduces an explicit causal reasoning layer:

```text
                         ┌──────────────────────┐
Scientific Text ───────► │ Causal Representation│
                         └──────────┬───────────┘
                                    │
                              Intervention
                                    │
                                    ▼
                         Counterfactual State
                                    │
                                    ▼
                           Counterfactual QA
```

The central idea is simple:

> **Counterfactual data should be generated by changing the modeled world, not merely by changing the wording.**

---

## Reproducibility

The repository provides:

- generation notebooks;
- validation notebooks;
- generated evaluation tables;
- expert annotation files;
- inter-rater agreement statistics;
- publication-ready evaluation figures.

Together, these artifacts support inspection of both the **generation process** and the **quality-control procedure**.

---

## Citation

If you use CFA in academic work, please cite the associated publication.

```bibtex
@article{cfa2026,
  title   = {CFA: Causal Counterfactual Augmentation for Scientific Question Answering},
  author  = {...},
  journal = {...},
  year    = {2026}
}
```

*Citation details will be updated upon publication.*

---

## Keywords

`counterfactual-reasoning` · `causal-inference` · `structural-causal-models` · `scientific-nlp` · `question-answering` · `large-language-models` · `synthetic-data` · `human-evaluation`
