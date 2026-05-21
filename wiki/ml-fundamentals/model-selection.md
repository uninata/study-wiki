# Model Selection

**Summary**: Choosing the best model (hyperparameters, architecture) from data. Covers Structural Risk Minimization (impractical), hold-out validation, K-fold cross-validation with bias-variance tradeoff, nested CV for honest evaluation, and confidence intervals including the Nadeau-Bengio correction for correlated folds.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_model_selection.pdf, understanding-machine-learning-theory-algorithms.pdf

**Last updated**: 2026-05-16

---

## The Problem

Given a set of candidate models $\mathcal{H}_1, \mathcal{H}_2, \ldots$, select the one that minimizes the true risk $R(p, h)$. We cannot compute $R(p, h)$ directly — must estimate it from data (source: ml-fundamentals/ls26_lecture_model_selection.pdf).

Model selection is the practical face of the bias-complexity tradeoff: a low-complexity model can underfit because its approximation error is too high, while a high-complexity model can overfit because its estimation error is too high (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Structural Risk Minimization (SRM)

The theoretical solution from [[ml-fundamentals/vc-dimension|VC theory]]: minimize the generalization bound (source: ml-fundamentals/ls26_lecture_model_selection.pdf):

$$h^* = \arg\min_{h \in \bigcup_k \mathcal{H}_k} \left[\hat{R}(\mathcal{T}_m, h) + \sqrt{\frac{\text{VC}(\mathcal{H}_k) \cdot \log(2m / \text{VC}(\mathcal{H}_k)) + \log(4/\delta)}{m}}\right]$$

**Impractical** because:
- VC dimension is hard to compute for most models (especially neural networks)
- The bound is typically very loose
- Not used in practice — but motivates the bias-variance tradeoff

The textbook presents SRM as a bound-minimization rule over nested classes $\mathcal{H}_1,\mathcal{H}_2,\ldots$, where the selected model minimizes empirical risk plus a complexity penalty that increases with the class index (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). This is useful conceptually even when the numerical bound is too pessimistic for applied tuning.

## Hold-Out Validation

Split data into training / validation / test (e.g., 60/20/20) (source: ml-fundamentals/ls26_lecture_model_selection.pdf):

1. Train each candidate on the training set
2. Select the best on the validation set
3. Report final performance on the test set (used only once)

**Pros**: Simple, fast, test set gives unbiased estimate  
**Cons**: Wastes data (especially problematic for small datasets), high variance from a single random split

The key reason validation works is independence: once $h$ has been trained without using the validation set, Hoeffding's inequality bounds $|R(p,h)-\hat{R}_{\mathrm{val}}(h)|$ without paying for the full complexity of the original hypothesis class (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). When choosing among $r$ trained candidates on the same validation set, the finite-class penalty becomes logarithmic in $r$, so trying too many configurations can itself overfit the validation set (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

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

After cross-validation chooses a hyperparameter, the final predictor is usually retrained on the full training set using that chosen value; the CV score is primarily a model-selection device, not a substitute for an untouched final test estimate (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

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

## Diagnosing Failed Learning

When validation or test performance is poor, the first question is whether the model is underfitting or overfitting (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

Large training error usually points to underfitting, optimization failure, poor features, or a hypothesis class with high approximation error; useful remedies include changing features, enlarging/changing $\mathcal{H}$, or checking whether the training algorithm actually found a good empirical minimizer (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

Small training error but large validation error points to overfitting or insufficient sample size for the chosen class; useful remedies include more data, stronger regularization, a smaller class, or a better inductive bias (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

Learning curves plot training and validation error as the training set size grows. If both curves remain high, the model is underfitting; if training error is low and validation error decreases with more data, the model is data-limited; if a persistent gap remains, the model may need stronger regularization or reduced complexity (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

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

- [[ml-fundamentals/understanding-machine-learning]]
- [[ml-fundamentals/vc-dimension]]
- [[ml-fundamentals/empirical-risk-minimization]]
- [[ml-fundamentals/prediction-evaluation]]
- [[ml-fundamentals/bayesian-learning]]
- [[ml-fundamentals/deep-learning-generalization]]
