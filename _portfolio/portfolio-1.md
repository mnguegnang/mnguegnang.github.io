---
title: "Analysis of Training Neural Networks"
excerpt: "Global convergence of GD for training neural networks<br/><img src='/images/Loss_evolution_tanh_constantetak_tanh_d70_r25_stepsize0_00003_epochs1000000.png' style='width: 100%; max-width: 650px; height: auto; display: block; margin: 20px auto; border: 1px solid #ddd; border-radius: 5px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);'><br/><a href='https://github.com/mnguegnang/DNN_project/tree/master/GD_for_DNN' class='btn btn--info'>View code</a>"
collection: portfolio
order: 3
---

<div class="links" style="margin-bottom: 1.5rem;">
  <a href="https://github.com/mnguegnang/DNN_project/tree/master/GD_for_DNN" target="_blank" class="btn btn-sm z-depth-0" role="button">
    GitHub — Experiment Code
  </a>
  <a href="https://link.springer.com/content/pdf/10.1186/s13662-023-03797-x.pdf" target="_blank" class="btn btn-sm z-depth-0" role="button">
    Paper — Springer Nature (2024)
  </a>
  <a href="https://arxiv.org/abs/2108.02040" target="_blank" class="btn btn-sm z-depth-0" role="button">
    Preprint — arXiv 2108.02040
  </a>
</div>

---

## TL;DR

- Training deep neural networks requires optimizing a nonconvex loss function, where convergence guarantees remain a fundamental open challenge.
- Extended prior gradient flow analyses to the discrete gradient descent (GD) setting and proved that, under explicit step-size conditions, GD converges to a global minimum for almost all initializations in deep linear networks — without the restrictive deficiency-margin or boundedness assumptions required by earlier work.
- A key theoretical contribution is a norm-based bound on GD iterates that controls the exploding gradient problem, enabling application of a discrete Lojasiewicz inequality. Notably, the derived maximum permissible step size does not decrease exponentially with network depth, distinguishing this result from prior analyses. Empirical experiments further demonstrate that these convergence properties generalize to nonlinear architectures with tanh and ReLU activations.

---

## Problem Statement & Research Objective

### Context & Significance

Modern deep learning relies almost entirely on gradient-based optimization to train neural networks. Despite remarkable empirical success, the theoretical understanding of *why* gradient descent finds good solutions in highly nonconvex loss landscapes remains incomplete. The objective function of even a simple fully connected network is riddled with saddle points and spurious local minima, yet practitioners observe that standard GD and SGD routinely converge to high-quality solutions. Closing the gap between this empirical observation and rigorous mathematical guarantees is a central question in optimization theory and deep learning.

Prior theoretical work on this problem either analyzed gradient flow (the continuous-time limit of GD, which ignores discretization effects) or imposed restrictive assumptions such as deficiency-margin conditions and a priori boundedness of iterates. These assumptions rarely hold in practice and limit the applicability of the resulting convergence theorems to real training scenarios.

### Mathematical Formulation

The problem is framed as minimizing the **square loss** of a deep linear network, a product of weight matrices that parameterize a deep matrix factorization. Given training data, the network output is the composition of *L* weight matrices, and the loss measures the squared Frobenius-norm distance between the network output and a target matrix. Despite the linearity of the individual layers, the loss function is **nonconvex** in the factored parameters due to the multiplicative coupling between weight matrices. The analysis focuses on gradient descent with a fixed or slowly decaying learning rate applied directly to these factored parameters.

### Scope

**In scope:** Convergence analysis of discrete GD for deep linear networks, including two-layer, three-layer, and general *L*-layer architectures; derivation of explicit step-size conditions; empirical validation on nonlinear networks. **Out of scope:** Stochastic gradient descent analysis (addressed in a companion SampTA 2023 paper), batch normalization effects, and convolutional architectures.

### Success Criteria

Establish that GD converges to a global minimum for almost all initializations under an explicit step-size bound that does not degrade exponentially with network depth, and validate the theory empirically on standard nonlinear architectures.

---

## Methodology & Approach

### Boundedness of GD Iterates

A critical first step in the analysis is proving that the GD iterates remain bounded throughout training. Without this property, gradient norms can grow without control — the well-known **exploding gradient problem** — and no convergence guarantee is possible. The approach develops a novel norm-based bound: by exploiting the algebraic structure of the gradient in deep matrix factorizations, the work shows that the product of weight matrix norms is non-increasing along the GD trajectory under the prescribed step-size condition. This boundedness result holds without assuming the iterates stay in a pre-specified compact set, a key improvement over earlier analyses.

