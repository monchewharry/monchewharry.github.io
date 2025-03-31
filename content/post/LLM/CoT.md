---
draft: false
authors:
  - admin
title: CoT
date: 2025-03-31
summary: Chain-of-Thought techniques are used to improve the reasoning abilities
  of large language models (LLMs) by encouraging them to think step-by-step
  before arriving at an answer.
categories: [LLM]
tags: [LLM, CoT]
---

Chain-of-thought (CoT) techniques are a set of methods used to improve the reasoning abilities of large language models (LLMs) by encouraging them to "think" step-by-step before arriving at an answer. Instead of jumping straight to a conclusion, the model generates a sequence of intermediate reasoning steps, much like how a human might break down a complex problem. This approach has proven especially effective for tasks requiring logical reasoning, arithmetic, commonsense reasoning, or multi-step problem-solving.

## How Chain-of-Thought Works
The core idea is to prompt or train the model to explicitly articulate its reasoning process. For example:
- **Without CoT**: Asked "What’s 15% of 80?", an LLM might directly output "12" (correctly or incorrectly) based on pattern recognition.
- **With CoT**: The model might respond: "To find 15% of 80, first convert 15% to a decimal, which is 0.15. Then multiply 0.15 by 80: 0.15 × 80 = 12. So, the answer is 12." 

This step-by-step breakdown makes the process transparent and often more accurate, as it reduces reliance on shortcuts or memorized answers.

## Key Chain-of-Thought Techniques
1. **Prompting-Based CoT**
	- **zero-shot (n-shot-learning) CoT**: Simply add a phrase like "Let’s think step by step" to the prompt. The model then tries to reason sequentially without prior examples. For instance: "Solve 2 + 3 × 4 step by step." The model might say: "First, do multiplication: 3 × 4 = 12. Then add 2: 12 + 2 = 14. The answer is 14."
	- **Few-Shot CoT**: Provide the model with a few examples of step-by-step reasoning in the prompt. For example: "Q: What’s 20% of 50? A: Step 1: 20% = 0.2. Step 2: 0.2 × 50 = 10. Answer: 10." Then ask a new question, and the model mimics the pattern.

2. **Fine-Tuning for CoT**
	Train the model on datasets where answers are paired with detailed reasoning steps. This ingrains the habit of breaking problems down, making it more natural for the model to reason explicitly even without special prompts.

3. **Self-Consistency with CoT**
	Generate multiple reasoning paths for the same question (e.g., three different ways to solve a math problem) and pick the most consistent answer. This reduces errors from flawed single attempts.

4. **Tree of Thoughts (ToT)**
	An advanced variant where the model explores multiple branching possibilities (like a decision tree) before converging on an answer. For example, solving a puzzle might involve testing different hypotheses step-by-step.

5. **Reflexion**
	The model reflects on its own reasoning after an attempt, critiques it, and adjusts if needed. For instance, if it gets a math problem wrong, it might recheck its steps and correct itself.

## Why It’s Effective
- **Improves Accuracy**: Breaking problems into smaller steps reduces the chance of overlooking details or making leaps that lead to errors.
- **Handles Complexity**: CoT shines in multi-step tasks (e.g., "If a car travels 60 miles in 2 hours, what’s its speed in miles per hour?") where direct answers are harder to intuit.
- **Transparency**: Users can see and verify the reasoning, making the model’s output more trustworthy.
- **Generalization**: It helps LLMs tackle unfamiliar problems by relying on logical processes rather than memorized patterns.

## Examples in Action
- **Math Problem**: "A store has a 20% off sale on a $50 item. What’s the final price?"    

CoT: "Step 1: Calculate the discount: 20% of 50 = 0.2 × 50 = 10. Step 2: Subtract the discount from the original price: 50 - 10 = 40. Final price is $40."
- **Logic Puzzle**: "If all cats are mammals and some mammals are black, are some cats black?"    

CoT: "Step 1: All cats are mammals, so cats are a subset of mammals. Step 2: Some mammals are black, meaning at least one mammal is black. Step 3: Since cats are mammals, it’s possible some cats are black, though not guaranteed. Answer: Yes, it’s possible."

## Limitations
- **Verbosity**: CoT can make responses longer than necessary for simple queries.
- **Compute Cost**: Generating step-by-step reasoning requires more processing power.
- **Prompt Dependency**: Zero-shot CoT might fail if the prompt isn’t clear, and few-shot CoT relies on good examples.
- **Overthinking**: The model might overcomplicate straightforward tasks.

## Evolution and Research
CoT was popularized by papers like "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models"[^1] , showing significant performance boosts on benchmarks like arithmetic (e.g., GSM8K) or commonsense reasoning (e.g., StrategyQA). Since then, it’s evolved into more sophisticated forms like ToT and self-consistency, pushing LLMs closer to human-like problem-solving.

[^1]: Jason Wei, Xuezhi Wang, Dale Schuurmans, Maarten Bosma, Fei Xia, Ed Chi, Quoc V Le, Denny Zhou, others. (2022). Chain-of-thought prompting elicits reasoning in large language models.