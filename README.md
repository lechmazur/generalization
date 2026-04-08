# LLM Thematic Generalization Benchmark V2

This benchmark tests whether large language models can infer a **specific latent theme** from a few examples, use anti-examples to reject the broader but wrong pattern, and then identify the **one true match** among close distractors.

Each item gives the model:
- **3 positive examples**
- **3 anti-examples** that fit a broader or adjacent pattern but not the exact one
- **8 candidates**, with exactly **1** hidden true match

Models score all 8 candidates. We then measure whether the correct candidate is ranked first, and how highly it tends to be ranked overall.

V2 uses **1,247 validated prompts** and adds stricter ambiguity filtering plus harder evaluation slices. The previous published release is being archived as V1.

---

## Headline Result: Cross-Family Hard Subset

![Cross-family hard subset inverse-rank leaderboard](images/04_model_bar_correct_rank_hard_subset_two_plus_wrong.png)

This is the main result.

The chart uses **inverse-rank score**, a higher-is-better transformation of the average rank assigned to the correct answer. It is easier to interpret than raw average rank and avoids the old problem where a random model would sit around the middle of the scale.

The subset contains **649** items where at least **two distinct full-coverage model buckets** miss the item. A case does not enter this subset just because two nearby variants from the same family fail; it needs broader model disagreement.

The table below includes every full-coverage model on this subset.

### Cross-family hard-subset leaderboard

| Rank | Model | Top-1 Accuracy | Inverse-Rank Score | Cases |
|---:|---|---:|---:|---:|
| 1 | Claude Opus 4.6 (high reasoning) | 89.2% | 79.2 | 649 |
| 2 | GPT-5.4 (xhigh reasoning) | 90.3% | 78.9 | 649 |
| 3 | Gemini 3.1 Pro Preview | 90.6% | 77.7 | 649 |
| 4 | GPT-5.4 (high reasoning) | 87.5% | 75.9 | 649 |
| 5 | Claude Sonnet 4.6 (high reasoning) | 87.7% | 75.0 | 649 |
| 6 | GPT-5.4 (medium reasoning) | 87.4% | 73.6 | 649 |
| 7 | Kimi K2.5 Thinking | 83.5% | 67.6 | 649 |
| 8 | Claude Opus 4.6 (no reasoning) | 83.1% | 67.1 | 649 |
| 9 | Claude Sonnet 4.6 (no reasoning) | 81.2% | 66.7 | 649 |
| 10 | Qwen3.5-397B-A17B | 81.4% | 64.0 | 649 |
| 11 | DeepSeek V3.2 | 80.3% | 63.0 | 649 |
| 12 | Grok 4.20 0309 (Reasoning) | 80.3% | 62.4 | 649 |
| 13 | Gemini 3.1 Flash-Lite Preview | 80.7% | 61.6 | 649 |
| 14 | GPT-5.4 Mini (xhigh reasoning) | 79.4% | 59.8 | 649 |
| 15 | Qwen 3.6 Plus | 80.3% | 57.5 | 649 |
| 16 | ByteDance Seed2.0 Pro | 75.0% | 54.9 | 649 |
| 17 | Gemma 4 31B Reasoning | 75.7% | 51.0 | 649 |
| 18 | GLM-5 | 74.1% | 47.0 | 649 |
| 19 | Xiaomi MiMo V2 Pro | 66.9% | 43.9 | 649 |
| 20 | Baidu Ernie 5.0 | 59.6% | 36.2 | 649 |
| 21 | GPT-5.4 (no reasoning) | 41.1% | 25.6 | 649 |
| 22 | Mistral Large 3 | 30.0% | 21.3 | 649 |
| 23 | Grok 4.20 0309 (Non-Reasoning) | 31.4% | 20.9 | 649 |
| 24 | Mistral Medium 3.1 | 34.7% | 20.2 | 649 |

