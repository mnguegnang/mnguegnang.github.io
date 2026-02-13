---
title: "Convergence of gradient descent for learning linear neural networks"
collection: publications
category: manuscripts
permalink: /publication/2010-10-01-paper-title-number-2
excerpt: 'This paper examines the impact of gradient descent on the training of deep neural networks, with a particular emphasis on its convergence behavior in deep linear networks and the establishment of key convergence properties.'
date: 2024-07-18
venue: 'Springer Nature: Advances in Continuous and Discrete Models
Theory and Modern Applications'
#slidesurl: 'https://academicpages.github.io/files/slides2.pdf'
paperurl: '[https://academicpages.github.io/files/paper2.pdf](https://link.springer.com/content/pdf/10.1186/s13662-023-03797-x.pdf)'
#citation: 'Your Name, You. (2010). &quot;Paper Title Number 2.&quot; <i>Journal 1</i>. 1(2).'
---

The convergence properties of gradient descent (GD) in training deep linear neural networks, particularly in the context of deep matrix factorizations, are analyzed by extending previous studies of gradient flow. In contrast to earlier work, which frequently relied on restrictive assumptions such as deficiency-margin and boundedness, this study considers a wider range of initializations and data conditions, thereby underscoring the novelty of the approach.

This study establishes the boundedness of GD, identifies appropriate step-size conditions, and demonstrates the convergence of gradient descent to a critical point of the loss function, specifically the square loss. For almost all initializations, gradient descent attains a global minimum in two-layer networks. In networks with three or more layers, gradient descent converges to a global minimum on the manifold of matrices with a fixed, though initially undetermined, rank. These findings differ from previous analyses by introducing a more general step size schedule; notably, the maximum permissible step size does not decrease exponentially with network depth. Empirical results further indicate that exceeding the prescribed step size bounds can lead to training failure.

