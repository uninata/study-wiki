# Logistic Regression

**Summary**: Discriminative linear classifier that models posterior probabilities directly using the sigmoid (binary) or softmax (multiclass) function. Trained by minimizing cross-entropy via gradient descent or IRLS.

**Course**: pattern-recognition

**Sources**: pr_05_logistic_regression_2022.pdf

**Last updated**: 2026-04-15

---

## Binary Logistic Regression

The model for p(y=1|x) is (source: pattern-recognition/pr_05_logistic_regression_2022.pdf):

**p(y=1|x) = sigma(w^T x) = 1 / (1 + exp(-w^T x))**

where sigma is the **sigmoid function**. The decision boundary is linear: w^T x = 0.

### Properties of the Sigmoid
- sigma(-z) = 1 - sigma(z)
- sigma'(z) = sigma(z)(1 - sigma(z))
- Maps R -> (0, 1), giving calibrated probabilities

## Cross-Entropy Loss

The negative log-likelihood for binary classification (source: pattern-recognition/pr_05_logistic_regression_2022.pdf):

**E(w) = -sum_i [y_i log p_i + (1-y_i) log(1-p_i)]**

where p_i = sigma(w^T x_i). This is a **convex** function of w — guaranteed to have a unique global minimum.

## Optimization

### Gradient Descent
The gradient is (source: pattern-recognition/pr_05_logistic_regression_2022.pdf):

**nabla E = sum_i (p_i - y_i) x_i = X^T (p - y)**

Update: w <- w - eta * nabla E

### Newton's Method / IRLS
The Hessian is H = X^T S X where S = diag(p_i(1-p_i)). Newton's update (source: pattern-recognition/pr_05_logistic_regression_2022.pdf):

**w <- w - H^{-1} nabla E**

This is equivalent to **Iteratively Reweighted Least Squares (IRLS)**: at each step, solve a weighted least squares problem with weights S. Converges quadratically near the optimum.

## Multiclass: Softmax Regression

For K classes, the conditional probability of class k is (source: pattern-recognition/pr_05_logistic_regression_2022.pdf):

**p(k|x) = exp(w_k^T x) / sum_j exp(w_j^T x)**

The parameters W = [w_1, ..., w_K] are found by maximizing the conditional log-likelihood:

**l'(W) = sum_{(x,k) in T} w_k^T x - sum_{(x,k) in T} log(sum_j exp(w_j^T x))**

Decision boundaries remain **linear** (piecewise between each pair of classes).

## L2 Regularization

Add a penalty lambda ||w||^2 to the cross-entropy loss (source: pattern-recognition/pr_05_logistic_regression_2022.pdf):

**E_reg(w) = E(w) + lambda ||w||^2**

This is equivalent to MAP estimation with a Gaussian prior on w. The regularization path shows how coefficients shrink toward zero as lambda increases.

## Bayesian Logistic Regression

Instead of a point estimate, place a Gaussian prior on w and approximate the posterior (source: pattern-recognition/pr_05_logistic_regression_2022.pdf):

1. Find the MAP estimate w_MAP
2. Approximate the posterior as Gaussian using **Laplace approximation**: p(w|D) ≈ N(w_MAP, H^{-1}) where H is the Hessian at w_MAP
3. The predictive distribution integrates over this approximate posterior

This gives uncertainty estimates on predictions — the predictive probabilities are less extreme than point-estimate predictions, especially far from training data.

## Discriminative vs Generative

Logistic regression is **discriminative** — it models p(y|x) directly. Contrast with generative approaches ([[pattern-recognition/parameter-estimation]], [[ml-fundamentals/generative-learning]]) that model p(x|y)p(y) and derive p(y|x) via Bayes' rule (source: pattern-recognition/pr_05_logistic_regression_2022.pdf):

- **Generative**: can generate synthetic data, works with missing features, uses more assumptions
- **Discriminative**: often better classification accuracy, fewer parameters, directly optimizes the quantity of interest

## Related pages

- [[pattern-recognition/perceptron]]
- [[pattern-recognition/svm]]
- [[pattern-recognition/neural-networks]]
- [[pattern-recognition/bayesian-decision-theory]]
- [[ml-fundamentals/linear-classifiers]]
- [[shared/bayes-classifier]]