Main takeaway:
- the top three remain extremely tight: Claude Opus 4.6 (high reasoning), GPT-5.4 (xhigh reasoning), and Gemini 3.1 Pro Preview sit within **1.5 inverse-rank points**
- reasoning still matters a lot: GPT-5.4's `xhigh`, `high`, and `medium` variants all land in the top 6, while GPT-5.4 (no reasoning) drops to 21st
- newer additions such as Qwen 3.6 Plus, Gemma 4 31B Reasoning, and Xiaomi MiMo V2 Pro land in the middle of the pack rather than joining the frontier cluster

Across the full **1,247-item** validated set, the same top six hold: Claude Opus 4.6 (high reasoning), GPT-5.4 (xhigh reasoning), Gemini 3.1 Pro Preview, GPT-5.4 (high reasoning), Claude Sonnet 4.6 (high reasoning), and GPT-5.4 (medium reasoning), all with average correct rank between **1.12** and **1.16**.

---

## Model-Behavior Correlation

![Hard subset model correlation heatmap](images/06_models_correlation_hard_subset_two_plus_wrong.png)

This heatmap compares model behavior at the item level on the same hard subset.

Models that tend to score the same prompts similarly cluster together, while more unusual models sit further away from the pack. This is useful because raw leaderboard position is only part of the story: two models can have similar overall scores while making different kinds of mistakes.

---


## Example Benchmark Item

Here is a representative V2-style example:

**Examples**
- a surveyor's leveling rod
- a fishpole microphone boom
- a submarine periscope housing

**Anti-examples**
- a coiled steel measuring tape
- a folding wooden carpenter's rule
- a retractable cord dog leash

**Correct candidate**
- a collapsible stainless steel drinking straw

**Theme**
- physical objects that extend and retract by sliding rigid, nested tubular segments along a single axis

This shows the core idea of the benchmark:
- the model must infer a **narrow mechanism**, not just a broad category like "things that extend"
- the anti-examples are deliberately close enough to tempt a broader but wrong rule
- the correct answer is only obvious if the model identifies the precise latent theme

---

## Method Summary

### 1. Theme generation

Multiple strong LLMs generate candidate themes from random seeds. These themes are meant to be narrow, specific, and checkable rather than broad trivia categories.

### 2. Examples and anti-examples

For each theme, models generate:
- true examples that fit the exact theme
- anti-examples that fit a broader or neighboring pattern but not the exact one

### 3. Double-checking

Generated examples and anti-examples are reviewed by other models. Weak or internally inconsistent items are removed.

### 4. Validation

The benchmark runs explicit validation prompts that ask models to:
- check whether the true candidate matches the stated theme
- infer alternate themes from the examples
- detect cases where the candidate pack is too ambiguous

V2 adds a stricter ambiguity / exclusivity screen here.

### 5. Final pick task

Each benchmark prompt shows:
- 3 examples
- 3 anti-examples
- 8 candidates

Exactly one candidate is the hidden fourth true example. Models score all 8 candidates, and the results are turned into leaderboard metrics.

---

## Updates

- April 8, 2026: Claude Opus 4.6 (high reasoning), GPT-5.4 (high reasoning), GPT-5.4 Mini (xhigh reasoning), Qwen 3.6 Plus, Gemma 4 31B Reasoning, Xiaomi MiMo V2 Pro added
- March 16, 2026: V2



---

## Related Benchmarks

- [Sycophancy Benchmark](https://github.com/lechmazur/sycophancy/)
- [Writing Benchmark](https://github.com/lechmazur/writing/)
- [Translation Benchmark](https://github.com/lechmazur/translation/)
- [PACT: Multi-Round Bargaining Benchmark](https://github.com/lechmazur/pact/)
- [BAZAAR: Auction Market Benchmark](https://github.com/lechmazur/bazaar/)
- [Extended NYT Connections](https://github.com/lechmazur/nyt-connections/)
- [Step Game](https://github.com/lechmazur/step_game/)
- [Elimination Game](https://github.com/lechmazur/elimination_game/)
