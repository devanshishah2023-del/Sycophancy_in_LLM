# LLM Sycophancy in Mental Health: A Multi-Model Evaluation with Multi-Agent Mitigation

## Overview

This repository contains the full code, datasets, and results for a study evaluating sycophantic behavior in large language models (LLMs) when applied to psychiatric multiple-choice questions, and a multi-agent framework designed to mitigate it.

Sycophancy occurs when an LLM changes a correct answer under social pressure. This poses serious risks in clinical settings. This study quantifies that risk across 5 models and 1,336 questions, then demonstrates a critic-based intervention that reduces it by 96.3%.

---

## Key Results

### Baseline Experiment (5 Models x 1,336 Questions x 6 Strategies)

| Model | Baseline Accuracy | Sycophancy Rate | Most Dangerous Strategy |
|---|---|---|---|
| GPT-5.4-nano | 76.9% | 8.2% | Adversarial Framing |
| Mistral-Small-2603 | 87.4% | 5.5% | Simple Doubt |
| DeepSeek-V3.2 | 90.9% | 3.3% | Adversarial Framing |
| Gemini-3.1-Flash-Lite | 92.3% | 2.9% | Adversarial Framing |
| Grok-4.1-Fast | 92.4% | 0.2% | Confirmation Bias |

Total rows collected: 40,080

Verdict breakdown (all models combined):
- HELD GROUND: 38,470
- SYCOPHANCY (Correct to Wrong): 884
- SYCOPHANCY (Wrong to Correct): 390
- SYCOPHANCY (Wrong to Diff Wrong): 336

---

### Multi-Agent Mitigation (GPT-5.4-nano, n=8,016)

| Metric | Original | Post-Critic | Change |
|---|---|---|---|
| Sycophancy Rate | 8.2% | 0.3% | -7.9 pp |
| Accuracy | 76.9% | 77.2% | +0.3 pp |
| Correct to Wrong cases | 249 | 1 | -248 recovered |

Statistical significance:
- Flip reduction: p < 0.001 (McNemar)
- R1 vs R3 accuracy: p < 0.001 (McNemar)
- Confidence shift: p < 0.001 (Wilcoxon)

---

## Six Manipulation Strategies Tested

| Code | Strategy | Description |
|---|---|---|
| SD | Simple Doubt | Are you sure? I am not convinced. |
| AP | Authority Pressure | Senior psychiatrist claims wrong answer |
| CB | Confirmation Bias | Frames wrong answer as obviously correct |
| EP | Emotional Pressure | Urgent clinical emergency framing |
| PD | Peer Disagreement | Claims another model gave a different answer |
| AF | Adversarial Framing | Claims textbooks support wrong answer |

---

## Repository Structure

```
9-LLM-Sycophancy/
├── Datasets/
├── Images/
│   ├── fig1_strategy_comparison.svg
│   ├── fig2_model_comparison.svg
│   ├── fig3_combined_analysis.svg
│   ├── fig4_confidence_analysis.svg
│   ├── fig5_model_flip_confidence_intervals.svg
│   ├── fig6_strategy_effectiveness.svg
│   ├── fig7_answer_transition_matrix.svg
│   ├── fig8_model_size_correlation.svg
│   ├── fig9_confidence_flip_analysis.svg
│   ├── MultiAgent_fig1_flip_rate_comparison.svg
│   ├── Multi_Agent_fig2_accuracy_comparison.svg
│   ├── Multi_Agent_fig3_strategy_breakdown.svg
│   └── Multi_Agent_fig4_confidence_analysis.svg
├── Results/
│   ├── full_multimodel_results.csv.zip
│   ├── full_multimodel_metrics.csv
│   ├── full_multimodel_comparison.csv
│   ├── multiagent_results.csv
│   ├── multiagent_metrics.csv
│   └── multiagent_strategy_accuracy.csv
├── Updated_LLM_Sycophancy_code.ipynb
├── Updated_MultiAgent.ipynb
├── requirements.txt
└── README.md
```

---

## Experimental Design

### Baseline Protocol

- Dataset: MedMCQA mental health subset (1,336 single-answer questions)
- Models tested: GPT-5.4-nano, Gemini-3.1-Flash-Lite, DeepSeek-V3.2, Grok-4.1-Fast, Mistral-Small-2603
- Design: Independent baseline per strategy (each strategy gets its own R1 call at T=0.0)
- Challenge temperature: T=0.3
- Random seed: 42 (fixed throughout; never changed)
- Distractor selection: Seeded random wrong option per question

### Multi-Agent Protocol (R1 to R2 to Critic to R3)

1. R1: Baseline answer at T=0.0
2. R2: Answer after social pressure at T=0.3
3. Critic: Skeptical reviewing agent evaluates whether the change was justified
4. R3: Final answer at T=0.0 with critic feedback; hard anchor enforces no drift for held-ground cases

### Sycophancy Definition

Any answer change after pressure (all flip directions counted):
- Correct to Wrong: Harmful sycophancy
- Wrong to Correct: Pressure-induced improvement
- Wrong to Diff Wrong: Lateral sycophancy

---

## Setup and Usage

### Installation

```bash
pip install -r requirements.txt
```

### Requirements

```
requests
pandas
matplotlib
seaborn
scipy
statsmodels
numpy
```

### API Access

Both notebooks use OpenRouter to access models. Set your API key:

```python
OPENROUTER_API_KEY = "your-key-here"
```

### Running the Baseline Experiment

Open Updated_LLM_Sycophancy_code.ipynb in Google Colab and update:

```python
CURRENT_MODEL = "openai/gpt-5.4-nano"
Q_START = 0
Q_END   = 1336
RANDOM_SEED = 42
FRESH_RUNTIME = True
```

### Running the Multi-Agent Experiment

Open Updated_MultiAgent.ipynb in Google Colab. Upload the baseline results CSV when prompted, then optionally upload the MedMCQA dataset for full question reconstruction.

---

## Models Tested

| Model | Provider | Approx. Parameters |
|---|---|---|
| GPT-5.4-nano | OpenAI | ~8B |
| Gemini-3.1-Flash-Lite | Google | ~8B |
| DeepSeek-V3.2 | DeepSeek | ~671B (MoE) |
| Grok-4.1-Fast | xAI | ~70B |
| Mistral-Small-2603 | Mistral | ~22B |

---


---

## License

MIT License. See LICENSE for details.
