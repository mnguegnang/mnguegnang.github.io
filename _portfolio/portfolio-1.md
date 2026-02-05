---
title: "Analysis of Training Neural Networks"
excerpt: "Global convergence of GD for training neural networks<br/><img src='/images/Loss_evolution_tanh_constantetak_tanh_d70_r25_stepsize0_00003_epochs1000000.png' width='700'><br/><a href='https://github.com/mnguegnang/DNN_project/tree/master/GD_for_DNN' class='btn btn--info'>View code</a>"
collection: portfolio
---

## Problem 
Training neural networks involves optimizing a nonconvex loss function with respect to model parameters. This work focuses on the square loss function of fully connected networks. The primary challenges include identifying optimal parameters and establishing learning rate conditions that guarantee convergence of the optimizer, such as the gradient descent (GD) algorithm.

## GD training dynamics
A critical component of the analysis is the development of a norm-based bound on GD to address the exploding gradient problem. The boundedness of GD is subsequently used to establish a fundamental property of iterative descent methods, demonstrating that GD satisfies the requirements of the discrete version of Lojasiewicz's theorem. The plots below depict the evolution of the overparameterized weight matrices for the three networks throughout training.

<figure>
    <img src="/images/Parameterized_matrix_norms_comparison_linear_constantetak_linear_d70_r35_stepsize0_00003_epochs1000000.png" alt="Boundedness" width="700">
    <figcaption>Figure 1: Boundedness of the overparameterized matrix across networks.</figcaption>
</figure>
{% comment %}
![Boundedness of overparameterized](/images/Parameterized_matrix_norms_comparison_linear_constantetak_linear_d70_r35_stepsize0_00003_epochs1000000.png)
*Figure 1: Boundedness of the overparameterized matrix across networks*
{% endcomment %}

## Global convergence of GD
This work studies the convergence properties of GD for training deep neural networks and demonstrates  that GD converges to a global minimizer for almost all initializations. Empirical results further prove that the theoretical findings on learning rate and convergence generalize to common nonlinear networks, thereby validating these principles in broader deep learning architectures.

<figure class="half">
    <img src="/images/Loss_evolution_linear_constantetak_linear_d70_r35_stepsize0_00003_epochs1000000.png" alt="Linear network" width="700">
    <img src="/images/Loss_evolution_tanh_constantetak_tanh_d70_r25_stepsize0_00003_epochs1000000.png" alt="Nonlinear networks" width="700">
    <figcaption><em>Figure 2: Global convergence of GD for training linear neural networks on the left panel and nonlinear networks on the right panel.</em></figcaption>
</figure>


{% comment %}
![Global convergence of GD in linear neural networks](/images/Loss_evolution_linear_constantetak_linear_d70_r35_stepsize0_00003_epochs1000000.png)
*Figure 2: Global convergence GD for training linear neural networks under constant learning rate*

![Global convergence of GD in nonlinear neural networks](/images/Parameterized_matrix_norms_comparison_tanh_constantetak_tanh_d70_r25_stepsize0_00003_epochs1000000.png)
*Figure 3: Global convergence GD for training nonlinear neural networks under constant learning rate*



This is an item in your portfolio. It can be have images or nice text. If you name the file .md, it will be parsed as markdown. If you name the file .html, it will be parsed as HTML.
{% endcomment %}
