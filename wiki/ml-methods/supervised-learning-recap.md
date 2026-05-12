# Supervised Learning Recap

**Summary**: Foundational supervised learning covering empirical risk minimization, bias-variance decomposition, cross-validation, regularization techniques (L1, L2, Elastic Net), and applications to linear/logistic regression.

**Course**: ml-methods

**Sources**: mlm_week_1.pdf

**Last updated**: 2026-04-16

---

## Empirical Risk Minimization (ERM)

The core principle: minimize training error (empirical risk) as a proxy for generalization (source: mlm_week_1.pdf).

- **True risk**: $R(h) = \mathbb{E}_{(x,y) \sim D}[\ell(h(x), y)]$ (expected loss on unseen data)
- **Empirical risk**: $\hat{R}(h) = \frac{1}{n} \sum_{i=1}^n \ell(h(x_i), y_i)$ (average loss on training set)
- Gap: $R(h) - \hat{R}(h)$ depends on sample size and model complexity

## Bias-Variance Decomposition

Any predictor's error decomposes into three irreducible parts (source: mlm_week_1.pdf):

- **Bayes error (irreducible)**: $\min_h R(h)$ — the best possible error
- **Bias**: $(R(h^*) - R(h_D))$ — underfitting; systematic error from wrong model class
- **Variance**: $\mathbb{E}[R(h_D) - \hat{R}(h_D)]$ — overfitting; sensitivity to training set fluctuations

Tradeoff: Simpler models have high bias, low variance. Complex models have low bias, high variance.

## Cross-Validation

Estimate generalization without a held-out test set (source: mlm_week_1.pdf):

- **k-fold**: Partition data into k folds; train on k−1, evaluate on the held-out fold, repeat k times
- **Leave-one-out**: k = n; computationally expensive but low variance estimate
- **Stratified**: Preserve class distribution in imbalanced data

## Regularization Techniques

Penalize model complexity to reduce overfitting (source: mlm_week_1.pdf):

### L1 Regularization (Lasso)
$$\min_w \frac{1}{n} \sum_{i=1}^n (y_i - w^T x_i)^2 + \lambda \|w\|_1$$

- Promotes sparsity: some weights → exactly 0
- Feature selection side-effect

### L2 Regularization (Ridge)
$$\min_w \frac{1}{n} \sum_{i=1}^n (y_i - w^T x_i)^2 + \lambda \|w\|_2^2$$

- Distributes weight evenly across features
- Numerically stable (no exact zeros)

### Elastic Net
$$\lambda_1 \|w\|_1 + \lambda_2 \|w\|_2^2$$

Combines L1 and L2: sparsity with grouped feature selection.

## Linear and Logistic Regression

### Linear Regression
- Model: $\hat{y} = w^T x + b$
- Loss: Mean squared error (MSE)
- Closed form: $w = (X^T X)^{-1} X^T y$

### Logistic Regression
- Model: $P(y=1|x) = \sigma(w^T x + b)$ where $\sigma(z) = \frac{1}{1 + e^{-z}}$
- Loss: Binary cross-entropy
- Solved iteratively (no closed form); gradient-based optimization

Both are foundation for [[ml-methods/decision-trees-ensembles]] and [[ml-methods/svm]].

## Learning Curves

Diagnostic plots of train/validation error vs. sample size reveal underfitting vs. overfitting (source: mlm_week_1.pdf):

- **Underfitting** (high bias): Both train and validation error plateau at high values
- **Overfitting** (high variance): Train error low, validation error high and declining slowly

## Feature Engineering

Domain-specific transformations to improve model fit (source: mlm_week_1.pdf):

- **Scaling**: Standardize or normalize features
- **Polynomial features**: Add $x^2, x^3$, etc. for non-linear relationships
- **Interaction terms**: Multiply features to capture synergies
- **Domain knowledge**: Custom transformations (e.g., log, binning)

---

## Related Pages

- [[ml-methods/course-overview]]
- [[ml-methods/decision-trees-ensembles]]
- [[ml-methods/svm]]
- [[shared/bayes-classifier]]
