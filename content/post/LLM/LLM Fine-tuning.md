---
draft: false
authors:
  - admin
title: LLM Fine-tuning
date: 2025-04-01
summary: Fine-tuning is suitable for adjusting behavior or incorporating
  domain-specific knowledge.
categories: [LLM]
tags: [LLM, fine-tuning]
---

Fine-tuning a Large Language Model (LLM) involves adjusting a pre-trained model to improve its performance on a specific task or domain. Pre-trained LLMs, like those trained on vast general datasets, already have a strong foundation in language understanding. Fine-tuning tailors this knowledge to specialized use cases. 

>Recommend reading technical report _The ultimate guide to fine-tuning LLMs from basics to breakthroughs_ that thoroughly examines the process of fine-tuning Large Language Models (LLMs).[^1]

## Fine-tuning approaches
### Full Fine-Tuning

- **What it is**: This approach updates all the parameters (weights) of the pre-trained model during training on the new task-specific dataset.
- **How it works**: The model is exposed to the new data, and through backpropagation, every layer—from input to output—is adjusted to minimize the error for the target task.
- **Pros**: 
	- Maximizes flexibility, allowing the model to deeply adapt to the new task.
	- Often yields the best performance when you have enough data and computational resources.
- **Cons**: 
	- Requires significant computational power and memory since every parameter is updated.
	- Risks overfitting if the task-specific dataset is small, and can lead to "<mark>catastrophic forgetting</mark>" where the model loses some of its general knowledge.
- **Use case**: Fine-tuning a model like BERT for sentiment analysis on a large dataset of customer reviews.
### Parameter-Efficient Fine-Tuning (PEFT)

- **What it is**: Instead of updating all parameters, PEFT methods tweak only a small subset or add lightweight components to the model, keeping most of the original weights frozen.
	- **Adapter Tuning**: Adds small, task-specific layers adapter between the model’s existing layers. Only these adapters are trained, while the core model stays unchanged. HuggingFace supports adapter configurations through the PEFT library. During fine-tuning, new adapters are integrated into the model using LoraConfig. [^2]
	- **LoRA**: Introduces low-rank updates to the weight matrices, adjusting them with minimal additional parameters.
	- **Prompt Tuning**: Trains soft prompts (learnable input embeddings) rather than modifying the model itself, guiding it to perform the task.
- **Pros**: 
	- Much less resource-intensive—ideal for limited compute or when fine-tuning on multiple tasks.
	- Preserves the pre-trained model’s general knowledge, reducing forgetting.
- **Cons**: 
	- May not achieve the same level of performance as full fine-tuning on highly specialized tasks.
	- Requires careful design of the added components.
- **Use case**: Adapting a massive model like GPT-3 for a niche task (e.g., medical diagnosis) without needing a supercomputer.

### Mixture of Experts (MoE)

- **What it is**: A technique where the model uses multiple specialized sub-networks (experts), and a gating mechanism dynamically selects which experts to activate for a given input, rather than fine-tuning the entire model.
- **How it works**: The pre-trained LLM is augmented with a set of expert modules. During inference or fine-tuning, the gating network (often a small neural network) routes input to a subset of experts based on task or context. Only the selected experts and the gating mechanism are fine-tuned.
- **Pros**: 
	- Scales efficiently—adds capacity without retraining the whole model.
	- Experts can specialize in different domains, improving performance on diverse tasks.
- **Cons**: 
	- Increased complexity in training and inference due to the gating mechanism.
	- May require more memory to store multiple experts, even if only a few are used per input.  
- **Use case**: Fine-tuning a language model for multi-domain tasks (e.g., code generation and translation) where different experts handle distinct skills.

### Mixture of Agents (MoA)

- **What it is**: An approach that combines multiple distinct LLMs or agents, each with its own strengths, to collaboratively solve a task, rather than fine-tuning a single model.
- **How it works**: A coordinator or aggregator (e.g., a meta-agent) orchestrates inputs and outputs across a group of pre-trained LLMs. Each agent processes the input independently, and their responses are combined (e.g., via voting, weighting, or synthesis) to produce a final output. Fine-tuning, if any, occurs at the aggregator level.
- **Pros**: 
	- Leverages diverse model capabilities without modifying individual models.
	- Can outperform single models by integrating complementary strengths (e.g., one agent for reasoning, another for creativity).
