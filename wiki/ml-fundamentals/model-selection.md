# Model Selection

**Summary**: Choosing the best model (hyperparameters, architecture) from data. Covers Structural Risk Minimization (impractical), hold-out validation, K-fold cross-validation with bias-variance tradeoff, nested CV for honest evaluation, and confidence intervals including the Nadeau-Bengio correction for correlated folds.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_model_selection.pdf

**Last updated**: 2026-05-12

---

## The Problem

Given a set of candidate models $\mathcal{H}_1, \mathcal{H}_2, \ldots$, select the one that minimizes the true risk $R(p, h)$. We cannot compute $R(p, h)$ directly — must estimate it from data (source: ml-fundamentals/ls26_lecture_model_selection.pdf).

## Structural Risk Minimization (SRM)

The theoretical solution from [[ml-fundamentals/vc-dimension|VC theory]]: minimize the generalization bound (source: ml-fundamentals/ls26_lecture_model_selection.pdf):

$$h^* = \arg\min_{h \in \bigcup_k \mathcal{H}_k} \left[\hat{R}(\mathcal{T}_m, h) + \sqrt{\frac{\text{VC}(\mathcal{H}_k) \cdot \log(2m / \text{VC}(\mathcal{H}_k)) + \log(4/\delta)}{m}}\right]$$

**Impractical** because:
- VC dimension is hard to compute for most models (especially neural networks)
- The bound is typically very loose
- Not used in practice — but motivates the bias-variance tradeoff

## Hold-Out Validation

Split data into training / validation / test (e.g., 60/20/20) (source: ml-fundamentals/ls26_lecture_model_selection.pdf):

1. Train each candidate on the training set
2. Select the best on the validation set
3. Report final performance on the test set (used only once)

**Pros**: Simple, fast, test set gives unbiased estimate  
**Cons**: Wastes data (especially problematic for small datasets), high variance from a single random split

## K-Fold Cross-Validation

Partition data into $K$ folds; iterate: train on $K-1$ folds, validate on the held-out fold (source: ml-fundamentals/ls26_lecture_model_selection.pdf):

$$\hat{R}_{\text{CV}} = \frac{1}{K} \sum_{k=1}^K \hat{R}(\mathcal{T}_k^{\text{val}}, h_k)$$

### Bias-Variance Tradeoff in $K$

| $K$ | Training set size | Bias | Variance | Computation |
|-----|------------------|------|----------|-------------|
| Small (e.g., 2) | $m/2$ | High (trains on too little data) | Low | Cheap |
| Large (e.g., $m$ = LOO) | $m-1$ | Low (nearly full data) | High (folds highly correlated) | Expensive |
| Typical (5 or 10) | $0.8m$–$0.9m$ | Moderate | Moderate | Moderate |

**Leave-one-out (LOO)**: $K = m$ — nearly unbiased but high variance because the $m$ training sets differ by only one example, making the estimates highly correlated.

## Nested Cross-Validation

For simultaneously selecting a model *and* estimating its performance honestly (source: ml-fundamentals/ls26_lecture_model_selection.pdf):

- **Outer loop** ($K_{\text{out}}$ folds): estimates generalization performance
- **Inner loop** ($K_{\text{in}}$ folds on each outer training set): selects best hyperparameters

```
for each outer fold k:
    for each candidate model m:
        run inner K-fold CV on outer_train[k] → score[m]
    best_model[k] = argmax(score)
    train best_model[k] on outer_train[k]
    evaluate on outer_test[k] → performance[k]
final_estimate = mean(performance)
```

This avoids the optimistic bias that occurs when the same data is used for both selection and evaluation.

## Confidence Intervals

### For Hold-Out Test Set

With $n$ test examples and 0/1 loss, the test accuracy $\hat{p}$ has a confidence interval (source: ml-fundamentals/ls26_lecture_model_selection.pdf):

$$\hat{p} \pm t_{\alpha/2, n-1} \sqrt{\frac{\hat{p}(1 - \hat{p})}{n}}$$

using Student's $t$-distribution with $n-1$ degrees of freedom (approximates normal for large $n$).

### Problem with CV Confidence Intervals

Standard CI formulas assume **independent** observations. In K-fold CV, the $K$ estimates are **correlated** because the training sets overlap heavily (share $(K-2)/(K-1)$ of the data). Naive CIs are therefore too narrow — they underestimate uncertainty.

### Nadeau-Bengio Correction

Corrected variance estimate for K-fold CV (source: ml-fundamentals/ls26_lecture_model_selection.pdf):

$$\hat{\sigma}_{\text{corrected}}^2 = \hat{\sigma}^2 \cdot \left(\frac{1}{K} + \frac{1}{K - 1}\right)$$

where $\hat{\sigma}^2$ is the sample variance of the $K$ fold errors. The correction factor $\frac{1}{K} + \frac{1}{K-1}$ accounts for the correlation between folds due to overlapping training sets.

The corrected CI:

$$\hat{R}_{\text{CV}} \pm t_{\alpha/2, K-1} \cdot \hat{\sigma}_{\text{corrected}}$$

## Related pages

- [[ml-fundamentals/vc-dimension]]
- [[ml-fundamentals/empirical-risk-minimization]]
- [[ml-fundamentals/prediction-evaluation]]
- [[ml-fundamentals/bayesian-learning]]
- [[ml-fundamentals/deep-learning-generalization]]
