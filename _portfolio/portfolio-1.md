---
title: "Training Neural Networks"
excerpt: "Global convergence of GD for training neural networks<br/><img src='/images/Parameterized_matrix_norms_comparison_tanh_constantetak_tanh_d70_r25_stepsize0_00003_epochs1000000.png'>"
collection: portfolio
---

## Problem 
Training neural networks involves optimizing a nonconvex loss function with respect to model parameters. This work focuses on the square loss function of fully connected networks. The primary challenges include identifying optimal parameters and establishing learning rate conditions that guarantee convergence of the optimizer, such as the gradient descent (GD) algorithm.

## GD training dynamics
A critical component of the analysis is the development of a norm-based bound on gradient descent to address the exploding gradient problem. The plots below depict the evolution of the overparameterized weight matrices for the three networks throughout training.

## Global convergence of GD
This work studies the convergence properties of GD for training deep neural networks and demonstrates  that GD converges to a global minimizer for almost all initializations. Empirical results further prove that the theoretical findings on learning rate and convergence generalize to common nonlinear networks, thereby validating these principles in broader deep learning architectures.

![Global convergence of GD](/images/Parameterized_matrix_norms_comparison_tanh_constantetak_tanh_d70_r25_stepsize0_00003_epochs1000000.png)

This is an item in your portfolio. It can be have images or nice text. If you name the file .md, it will be parsed as markdown. If you name the file .html, it will be parsed as HTML. 