- **Cons**: 
	- Higher computational cost due to running multiple models.
	- Requires a well-designed aggregation strategy to avoid conflicts or redundancy.
- **Use case**: Building a system where one agent generates text, another critiques it, and a third refines it (e.g., collaborative writing or complex problem-solving).

### Transfer Learning with Feature Extraction
   - **What it is**: The pre-trained model’s weights are frozen, and it’s used as a feature extractor. Only a new, task-specific head (e.g., a classifier) is added and trained on top of the extracted features.
   - **How it works**: The LLM processes input data to generate embeddings or representations, and a smaller, separate model (like a linear layer) is trained on these for the specific task.
   - **Pros**: 
     - Extremely efficient since the bulk of the model isn’t retrained.
     - Works well with small datasets, as it leverages the pre-trained features.
   - **Cons**: 
     - Limited adaptability—the frozen model might not fully align with the new task’s nuances.
     - Performance can lag behind full fine-tuning.
   - **Use case**: Using a pre-trained model to classify short text snippets (e.g., tweets) with a simple added layer.

### Instruction Tuning
   - **What it is**: The model is fine-tuned on a dataset of instructions paired with desired outputs, teaching it to follow human-like directives or generalize across tasks.
   - **How it works**: The training data consists of examples like “Summarize this text” followed by a summary, often using supervised learning or reinforcement learning with human feedback (RLHF).
   - **Pros**: 
     - Enhances the model’s ability to handle diverse, user-defined tasks without needing task-specific models.
     - Aligns the model more closely with human expectations.
   - **Cons**: 
     - Requires high-quality, diverse instruction datasets, which can be hard to create.
     - May not excel at highly specialized tasks compared to targeted fine-tuning.
   - **Use case**: Turning a general LLM into a conversational assistant like me, capable of answering varied questions.

### Reinforcement Learning from Human Feedback (RLHF)
   - **What it is**: A fine-tuning method where the model is optimized based on human preferences or rewards rather than a fixed dataset.
   - **How it works**: Humans rank or score model outputs, and a reward model is trained to guide the LLM’s adjustments via reinforcement learning.
   - **Pros**: 
     - Aligns the model with subjective goals (e.g., helpfulness, safety) that are hard to capture with standard loss functions.
     - Can improve over time with iterative feedback.
   - **Cons**: 
     - Time-consuming and expensive due to the need for human input.
     - Reward design can be tricky—poorly defined rewards might lead to unintended behavior.
   - **Use case**: Fine-tuning a chatbot to be more polite or avoid controversial responses, as seen in models like ChatGPT.

## Choose between RAG and Fine-tuning
When considering external data access, RAG is likely a superior option for applications needing to access external data sources. Fine-tuning, on the other hand, is more suitable if you require the model to adjust its behaviour, and writing style, or incorporate domain-specific knowledge. 
![RAG_vs_Fine_tuning](https://arxiv.org/html/2408.13296v1/extracted/5809594/images/RAG_vs_Fine_tuning.png)

## Overview and Trends
Each approach balances trade-offs between performance, efficiency, and scalability. Full fine-tuning is the heavyweight champ for precision but demands resources. PEFT methods like LoRA are gaining traction for their practicality, especially as models grow larger (think billions of parameters). Instruction tuning and RLHF shine in making models more versatile and user-friendly, which is why they’re popular for conversational AIs. The choice depends on the task, dataset size, and available compute—there’s no one-size-fits-all.

---

[^1]: Venkatesh Balavadhani Parthasarathy, Aafaq Khan Ahtsham Zafar, Arsalan Shahid. (2024). The ultimate guide to fine-tuning LLMs from basics to breakthroughs: An exhaustive review of technologies, research, best practices, applied research challenges and opportunities.
[^2]: Adapter-based methods introduce additional trainable parameters after the attention and fully connected layers of a frozen pre-trained model, aiming to reduce memory usage and accelerate training. The specific approach varies depending on the adapter; it might involve adding an extra layer or representing the weight updates delta (W) as a Low-rank decomposition of the weight matrix. Regardless of the method, adapters are generally small yet achieve performance comparable to fully fine-tuned models, allowing for the training of larger models with fewer resources.