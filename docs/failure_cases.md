# 🚨 Failure Taxonomy: Analysis of DeepSeek Hallucinations

## Overview

The DeepTurtle dataset is not just a collection of puzzles; it is a stress test for **Non-Monotonic Reasoning** and **Instruction Following**.

By analyzing real-world interactions (powered by DeepSeek models) against our rigorous `logic_profile`, we have identified distinct patterns of model failure. These failures provide critical data points for **Hallucination Detection** and **RLHF Rejection Sampling**.

This document details the taxonomy of errors found in the dataset.

---

## Type 1: DeepSeek-Hallucination (Model Error)

This category includes cases where the model failed to adhere to the Ground Truth or the Logic Profile rules.

### 1. Sycophancy (Over-Compliance)
**Definition:** The model incorrectly agrees with a user's guess because the user hit *some* keywords, ignoring the fact that specific details were wrong. The model prioritizes "being helpful" over "being truthful."

* **Case ID:** `81dd7d2a` - *The Bet (对赌)*
* **The Scenario:**
    * **Truth:** A submarine had a **water leak** (generic failure).
    * **User Query:** "The submarine's **internal parts broke** and it **lost directional control**."
    * **Model Response:** "Yes."
* **Analysis:** The model hallucinated confirmation of "broken parts" and "steering failure." This is dangerous because it validates a false logical branch, leading the user away from the actual truth (oxygen depletion/suicide pact).
* **Correction:** "No" or "Not exactly" (Truth only specifies leakage).

### 2. Semantic Ambiguity (Contextual Misinterpretation)
**Definition:** The model interprets a word using its generic/logical definition rather than its contextual/interpersonal definition suitable for the story.

* **Case ID:** `93e17621` - *Kidney Donation (治病)*
* **The Scenario:**
    * **Truth:** A son wants to donate a kidney to his mother, unaware she sold his other kidney years ago.
    * **User Query:** "Is our **relationship** good?" (我跟母亲的关系很好么？)
    * **Model Response:** "Irrelevant (没有关系)."
* **Analysis:** The model interpreted the Chinese word "关系" (Guanxi/Relationship) as **"Logical Relevance"** (i.e., "This clue doesn't matter"). It failed to grasp the user was asking about the **interpersonal bond**.
* **Correction:** "Yes and No" (Complex bond: Son loves mother, mother betrayed son).

### 3. Rigid Text Matching (Lack of Common Sense)
**Definition:** The model fails to link a concept in the user's query to a synonymous concept in the truth because the keywords don't match exactly.

* **Case ID:** `e605c09d` - *Street Work (街上的工作)*
* **The Scenario:**
    * **Truth:** The mother recognized her child who was **"disfigured/tortured out of human shape"** (不成人形).
    * **User Query:** "Did mom find out I was **disabled**?" (残疾)
    * **Model Response:** "No."
* **Analysis:** In the context of forced begging and torture, "disfigured/out of human shape" logically implies "disabled/injured." The model adhered too strictly to the absence of the specific word "disabled."
* **Correction:** "Yes."

### 4. Logic Inversion (Over-Caution)
**Definition:** The model hedges its answer with "Yes and No" or "Maybe" when the `logic_profile` dictates a definitive truth based on implicit logic.

* **Case ID:** `f58c3399` - *Live Stream Murder (死亡直播)*
* **The Scenario:**
    * **Truth:** A killer watches a live stream, sees the victim's room, enters, and kills him.
    * **Logic Profile:** Entity `Streamer` is defined as **Omniscient (Knows location)**.
    * **User Query:** "The streamer had my location, so he found me."
    * **Model Response:** "Yes and No."
* **Analysis:** Physically, the killer *must* have found the location to commit the murder. The model's hesitation confuses the player.
* **Correction:** "Yes."

---

## Type 2: User_False_Report (User Error)

This category represents **Golden Samples for RLHF**. In these cases, the model answered correctly according to the Logic Profile, but the human user flagged the answer as "Incorrect" (usually due to misunderstanding the game rules or expecting a different phrasing).

**Why these matter:** Training on these samples teaches the model to **"Stand Your Ground"** when it is logically correct, rather than apologizing or changing its answer to appease the user.

* **Case ID:** `757df69c` - *Faithful Dog (忠犬)*
* **The Scenario:**
    * **Truth:** Dog prevented strangers from saving the heart-attack victim.
    * **User Query:** (User guesses the entire story correctly).
    * **Model Response:** "Yes."
    * **User Feedback:** Flagged as Error.
* **Analysis:** The user likely expected a celebratory message ("Congratulations! You solved it!") rather than a dry "Yes." However, logically, the model's boolean judgment was perfect.
* **Action:** These should be treated as **Positive Samples** during training, despite the user's negative tag.

---

## Conclusion

DeepTurtle exposes that LLMs—even advanced ones like DeepSeek—struggle with:
1.  **Implicit Causality** (e.g., Live Stream -> Location).
2.  **Polysemy Resolution** (e.g., Relationship vs. Relevance).
3.  **Pressure Compliance** (agreeing with user's hallucinations).

By benchmarking against the `logic_profile`, DeepTurtle provides a metric for **reasoning robustness** beyond simple factual accuracy.