<figure>
    <img src="/images/Parameterized_matrix_norms_comparison_linear_constantetak_linear_d70_r35_stepsize0_00003_epochs1000000.png" alt="Boundedness of GD iterates" style='width: 100%; max-width: 700px; height: auto; display: block; margin: 20px auto; border: 1px solid #ddd; border-radius: 5px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);'>
    <figcaption>Figure 1: Boundedness of the overparameterized weight matrices across three network architectures throughout GD training. The norms remain controlled, empirically confirming the theoretical bounds.</figcaption>
</figure>

### Convergence via Discrete Lojasiewicz Inequality

Once boundedness is established, the analysis connects the GD dynamics to the **Lojasiewicz inequality**, a powerful tool from real algebraic geometry. The square loss of a linear network is a polynomial (hence real-analytic) function, which satisfies a Lojasiewicz gradient inequality near every critical point. The boundedness result ensures that the GD trajectory remains in a region where this inequality applies, and a discrete-time version of the Lojasiewicz convergence theorem then guarantees that the iterates converge to a single critical point of the loss.

### From Critical Points to Global Minima

Converging to a critical point is necessary but not sufficient — the critical point could be a saddle or a local (non-global) minimum. The analysis proceeds layer by layer:

- **Two-layer networks:** The structure of the loss function ensures that every critical point with a certain rank property is a global minimum. Combined with the Lojasiewicz result, this shows that GD converges to a global minimum for almost all initializations.
- **Three or more layers:** The analysis proves convergence to a global minimum *on the manifold of matrices of a fixed rank*. The rank is determined by the initialization and remains invariant along the GD trajectory, a property inherited from the continuous gradient flow analysis and verified to persist under discretization.

### Step-Size Conditions

A distinctive feature of this work is the derived step-size bound. Unlike earlier discrete-time analyses where the maximum permissible step size **decreases exponentially** with network depth *L*, the conditions established here are polynomially dependent on the problem parameters. This means the theory applies to deeper architectures without requiring impractically small learning rates, making the results more relevant to real training scenarios.

---

## Results & Impact

### Key Theoretical Results

<table>
  <thead>
    <tr>
      <th>Result</th>
      <th>Network Depth</th>
      <th>Guarantee</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Convergence to critical point</td>
      <td>Any <em>L</em></td>
      <td>GD converges to a critical point of the square loss under the explicit step-size bound</td>
    </tr>
    <tr>
      <td>Global convergence</td>
      <td>L = 2</td>
      <td>GD converges to a <strong>global minimum</strong> for almost all initializations</td>
    </tr>
    <tr>
      <td>Rank-manifold convergence</td>
      <td>L &ge; 3</td>
      <td>GD converges to a global minimum on the manifold of fixed-rank matrices</td>
    </tr>
    <tr>
      <td>Step-size scalability</td>
      <td>Any <em>L</em></td>
      <td>Maximum step size does <strong>not</strong> decrease exponentially with depth</td>
    </tr>
  </tbody>
</table>

### Empirical Validation on Nonlinear Networks

The theoretical framework is developed for linear networks, but the practical implications extend further. Experiments on networks with **tanh** and **ReLU** activation functions demonstrate that the derived learning rate bounds and convergence behavior carry over to nonlinear architectures. Exceeding the prescribed step-size bound leads to training divergence, confirming the tightness of the theoretical conditions.

<figure class="half">
    <img src="/images/Loss_evolution_tanh_constantetak_tanh_d70_r25_stepsize0_00003_epochs1000000.png" alt="Nonlinear networks" style='width: 100%; max-width: 700px; height: auto; display: block; margin: 20px auto; border: 1px solid #ddd; border-radius: 5px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);'>
    <img src="/images/Loss_evolution_linear_constantetak_linear_d70_r35_stepsize0_00003_epochs1000000.png" alt="Linear network" style='width: 100%; max-width: 700px; height: auto; display: block; margin: 20px auto; border: 1px solid #ddd; border-radius: 5px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);'>
    <figcaption><em>Figure 2: Global convergence of GD for training nonlinear neural networks (top panel, tanh activation) and linear networks (bottom panel) under constant learning rate.</em></figcaption>
</figure>

### Limitations & Future Directions

The analysis is restricted to the square loss and fully connected architectures. Extension to cross-entropy loss, convolutional layers, and residual connections remains open. The companion work on SGD (SampTA 2023) begins to address stochastic variants by employing stochastic approximation and asymptotic pseudo-trajectory frameworks, establishing convergence guarantees for SGD in training linear neural networks with both constant and adaptive learning rate schedules.

---

## Links & Artifacts

- [Published Paper — Springer Nature (2024)](https://link.springer.com/content/pdf/10.1186/s13662-023-03797-x.pdf){:target="_blank"}
- [Preprint — arXiv 2108.02040](https://arxiv.org/abs/2108.02040){:target="_blank"}
- [Experiment Code — GitHub](https://github.com/mnguegnang/DNN_project/tree/master/GD_for_DNN){:target="_blank"}
- [Companion SGD Paper — SampTA 2023](/publication/SGD-conference-paper)

---
