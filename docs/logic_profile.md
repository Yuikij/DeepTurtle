# 🧠 The Logic Profile: The Reasoning Engine of DeepTurtle

## Overview

In traditional Question-Answering (QA) datasets, the relationship between the Question and the Answer is usually static and explicit. However, in **Lateral Thinking Puzzles (Turtle Soup)**, the relationship is **non-monotonic** and **hidden**.

The **Logic Profile** is the core contribution of the DeepTurtle dataset. It acts as a structured "World Model" that translates vague narratives into computable logic constraints. It forces the LLM to move beyond simple semantic matching and perform **State Tracking** and **Entity Resolution**.

## 1. The Structure

A standard `logic_profile` consists of four critical modules:

```json
"logic_profile": {
  "entities_preprocess": { ... },  // Entity Resolution & Identity Management
  "logic_rules": [ ... ],          // Explicit Instruction Following (IF-THEN)
  "tricks": [ ... ],               // Theory of Mind (Anticipating User Misconceptions)
  "milestones": { ... }            // Progress Quantification
}

```

---

## 2. Entity Preprocessing (`entities_preprocess`)

This is the most complex module, designed to handle the **"Cognitive Mask"** phenomenon common in riddles (e.g., a character called "Dad" who is actually a "Trafficker").

It breaks down reasoning into a 4-step pipeline:

### Step 1: Raw Scan (`step1_raw_scan`)

Extracts all potential entities mentioned in the surface story.

### Step 2: Identity Matrix (`step2_identity_matrix`)

Defines the ground truth properties of each entity.

* **`noun`**: The word used in the surface story (e.g., "Dad").
* **`role_feature`**: The true role (e.g., "Trafficker").
* **`knowledge_feature`**: What this entity knows (e.g., "Omniscient", "Deceived").
* **`fate_feature`**: The outcome for this entity (e.g., "Killed by mother").

### Step 3: Logic Gates (`step3_logic_gates`)

This is where the "reasoning" happens. It compares two entities to determine if they are the same person or independent, based on three gates:

* **Gate A (Physical)**: Can they physically be the same? (e.g., Alive vs Dead).
* **Gate B (Semantic)**: Do the definitions overlap?
* **Gate C (Cognitive)**: Do their intentions conflict? (e.g., Savior vs. Killer).

**Verdict Types:**

* `INDEPENDENT`: Two separate entities.
* `SINGLE_ENTITY`: The same person.
* `SINGLE_ENTITY_WITH_MASK`: The same physical person, but with a deceptive role (e.g., Dad = Trafficker). **(Hardest for LLMs)**

---

## 3. Logic Rules (`logic_rules`)

A list of strict `IF-THEN` constraints that verify the model's **Instruction Following** capability. These rules often contradict the "common sense" probability of the surface text.

**Example:**

> Surface: "I saw the ceiling."
> Rule: "IF user asks 'Did the ceiling fall?', THEN verdict is 'NO'. (It was a visual feed, not physical object)."

These rules prevent the model from hallucinating plausible but incorrect scenarios.

---

## 4. Tricks (`tricks`)

This module evaluates the model's **Theory of Mind (ToM)**. It lists common "Traps" that human players fall into. A good reasoning model should anticipate these traps.

**Example:**

* *Trap:* "Players might think 'working on the street' means a job."
* *Correction:* "Model must identify it as 'forced begging'."

---

## 5. Milestones (`milestones`)

A dictionary mapping key plot points to progress percentages. This is used to evaluate how much of the hidden truth the user (or the model simulating a user) has uncovered.

---

## 🔍 Full Example: "Street Work" (街上的工作)

Here is how the Logic Profile resolves the ambiguity of a "Dad" who is actually a criminal.

```json
{
  "entities_preprocess": {
    "step2_identity_matrix": [
      {
        "noun": "Dad",
        "role_feature": "Trafficker/Abuser",
        "knowledge_feature": "Deceiver",
        "fate_feature": "Killed by mother"
      },
      {
        "noun": "Mad Woman",
        "role_feature": "Biological Mother/Savior",
        "knowledge_feature": "Omniscient (Recognized her child)",
        "fate_feature": "Revenge Success"
      }
    ],
    "step3_logic_gates": [
      {
        "target_pair": ["Dad", "Trafficker"],
        "gate_logs": {
          "gate_C_cognitive": "ALERT: Dad (Surface: Protector) vs Trafficker (Truth: Abuser). Cognition Opposite."
        },
        "final_verdict": "SINGLE_ENTITY_WITH_MASK"
      }
    ]
  },
  "logic_rules": [
    "If user asks 'Does dad love me?', THEN verdict is 'NO' (He is using me for money).",
    "If user asks 'Is the woman mentally ill?', THEN verdict is 'NO' (She is just emotional)."
  ],
  "tricks": [
    "Player may assume 'Dad' implies biological relation. Must correct to 'Kidnapper'.",
    "Player may assume 'Mad' implies clinical insanity. Must correct to 'Grief'."
  ]
}

```

```

```
