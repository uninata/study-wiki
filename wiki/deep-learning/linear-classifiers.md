# Linear Classifiers

**Summary**: Score functions, loss functions (hinge and softmax/cross-entropy), stochastic gradient descent, computational graphs, and automatic differentiation. Combines ML basics (bias-variance, regularization, cross-validation) with the linear classifier framework that forms the foundation for neural networks.

**Course**: deep-learning

**Sources**: 01_machinelearning101.pdf, 02_linearclassifier.pdf

**Last updated**: 2026-04-15

---

## ML Fundamentals Recap

### Supervised Learning Setup
Given training data {(x_i, y_i)}, learn a function f: X -> Y that generalizes to unseen data (source: deep-learning/01_machinelearning101.pdf).

### Bias-Variance Tradeoff
- **Bias**: error from overly simple models (underfitting)
- **Variance**: error from overly complex models (overfitting)
- **Total error** = bias^2 + variance + irreducible noise
- Low-capacity models have high bias, low variance; high-capacity models have the opposite

### Regularization
Techniques to reduce overfitting (source: deep-learning/01_machinelearning101.pdf):
- **L2 regularization** (weight decay): adds lambda * ||w||^2 to the loss
- **L1 regularization**: adds lambda * ||w||_1, promotes sparsity
- **Dropout**, **data augmentation**, **early stopping**

### Cross-Validation
k-fold CV: split data into k folds, train on k-1, validate on 1, rotate. Provides unbiased estimate of generalization error (source: deep-learning/01_machinelearning101.pdf).

## Score Function

A linear classifier computes (source: deep-learning/02_linearclassifier.pdf):

**f(x) = Wx + b**

where W is a weight matrix (K x D for K classes, D features) and b is a bias vector. Each row of W defines a template for one class.

Interpretation: each class score is a dot product between the input and a learned template — the classifier finds the class whose template best matches the input.

## Loss Functions

### Hinge Loss (SVM / Multi-class SVM)
For correct class y_i with scores s = f(x_i) (source: deep-learning/02_linearclassifier.pdf):

**L_i = sum_{j != y_i} max(0, s_j - s_{y_i} + 1)**

- Penalizes when incorrect class score is within margin of correct class score
- The constant 1 is the margin (Delta)
- Connection to [[shared/svm]]: this is the multi-class hinge loss

### Softmax / Cross-Entropy Loss
Convert scores to probabilities via softmax, then apply cross-entropy (source: deep-learning/02_linearclassifier.pdf):

**p_k = exp(s_k) / sum_j exp(s_j)**

**L_i = -log(p_{y_i})**

- Probabilities sum to 1, all positive
- Minimizing cross-entropy = maximizing log-likelihood of correct class
- Connection to [[deep-learning/mle-kl-divergence]]: cross-entropy loss is equivalent to MLE under categorical distribution

### Comparison
| Property | Hinge Loss | Cross-Entropy |
|----------|-----------|---------------|
| Gradient | Sparse (only margin violators) | Dense (all classes) |
| Calibration | Scores not probabilities | Outputs calibrated probabilities |
| Saturation | Zero loss once margin satisfied | Never fully satisfied |

## Optimization

### Gradient Descent
Update rule (source: deep-learning/02_linearclassifier.pdf):

**w <- w - eta * dL/dw**

where eta is the learning rate.

### Stochastic Gradient Descent (SGD)
- **Batch GD**: compute gradient over all training data — accurate but slow
- **SGD**: compute gradient on a single sample — noisy but fast
- **Mini-batch SGD**: compute gradient on a batch of B samples — standard in practice (B = 32, 64, 128, 256)

### Momentum
Accumulate a velocity term (source: deep-learning/02_linearclassifier.pdf):

**v <- beta * v - eta * gradient**
**w <- w + v**

Smooths noisy gradients and accelerates convergence through flat regions. Typical beta = 0.9.

### Learning Rate Schedules
- **Step decay**: reduce LR by factor every N epochs
- **Cosine annealing**: smooth decay following cosine curve
- See [[deep-learning/optimization]] for full treatment

## Computational Graphs and Automatic Differentiation

### Computational Graphs
Any function can be decomposed into a directed acyclic graph of elementary operations. Each node computes a simple function; edges represent data flow (source: deep-learning/02_linearclassifier.pdf).

### Forward Pass
Compute the output by evaluating nodes in topological order.

### Backward Pass (Backpropagation)
Apply the chain rule in reverse topological order (source: deep-learning/02_linearclassifier.pdf):
1. Each node stores its local gradient (partial derivative of output w.r.t. input)
2. Multiply upstream gradient by local gradient
3. Propagate to all inputs

This is **automatic differentiation** (reverse mode) — computes all gradients in O(n_operations) time. Frameworks like PyTorch build computational graphs dynamically and compute gradients automatically.

### Gradient Patterns
- **Add gate**: distributes gradient equally to both inputs
- **Multiply gate**: swaps and scales (gradient of one input = upstream * other input)
- **Max gate**: routes gradient to the larger input only

## Related pages

- [[deep-learning/neural-networks]]
- [[deep-learning/mle-kl-divergence]]
- [[deep-learning/optimization]]
- [[shared/svm]]
- [[ml-fundamentals/linear-classifiers]]
- [[pattern-recognition/logistic-regression]]
