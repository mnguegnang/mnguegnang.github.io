---
permalink: /
title: "About me" #"Academic Pages is a ready-to-fork GitHub Pages template for academic personal websites"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

I am an **Applied Mathematics Researcher** and **AI Consultant** with over 6 years of experience bridging the gap between theoretical deep learning and scalable AI production.
My PhD research findings, obtained at **LMU Munich** and **RWTH Aachen**, improve earlier contributions on the mathematical foundations underpinning the training of deep Neural Networks.
I translate this theoretical expertise into high-impact industrial solutions, especially in **post-training strategies** including Reinforcement Learning from Human Feedback (**RLHF**), Reinforcement Learning with Verifiable Rewards (**RLVR**), and **Rubric development** to advance the reasoning capabilities and alignment of Large Language Models (LLMs) for top AI Labs.
My work combines academic precision with industrial requirements, delivering AI systems that are **accurate, aligned, and reliable** for specialized real-world tasks.

---

## Current Focus: LLM Alignment & Post-Training Strategy
As an Applied Mathematics Consultant for AI Model Training, I focus on **stress-testing frontier models** to expose critical reasoning and agentic failures, engineering the solutions that bridge performance gaps. My work centers on systematically identifying failure modes in complex logic and agent trajectories, then designing rigorous, math-grounded frameworks that empower AI systems to master specialized, multi-step tasks they were previously unable to execute efficiently.

*   **Advanced Alignment:** Expertise in **Reinforcement Learning from Human Feedback (RLHF)** and **Reinforcement Learning with Verifiable Rewards (RLVR)** to align model behavior with human intent.
*   **Reasoning Enhancement:** Utilizing **Chain-of-Thought (CoT)** frameworks to transform complex mathematical concepts into reliable ground-truth data, improving model reasoning in STEM domains.
*   **Agentic AI & Error Analysis:** Conducting deep analysis of **agent trajectories** to assess their alignment with user requests. I evaluate **Function Calling** precision (e.g., detecting "Trigger Phrases") and classify execution results to diagnose failures in multi-step reasoning tasks.
*   **Evaluation & Peer Review:** Designing detailed rubrics and reviewing peer contributions to ensure that prompt engineering strategies and **model reasoning outputs consistently meet key industry standards**.
  
---

## Academic Research Interest: Deep Learning, Optimization, Dynamical Systems & Stochastic Approximation 
My doctoral research focused on the **convergence analysis of gradient-based methods** for training deep neural networks. By applying principles from **optimization**, **dynamical systems**, and **stochastic approximation**, I bridged the critical gap between theoretical guarantees and practical implementation, enabling more efficient training strategies.

**Samples of Key Findings & Contributions:**

**1. Convergence of Gradient Descent (GD) for Learning Linear Neural Networks** *(Springer Nature, 2024)*
*   **Convergence to Critical Points:** Proved that under suitable step-size conditions, GD reliably converges to a critical point of the square loss function of linear neural networks, establishing a baseline stability guarantee for practical nonconvex optimization problems.
*   **Global Convergence & Rank Manifolds:** Extended prior findings of linear neural networks from Gradient Flow to GD. We demonstrated that for **two-layer networks**, GD converges to a global minimum for almost all initializations. For **three or more layers**, we proved convergence to a global minimum on the manifold of matrices of a fixed rank, advancing the understanding of optimization dynamics in deep architectures.

**2. Analysis of Stochastic Gradient Descent (SGD) for Learning Linear Neural Networks**
*   **Bridging the Theoretical Gap:** While state-of-the-art SGD analysis typically relies on Lipschitz continuous gradients (or their variants), a property that is not applicable to neural networks' objective functions, my work addressed this limitation. We directly analyzed SGD in the context of training linear neural networks, effectively closing the gap between the theoretical understanding of SGD and its practical behavior in non-convex objectives.    
*   **Dynamics Control & Convergence Guarantees:** Analyzed the discrete dynamics of SGD to devise rigorous bounds that control its evolution, effectively mitigating the **exploding gradient problem** during training. Based on this stability analysis, we established suitable **learning rate schedules (including adaptive learning)** that mathematically guarantee the **convergence of SGD for training linear neural networks**.

