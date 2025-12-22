# Promptify: Evolutionary Prompt Discovery for Large Language Models

A lightweight local-first evolutionary system that automatically discovers effective prompting strategies through genetic optimization. Instead of manually crafting prompts, Promptify treats them as competing individuals in a population that evolve over time through selection, mutation, and crossover.

## Overview

Promptify uses genetic algorithms to optimize LLM prompts without any model training or fine-tuning. Given a task and a set of seed prompts, the system:

1. *Evaluates* each prompt's performance on factual QA tasks
2. *Selects* top performers as "parents"
3. *Mutates* prompts (adds constraints, removes verbosity, simplifies)
4. *Evolves* over 10-15 generations to discover optimal instructions

## How It Works

### 1. Task Evaluation

Each prompt is tested on 8 factual QA tasks:
```python
Task("What is the capital of France?", "Paris")
Task("What is 15 + 27?", "42")
Task("Who wrote '1984'?", "George Orwell")
...
```

### 2. Fitness Function

Prompts are scored on four metrics:
- Correctness (50%): Does the answer match expected output?
- Brevity (20%): Is the response concise without being too short?
- Directness (20%): Does it avoid filler phrases?
- Constraint (10%): Does it follow length limits?

### 3. Mutation Operators

Prompts evolve through weighted mutations:
- Simplify (33%): Keep only first sentence
- Remove verbosity (33%): Strip filler words
- Truncate (17%): Hard cut at 10 words
- Add constraint (8%): Append brevity instruction
- Tighten brevity (8%): Intensify existing constraints

### 4. Selection Strategy

- Top 25% (elite) survive to next generation
- 70% of offspring created via mutation
- 30% created via crossover (sentence mixing)
- Invalid prompts rejected (grammar checks, length limits)

## Results

### Discovered Optimal Prompts

Starting from 16 diverse seed prompts, Promptify converged to these top performers:

| Rank | Prompt | Fitness | Length | Correctness | Brevity |
|------|--------|---------|--------|-------------|---------|
| 1 | "Do not include examples or elaboration." | 0.901 | 6 words | 87.5% | 81.7% |
| 2 | "Answer directly without prefacing." | 0.891 | 4 words | 87.5% | 77.0% |
| 3 | "Be brief. Do not add unnecessary details." | 0.885 | 7 words | 87.5% | 73.7% |

Key Finding: The evolved winner ("Do not include examples or elaboration") outperformed naive baselines like "Answer the question" by +12% fitness.

### Evolution Dynamics

- Fast convergence**: Optimal fitness reached by generation 3
- Stable plateau: Maintained 0.90+ fitness for 9 generations
- Population quality: Worst performer improved from 0.35 → 0.88
- Length control: Median prompt length stabilized at 6 words

### Component Score Analysis

| Metric | Gen 0 | Gen 12 | Improvement |
|--------|-------|--------|-------------|
| Correctness | 82% | 87% | +5% |
| Brevity | 37% | 78% | +110% |
| Directness | 100% | 100% | Maintained |
| Constraint | 100% | 100% | Maintained |

Insight: Evolution focused almost entirely on optimizing brevity, which had the most room for improvement. This demonstrates the system correctly identifies optimization bottlenecks.

### Key Observations

### 1. Brevity is King -
Evolution focused 110% improvement on brevity scores, demonstrating that concise instructions work better for factual QA.

### 2. Seeds Matter -
Starting fitness of 0.891 (best seed) → final 0.901 shows seed quality is critical. Well-curated initial prompts yield better results.

### 3. Fast Convergence -
Optimal fitness reached by generation 3, suggesting the prompt space for simple QA is relatively small and easy to optimize.

### 4. Negative Instructions Win -
"Do NOT include X" consistently outperformed positive instructions like "Be concise" — telling the LLM what to avoid works better.

### 5. Length Sweet Spot -
6 words emerged as optimal prompt length. Shorter (4 words) lost clarity, longer (8+) added no value.

## How To Run

1. Make sure you have Python 3.8+ installed.
2. Clone this repository on your local machine.
3. Install the required dependencies:
```bash
pip install numpy matplotlib
```
4. Install Ollama & set up llama3:latest.
5. Open and run the cells of the `Promptify.ipynb` Jupyter Notebook.

Promptify
A lightweight genetic algorithm–based evolutionary framework for automated prompt engineering that discovers and evolves high-performing LLM instruction sets through selection and mutation to optimize task performance.