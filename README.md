# 🐢 DeepTurtle

<div align="center">

[![Hugging Face](https://img.shields.io/badge/🤗%20Hugging%20Face-Dataset-yellow)](https://huggingface.co/datasets/YuiMax/DeepTurtle)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Website](https://img.shields.io/badge/Play-turtlenoir.com-blue)](https://turtlenoir.com)

**A Logic-Grounded Lateral Thinking Benchmark for LLMs**
*Evaluating Implicit Reasoning, Hallucination Detection, and Instruction Following.*

[Introduction](#-introduction) • [Dataset](#-dataset) • [Methodology](#-methodology) • [Citation](#-citation)

</div>

---

## 📖 Introduction

**DeepTurtle** is a benchmark designed to evaluate the reasoning capabilities of Large Language Models (LLMs) through **Lateral Thinking Puzzles** (also known as "Turtle Soup").

Unlike traditional QA datasets, DeepTurtle focuses on **non-monotonic reasoning** where the premise is vague, and the truth is hidden. It introduces a novel **Logic Profile** engine to strictly define the boundaries of "Truth," enabling precise detection of model hallucinations—particularly referencing failure modes observed in **DeepSeek** models.

👉 **Play the Game:** [https://turtlenoir.com](https://turtlenoir.com)

## 💾 Dataset

The full dataset is hosted on Hugging Face. It contains 61 rigorously curated "Golden Samples" with human-in-the-loop annotations.

### [Download from Hugging Face 🤗](https://huggingface.co/datasets/YuiMax/DeepTurtle)

```python
from datasets import load_dataset

# Load the dataset
dataset = load_dataset("YuiMax/DeepTurtle")

# Inspect the first case
print(dataset['train'][0]['logic_profile'])

```

## 🧠 Methodology: The Logic Profile

The core contribution of DeepTurtle is the structured **Logic Profile**, which transforms a vague story into a computable logic state machine.

### Data Structure Example

```json
{
  "title": "Live Stream Murder",
  "logic_profile": {
    "entities_preprocess": {
      "step2_identity_matrix": [
        {
          "noun": "Streamer",
          "role_feature": "Predator",
          "knowledge_feature": "Omniscient (Knows victim's location)"
        }
      ]
    },
    "logic_rules": [
      "IF user asks 'Is it a ghost?', THEN verdict is 'NO'.",
      "IF user asks 'Did he find me via GPS?', THEN verdict is 'YES' (Implicit Logic)."
    ]
  }
}

```

## 🚨 Failure Taxonomy (Case Studies)

We categorize LLM failures into two distinct types based on real-world interactions.

### Type 1: DeepSeek-Hallucination

The model fails to adhere to the `logic_profile`.

* **Fact Hallucination**: Inventing details not present in the "Truth".
* **Semantic Ambiguity**: Misinterpreting relationship queries (e.g., confusing "interpersonal relationship" with "logical relevance").
* **Sycophancy**: Incorrectly agreeing with user guesses to be helpful.

### Type 2: User_False_Report

A unique dataset feature where **the model is correct**, but the user flags it as wrong.

* *Utility:* These samples are critical for **RLHF Rejection Sampling**, training models to "stand their ground" when logically correct.

## 🛠️ Logic Evaluation (Coming Soon)

We are developing an evaluation script to automatically benchmark LLMs against the `logic_rules` defined in the dataset.

* [ ] Auto-Evaluator based on Logic Gates
* [ ] Hallucination Rate metric

## 🤝 Contribution

We welcome contributions of new puzzles! Please ensure your pull request includes:

1. **Surface**: The question.
2. **Truth**: The full story.
3. **Logic Profile**: The parsed entity matrix and rules.

## 📜 License

This project is licensed under the MIT License.

## 🖊️ Citation

If you use DeepTurtle in your research, please cite:

```bibtex
@misc{deepturtle2026,
  title={DeepTurtle: A Logic-Grounded Lateral Thinking Benchmark},
  author={DeepTurtle Team},
  year={2026},
  publisher={GitHub},
  journal={GitHub repository},
  howpublished={\url{[https://github.com/YuiMax/DeepTurtle](https://github.com/YuiMax/DeepTurtle)}}
}

```