**3. Empirical Extension to General Nonlinear Networks**
*   **Validation with General Activation Functions:** Experimentally assessed whether the theoretical properties developed for linear networks hold in broader contexts involving **nonlinear activation functions**.
*   **Extension to Nonlinear Networks:** Demonstrated empirically that the theoretical findings regarding **learning rate and convergence** extend to **common nonlinear networks**, validating these mathematical principles in broader deep learning architectures.
  
---

## Further Industrial Solutions & Technical Leadership
Prior to my current focus on LLM alignment, I delivered successful machine learning solutions for industrial applications, contributing to the translation of mathematical models into deployed software.

*   **Explainable AI (XAI) for Industry:** Contributed to developing an ML-based anomaly detection framework that balances accuracy with fairness to enable interpretable AI solutions for high-stakes decision-making.
*   **Anomaly Detection for Telecom:** Developed systems for telecom base stations to detect anomalies in fuel consumption data.
*   **Predictive Modeling for Logistics:** Engineered algorithms for fuel consumption prediction to limit pilferage and optimize supply chain logistics.

**Technical Communication & Mentorship**
I demonstrate leadership through research coordination, academic mentorship, and the effective communication of complex technical ideas.
*   **Research Coordination:** As a **corresponding author** for multiple publications, I managed coordination with co-authors, editors, and reviewers, ensuring that complex findings were presented clearly and persuasively.
*   **Mentorship:** Assisted in supervising master's theses and taught exercise classes for mathematical foundations of **Machine Learning, Deep Learning, Data Science, and Optimization** at top German universities (LMU Munich, RWTH Aachen).
*   **Effective Communication:** Through international presentations and academic mentorship, I have mastered the ability to translate technical complexity into actionable insights that support strategic decision-making.

---

### 📄 [Download my Full CV](/files/cv.pdf)

