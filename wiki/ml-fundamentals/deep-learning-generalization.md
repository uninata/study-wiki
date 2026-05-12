# Deep Learning and Generalization

**Summary**: Why overparameterized deep networks generalize despite classical theory predicting they shouldn't. Covers the double descent phenomenon, implicit regularization and implicit bias of gradient descent, and architectural inductive biases.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_dl.pdf

**Last updated**: 2026-05-12

---

## The Deep Learning Puzzle

Classical [[ml-fundamentals/vc-dimension|VC theory]] predicts that models with more parameters than training samples should overfit catastrophically. Yet deep networks with millions of parameters generalize well, even when they perfectly interpolate the training data (source: ml-fundamentals/ls26_lecture_dl.pdf).

## Neural Networks as Learned Features

A neural network decomposes into (source: ml-fundamentals/ls26_lecture_dl.pdf):

$$h(\mathbf{x}; \theta) = \mathbf{w}^\top \phi(\mathbf{x}; \theta_\phi)$$

where $\phi(\mathbf{x}; \theta_\phi)$ is a **learned feature map** (the hidden layers) and $\mathbf{w}$ is a linear classifier on top. This contrasts with kernel methods where the feature map is fixed.

**Universal approximation theorem**: A single hidden layer with enough neurons can approximate any continuous function — but says nothing about learnability or generalization.

## Double Descent

The test error follows a non-monotonic curve as model complexity increases (source: ml-fundamentals/ls26_lecture_dl.pdf):

1. **Under-parameterized regime**: classical U-shaped bias-variance tradeoff
2. **Interpolation threshold**: model has just enough capacity to fit training data perfectly — test error peaks (worst generalization)
3. **Over-parameterized regime**: test error *decreases* again as capacity grows further

$$\text{complexity} \to \underbrace{\text{decreasing error}}_{\text{classical}} \to \underbrace{\text{peak at interpolation}}_{\text{threshold}} \to \underbrace{\text{decreasing error again}}_{\text{double descent}}$$

This contradicts the classical picture where more complexity always hurts past the optimum. The key insight: among all interpolating solutions, more capacity allows finding *smoother* ones.

## Implicit Regularization

Even without explicit regularization (no weight decay, no dropout), gradient-based optimization implicitly constrains the solution (source: ml-fundamentals/ls26_lecture_dl.pdf):

### Gradient Descent on Least Squares

For the underdetermined linear system $\mathbf{X}\mathbf{w} = \mathbf{y}$ (more parameters than data), GD initialized at $\mathbf{w}_0 = \mathbf{0}$ converges to:

$$\mathbf{w}^* = \arg\min_{\mathbf{w}} \|\mathbf{w}\|_2 \quad \text{s.t.} \quad \mathbf{X}\mathbf{w} = \mathbf{y}$$

the **minimum-norm interpolator**. GD implicitly regularizes toward small $\ell_2$ norm without any explicit penalty.

### Implicit Bias for Classification

For logistic loss on linearly separable data, GD converges in direction to the **maximum-margin solution** (source: ml-fundamentals/ls26_lecture_dl.pdf):

$$\frac{\mathbf{w}_t}{\|\mathbf{w}_t\|} \to \frac{\mathbf{w}_{\text{SVM}}}{\|\mathbf{w}_{\text{SVM}}\|}$$

This means gradient descent on logistic regression implicitly finds the same solution as a hard-margin [[ml-fundamentals/svm|SVM]] — connecting optimization dynamics to margin maximization.

## Explicit vs Implicit Regularization

| Type | Mechanism | Example |
|------|-----------|---------|
| **Explicit** | Added to loss or architecture | Weight decay, dropout, data augmentation, early stopping |
| **Implicit** | Arises from optimization | GD → min-norm, SGD → flat minima, architecture → symmetry |

SGD has additional implicit regularization beyond GD: the noise from mini-batches biases toward **flat minima** (regions where the loss landscape has low curvature), which tend to generalize better.

## Architectural Inductive Bias

The network architecture encodes assumptions about the data (source: ml-fundamentals/ls26_lecture_dl.pdf):

| Architecture | Inductive bias | Symmetry |
|-------------|---------------|----------|
| [[shared/cnn-fundamentals\|CNN]] | Translation equivariance | Shared local filters |
| RNN | Sequential structure | Shared weights across time |
| [[shared/transformers\|Transformer]] | Permutation equivariance | Self-attention over all positions |
| MLP | None (universal) | — |

Stronger inductive bias = better sample efficiency when the assumption matches the data, worse performance when it doesn't.

## Related pages

- [[ml-fundamentals/vc-dimension]]
- [[ml-fundamentals/empirical-risk-minimization]]
- [[ml-fundamentals/svm]]
- [[shared/cnn-fundamentals]]
- [[shared/transformers]]
- [[ml-fundamentals/model-selection]]
