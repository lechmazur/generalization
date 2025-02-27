# LLM Thematic Generalization Benchmark

This benchmark measures how effectively various LLMs can infer a narrow or specific "theme" (category/rule) from a small set of examples and anti-examples, then detect which item truly fits that theme among a collection of misleading candidates. The overall process involves generating themes, creating examples and anti-examples, filtering out low-quality data via a "double-check" step, and finally prompting LLMs to score the real example among several distractors.


## Visualizations

### 1. **Average Rank of the Correct Example** 
![04_model_bar_correct_rank](https://github.com/user-attachments/assets/563b9497-0f71-46c1-8890-12c1f8415d70)
This chart displays, for each model, the **average rank** that model assigns to the true example (when placed among seven distractors). Ranks range from 1 (top score) to 8 (lowest).  
- **Smaller values** indicate **better** performance, because it means the correct example is consistently placed near the top.  
- A bar height of 2.0 would mean that on average, the leftover correct item was the second-highest-scored candidate.

### 2. **Distribution of Ranks**
![05_model_rank_distribution](https://github.com/user-attachments/assets/28b0de6c-1c6a-44e8-b074-533494603c70)
A more granular view of the ranks each model assigns to the leftover correct example per file, showing how stable or varied those ranks are across different themes. 

### 3. **Model–Model Correlation**
![06_models_correlation](https://github.com/user-attachments/assets/fc70e67f-07fb-45da-924a-14336201da75)
A correlation matrix based on how similarly two models assign a “difference score” to the correct vs. anti-examples. It highlights which LLMs behave similarly or deviate significantly.

### 4. **How Often the Correct Example is the Highest Score**
![02_model_bar_correct_highest](https://github.com/user-attachments/assets/c18f7d14-358d-4704-bc0f-82e894e27f1f)
A stacked bar chart indicating how frequently each model places the real leftover example strictly at the top (or tied for top). This quickly shows which LLMs are best at ensuring the real item is #1 vs. merely near the top.

## Leaderboard

|Rank|Model|Avg Rank|Skipped/Total|
|----:|-----|-------:|------------:|
|1|Claude 3.7 Sonnet Thinking 16K|1.73|0/810|
|2|o1 (medium reasoning)|1.80|0/810|
|3|DeepSeek R1|1.80|0/810|
|4|Gemini 2.0 Flash Think Exp 01-21|1.84|0/810|
|5|o3-mini (medium reasoning)|1.85|0/810|
|6|Claude 3.7 Sonnet|1.88|0/810|
|7|Gemini 2.0 Pro Exp 02-05|1.89|0/810|
|8|Gemini 2.0 Flash Think Exp Old|1.90|0/810|
|9|GPT-4.5 Preview|1.93|0/810|
|10|Claude 3.5 Sonnet 2024-10-22|1.93|0/810|
|11|o1-mini|1.95|0/810|
|12|GPT-4o 2024-08-06|1.96|0/810|
|13|GPT-4o Feb 2025|2.00|0/810|
|14|Gemini 2.0 Flash|2.00|0/810|
|15|Gemini 2.0 Flash Exp|2.00|0/810|
|16|DeepSeek-V3|2.03|0/810|
|17|Qwen QwQ *|2.05|280/810|
|18|Llama 3.1 405B|2.08|0/810|
|19|Qwen 2.5 Max|2.08|2/810|
|20|Microsoft Phi-4|2.10|0/810|
|21|Mistral Large 2|2.11|0/810|
|22|Amazon Nova Pro|2.11|0/810|
|23|Llama 3.3 70B|2.12|0/810|
|24|Gemini 1.5 Pro (Sept)|2.13|0/810|
|25|Grok 2 12-12|2.21|0/810|
|26|Qwen 2.5 72B|2.21|0/810|
|27|Claude 3.5 Haiku|2.25|0/810|
|28|Mistral Small 3|2.25|0/810|
|29|MiniMax-Text-01|2.28|0/810|
|30|GPT-4o mini|2.30|0/810|
|31|Gemma 2 27B|2.60|0/810|


- Avg Rank is the mean ranking assigned to the correct example across 810 test files.
- Skipped indicates how many outputs failed to parse or didn’t follow the required output format (e.g., missing <number> and <score> tags).

## Benchmark Method in Detail

1. **Theme & Example Creation**

   We prompt high-quality LLMs (Claude 3.5 Sonnet, Grok 2, Gemini 1.5 Pro, GPT-4o, DeepSeek-V3) to generate **2,000** unique, succinct “themes” that foucs on a narrow concept. Each is based on a random trio of starting points, ensuring novelty.  

2. **Gather Examples & Anti-Examples**

   For each theme, we then request four `<example>` entries that specifically fit it, plus 20 `<anti_example>` entries that could belong to a broader or partially overlapping category but do **not** fit the exact theme. 

3. **Quality Check (“Double Check”)**  
   - We create specialized prompts that ask LLMs to *score* how well each of the **four real examples** (#1–4) matches the theme, and how well each of the **twenty anti-examples** (#5–24) fits the notion of being “broader or related but *not* the theme.”  
   - We parse these 24 numeric scores (per file, per LLM), computing standardized z-scores. If a real example scores poorly (z < -2.5) or if the top anti-examples fail to show sufficiently high “anti-example” scores, that file is discarded.  
   - From the initial 2,000 sets, we end up **retaining 810** sets (themes + examples + anti-examples).

4. **Final “Pick” Challenge**  
   - From each of the 810 validated sets, the final prompt includes 3 real examples + 3 anti-examples as context.
   - The *fourth* real example is hidden among 7 top "misleading" anti-examples (8 total)
   - We then prompt **26 different LLMs** to assign a 0–10 score to each of these 8 candidates. A perfect approach would always rank the correct example #1.

5. **Result Analysis**
   - If a model consistently places the real leftover example at or near the top, it implies strong thematic generalization.
   - We compile the results into multiple stats, including average rank, difference vs. the anti-example average, fraction of times the real item is top, etc.

## Examples

### 862

**Examples:** mathematical models, decision trees, flowcharts

**Anti-examples:** diagrams, maps, blueprints

**Candidates:**
1. checklists
2. spreadsheets
3. weather forecasts
4. **mind mapping** <- correct pick
5. road signs
6. instruction manuals
7. summaries
8. outlines

**Theme:** "Concepts or systems that involve solving complex problems through simplification or abstraction"

### 376

**Examples:** clay pot, bamboo sieve, calabash gourd spoon

**Anti-examples:** plastic serving spoon, rubber spatula, plastic strainer

**Candidates:**
1. cast iron skillet
2. **wooden mortar** <- correct pick
3. nylon cooking utensils
4. ceramic bowl 
5. stone grinding wheel
6. porcelain plate
7. silicone baking mat
8. bamboo steamer basket

**Theme:** "Tools or implements traditionally used in West African food preparation that are made primarily from a single, naturally occurring material."

## Note
Note that in general "verification vs. generation complexity asymmetry notions continue to hold empirically when replacing tailored algorithms with Language Models (LMs)" (https://arxiv.org/abs/2407.16831v1). "An LLM may be able to perform individual steps in a task, e.g. evidence verification, more accurately than the LLM can perform an entire task" (https://arxiv.org/abs/2306.00024). Verification tends to require fewer resources or simpler reasoning, so even very complex AI-generated benchmark items can be checked for correctness by another model with relative ease. 

In our benchmark, it's trivial for top LLMs to see if an example or counterexample fits a known theme. But hide the theme, and generalizing from a few examples to one underlying concept, then picking among adversarial alternatives, gets way harder. This asymmetry lets us use LLMs for both creating and evaluating benchmark items.

We also checked for self-grading bias. None detected.

## Updates and Other Benchmarks
- Feb 27, 2025: GPT-4.5 Preview added.
- Feb 25, 2025: Claude 3.7 Sonnet Thinking, Claude 3.7 Sonnet, GPT-4o Feb 2025, Gemini 2.0 Pro Exp 02-05, Gemini 2.0 Flash added.
- Feb 4, 2025: DeepSeek R1, o3-mini (medium reasoning effort), Gemini 2.0 Flash Thinking Exp 01-21, Qwen 2.5 Max, Microsoft Phi-4, Amazon Nova Pro, Mistral Small 3, MiniMax-Text-01 added.
- Also check out [Multi-Agent Elimination Game LLM Benchmark](https://github.com/lechmazur/elimination_game/), [LLM Public Goods Game](https://github.com/lechmazur/goods), [LLM Step Game](https://github.com/lechmazur/step_game), [LLM Creative Story-Writing Benchmark](https://github.com/lechmazur/writing), [LLM Confabulation/Hallucination Benchmark](https://github.com/lechmazur/confabulations/), [LLM Deception Benchmark](https://github.com/lechmazur/deception), [NYT Connections Benchmark](https://github.com/lechmazur/nyt-connections/), and [LLM Divergent Thinking Creativity Benchmark](https://github.com/lechmazur/divergent).
- Follow [@lechmazur](https://x.com/LechMazur) on X (Twitter) for other upcoming benchmarks and more.