{% comment %}
This is the front page of a website that is powered by the [Academic Pages template](https://github.com/academicpages/academicpages.github.io) and hosted on GitHub pages. [GitHub pages](https://pages.github.com) is a free service in which websites are built and hosted from code and data stored in a GitHub repository, automatically updating when a new commit is made to the repository. This template was forked from the [Minimal Mistakes Jekyll Theme](https://mmistakes.github.io/minimal-mistakes/) created by Michael Rose, and then extended to support the kinds of content that academics have: publications, talks, teaching, a portfolio, blog posts, and a dynamically-generated CV. Incidentally, these same features make it a great template for anyone that needs to show off a professional template!

 You can fork [this template](https://github.com/academicpages/academicpages.github.io) right now, modify the configuration and Markdown files, add your own PDFs and other content, and have your own site for free, with no ads!

A data-driven personal website
======
Like many other Jekyll-based GitHub Pages templates, Academic Pages makes you separate the website's content from its form. The content & metadata of your website are in structured Markdown files, while various other files constitute the theme, specifying how to transform that content & metadata into HTML pages. You keep these various Markdown (.md), YAML (.yml), HTML, and CSS files in a public GitHub repository. Each time you commit and push an update to the repository, the [GitHub pages](https://pages.github.com/) service creates static HTML pages based on these files, which are hosted on GitHub's servers free of charge.

Many of the features of dynamic content management systems (like Wordpress) can be achieved in this fashion, using a fraction of the computational resources and with far less vulnerability to hacking and DDoSing. You can also modify the theme to your heart's content without touching the content of your site. If you get to a point where you've broken something in Jekyll/HTML/CSS beyond repair, your Markdown files describing your talks, publications, etc. are safe. You can rollback the changes or even delete the repository and start over - just be sure to save the Markdown files! You can also write scripts that process the structured data on the site, such as [this one](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.ipynb) that analyzes metadata in pages about talks to display [a map of every location you've given a talk](https://academicpages.github.io/talkmap.html).

For those users that need more advanced functionality, the template also supports the following popular tools:
- [MathJax](https://www.mathjax.org/) for mathematical equations
- [Mermaid](https://mermaid.js.org/) for diagraming
- [Plotly](https://plotly.com/javascript/) for plotting

Getting started
======
1. Register a GitHub account if you don't have one and confirm your e-mail (required!)
1. Fork [this template](https://github.com/academicpages/academicpages.github.io) by clicking the "Use this template" button in the top right. 
1. Go to the repository's settings (rightmost item in the tabs that start with "Code", should be below "Unwatch"). Rename the repository "[your GitHub username].github.io", which will also be your website's URL.
1. Set site-wide configuration and create content & metadata (see below -- also see [this set of diffs](https://archive.is/3TPas) showing what files were changed to set up [an example site](https://getorg-testacct.github.io) for a user with the username "getorg-testacct")
1. Upload any files (like PDFs, .zip files, etc.) to the files/ directory. They will appear at https://[your GitHub username].github.io/files/example.pdf.  
1. Check status by going to the repository settings, in the "GitHub pages" section

Site-wide configuration
------
The main configuration file for the site is in the base directory in [_config.yml](https://github.com/academicpages/academicpages.github.io/blob/master/_config.yml), which defines the content in the sidebars and other site-wide features. You will need to replace the default variables with ones about yourself and your site's github repository. The configuration file for the top menu is in [_data/navigation.yml](https://github.com/academicpages/academicpages.github.io/blob/master/_data/navigation.yml). For example, if you don't have a portfolio or blog posts, you can remove those items from that navigation.yml file to remove them from the header. 

Create content & metadata
------
For site content, there is one Markdown file for each type of content, which are stored in directories like _publications, _talks, _posts, _teaching, or _pages. For example, each talk is a Markdown file in the [_talks directory](https://github.com/academicpages/academicpages.github.io/tree/master/_talks). At the top of each Markdown file is structured data in YAML about the talk, which the theme will parse to do lots of cool stuff. The same structured data about a talk is used to generate the list of talks on the [Talks page](https://academicpages.github.io/talks), each [individual page](https://academicpages.github.io/talks/2012-03-01-talk-1) for specific talks, the talks section for the [CV page](https://academicpages.github.io/cv), and the [map of places you've given a talk](https://academicpages.github.io/talkmap.html) (if you run this [python file](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.py) or [Jupyter notebook](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.ipynb), which creates the HTML for the map based on the contents of the _talks directory).

**Markdown generator**

The repository includes [a set of Jupyter notebooks](https://github.com/academicpages/academicpages.github.io/tree/master/markdown_generator
) that converts a CSV containing structured data about talks or presentations into individual Markdown files that will be properly formatted for the Academic Pages template. The sample CSVs in that directory are the ones I used to create my own personal website at stuartgeiger.com. My usual workflow is that I keep a spreadsheet of my publications and talks, then run the code in these notebooks to generate the Markdown files, then commit and push them to the GitHub repository.

How to edit your site's GitHub repository
------
Many people use a git client to create files on their local computer and then push them to GitHub's servers. If you are not familiar with git, you can directly edit these configuration and Markdown files directly in the github.com interface. Navigate to a file (like [this one](https://github.com/academicpages/academicpages.github.io/blob/master/_talks/2012-03-01-talk-1.md) and click the pencil icon in the top right of the content preview (to the right of the "Raw | Blame | History" buttons). You can delete a file by clicking the trashcan icon to the right of the pencil icon. You can also create new files or upload files by navigating to a directory and clicking the "Create new file" or "Upload files" buttons. 

Example: editing a Markdown file for a talk
![Editing a Markdown file for a talk](/images/editing-talk.png)

For more info
------
More info about configuring Academic Pages can be found in [the guide](https://academicpages.github.io/markdown/), the [growing wiki](https://github.com/academicpages/academicpages.github.io/wiki), and you can always [ask a question on GitHub](https://github.com/academicpages/academicpages.github.io/discussions). The [guides for the Minimal Mistakes theme](https://mmistakes.github.io/minimal-mistakes/docs/configuration/) (which this theme was forked from) might also be helpful.
{% endcomment %}
