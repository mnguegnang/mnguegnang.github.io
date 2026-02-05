---
title: "Agentic AI & Post-training strategies"
excerpt: "Frameworks for stress-testing and improving frontier AI through post-training strategies for Specialized Real-World Tasks.<br/><img src='/images/Post_training_flowchart.png' width='500'height='993' style='object-fit: contain;'><br/><a href='https://github.com/YOUR_USERNAME/llm-reasoning-alignment' class='btn btn--info'>View Methodology</a>"
collection: portfolio
---

## Limitations of Current Frontier AI Models
Advanced AI models often struggle with multi-step reasoning, including those within specialized STEM domains. In professional environments, these models frequently fail to address complex tasks that require a synthesis of specialized knowledge. For instance, when tasked with executing a multi-step chemical reaction process or solving a dynamic engineering problem that requires using earlier results to inform later steps, the models fall short. Similarly, agents sometimes fail to execute user requests involving intricate agentic trajectories, such as a single-use, time-limited administrative credential password that should be deleted afterward. A major reason for Frontier AI models' failure is that they are pre-trained on general patterns to predict the most likely next token, rather than on deep domain knowledge.  

## Strategies to Enhance Frontier AI for Specialized Real-World Tasks

I applied advanced prompt engineering to identify model failures by designing specific, sequential constraints e.g., expertise, 
content, formatting) and reasonable expectations (i.e., things an expert in the domain would need) to address the prompt request. 
I then write the ideal responses in preparation for supervised fine-tuning (SFT) and grade models' responses in preparation for 
reinforcement learning.  Based on my background in Applied Mathematics, I design evaluation frameworks to align model behavior 
with human intent. In particular, I contribute to three principal methods: Reinforcement Learning from Human Feedback (RLHF), 
Reinforcement Learning with Verifiable Rewards (RLVR), and Rubric criteria.

### 1. Model Alignment: RLHF & RLVR
RLVR is ideal for tasks with objective answers, such as mathematical problems, while RLHF is best for tasks with subjective answers, e.g., writing a novel. This is where one of my major contributions through the Rubric criteria comes into play, balancing subjective and objective preferences. A core aspect of rubric criteria is that it enables the reward model to learn human preferences with better objective and granular learning proceduce compare to RHLF.

### 2. Advancing Reasoning: Chain-of-Thought (CoT)
Based on Chain-of-Thought (CoT) frameworks, I break complex tasks, such as those based on research questions in mathematics, into verifiable steps. This approach transforms technical solution steps into reliable ground-truth data.  For example, writing the optimization process  of recovering MRI images into fine-grained steps.

### 3. Error Analysis & Rubric Evaluation
I investigate YAML files to understand user requests, evaluate the rubric principle, and analyze agent trajectories to detect specific failure modes. I examine both the agent's and the world agent's reasoning to ensure the agent did not fail due to the world agent's mistake. Moreover, examine the transcript to identify where the model made a Function Calling error, for example, missing a call of the right function or calling the right function with the wrong parameters. 

### 4. Successful Model Improvement
Upon completion of the post-training phase, models acquire the specialized knowledge necessary to address problems they previously could not solve.

<figure>
    <img src="/images/Post_training_flowchart.png" alt="Boundedness" width='600'height='1191' style='object-fit: contain;'>
    <figcaption>Figure 1: Post-training flowchart.</figcaption>
</figure>

{% comment %}
![Alignment Diagram](/images/Post_training_flowchart.png)
*Figure 1: Post-training flowchart*
{% endcomment %}

## Methodology Repository
Since the specific models and data are proprietary, I have published a repository containing **demonstration templates** of the rubrics and prompting strategies I employ.

[View the Methodology on GitHub](https://github.com/YOUR_USERNAME/llm-reasoning-alignment)
