---
title: "Agentic AI & Post-training strategies"
excerpt: "Frameworks for stress-testing frontier models and optimizing logical reasoning via post-training strategies.<br/><br/><a href='https://github.com/YOUR_USERNAME/llm-reasoning-alignment' class='btn btn--info'>View Methodology</a>"
collection: portfolio
header:
  teaser: /images/agentic_alignment_diagram.png
---

## The Challenge
Advanced AI models often struggle with multi-step reasoning, including those of specialized STEM domains. 
In professional environments, models frequently fail to address complex tasks that require a synthesis of 
specialized knowledge. In the same line agent fail to execute user requests that involve complex agentic trajectories. 
A major reason for Frontier models' failure is that they are pre-trained on general patterns (knowledge) to predict 
the most likely next token, without necessarily relying on deep domain knowledge. 

## Approach to improve Frontier AI qualities on specialized real-world tasks

I applied advanced prompt engineering to identify model failures by designing specific, sequential constraints e.g., expertise, 
content, formatting) and reasonable expectations (i.e., things an expert in the domain would need) to address the prompt request. 
I then write the ideal responses in preparation for supervised fine tuning (SFT) and grade models' responses in preparation for 
reinforcement learning.  Based on my background in Applied Mathematics, I design evaluation frameworks to align model behavior 
with human intent. In particular, I contribute to three principal methods: Reinforcement Learning from Human Feedback (RLHF), 
Reinforcement Learning with Verifiable Rewards (RLVR), and Rubric criteria.

### 1. Model Alignment with RLHF & RLVR)
RLVR is ideal for tasks with objective answers, such as mathematical problems, while RLHF is best for tasks with subjective answers, e.g., writing a novel. This is where one of my major contributions through the Rubric criteria comes into play, balancing subjective and objective preferences. A core aspect of rubric criteria is that it enables the reward model to learn human preferences with better objective and granular learning proceduce compare to RHLF.


*   *Technique:* Decomposing complex integrals or optimization problems into atomic logical steps to prevent "reasoning jumps."

### 2. Advanced Alignment (RLHF & RLVR)
I apply **Reinforcement Learning from Human Feedback** strategies to define reward models.1. Reasoning Enhancement (CoT)
*   **RLVR Focus:** For mathematical tasks, I advocate for **Verifiable Rewards**—ensuring that the model is rewarded not just for sounding correct, but for producing mathematically provable steps.

### 3. Error Analysis & Rubric Design
I analyze agent trajectories to detect specific failure modes:
*   **Function Calling Errors:** Detecting when an agent fails to trigger the correct tool.
*   **Logic Drift:** Identifying where the derivation deviates from axioms.

![Alignment Diagram](/images/agentic_alignment_diagram.png)
*Figure 1: The conceptual flow of aligning agentic reasoning.*

## Methodology Repository
Since the specific models and data are proprietary, I have published a repository containing **demonstration templates** of the rubrics and prompting strategies I employ.

[View the Methodology on GitHub](https://github.com/YOUR_USERNAME/llm-reasoning-alignment)
