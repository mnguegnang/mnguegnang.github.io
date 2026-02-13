---
title: "Analysis of Stochastic Gradient Descent for Learning Linear Neural Networks"
collection: publications
category: conferences
permalink: /publication/SGD-conference-paper
excerpt: 'This study is about stochastic gradient descent for training linear neural networks'
date: 2023-07-14
venue: 'SampTA 2023 Conference'
#paperurl:  'https://academicpages.github.io/files/paper3.pdf'
#citation: 'Your Name, You. (2024). &quot;Paper Title Number 3.&quot; <i>GitHub Journal of Bugs</i>. 1(3).'
---
Although state-of-the-art analyses of stochastic gradient descent (SGD) typically rely on the assumption of Lipschitz continuous gradients and their variants, these properties do not directly hold for neural network objective functions. This study addresses this limitation by employing the frameworks of stochastic approximation and asymptotic pseudo-trajectory, which combine discrete and continuous processes. The analysis directly examines SGD in the context of training linear neural networks, thereby closing the gap between theoretical understanding and practical behavior of SGD in non-convex objectives.

The discrete dynamics of SGD were analyzed to establish rigorous bounds that control its evolution, thereby mitigating the exploding gradient problem during training. Building on this stability analysis, appropriate learning rate schedules, including adaptive learning rate, were developed to mathematically guarantee the convergence of SGD for training linear neural networks. These results advance previous work on gradient-based methods for nonconvex optimization and practical implementations by offering a framework for understanding and optimizing neural network training. This framework has the potential to yield more robust algorithms and enhance the efficiency of machine learning models across diverse applications

