# Prediction Evaluation

**Summary**: After training a predictor, how do we estimate its true error? This page covers confidence intervals via Hoeffding's inequality, sample size requirements for different loss functions, and the train/test split methodology.

**Course**: ml-fundamentals

**Sources**: ls26-lecture-predeval.pdf, ls_seminar_predeval_sol.pdf, hw_assignment_confidence_interval_ws2025.pdf, understanding-machine-learning-theory-algorithms.pdf

**Last updated**: 2026-05-16

---

## The Evaluation Problem

Given a fixed predictor $h: \mathcal{X} \to \mathcal{Y}$ and an i.i.d. test set $\mathcal{S}_n = \{(x_i, y_i)\}_{i=1}^n$ from $p(x,y)$ (source: ml-fundamentals/ls26-lecture-predeval.pdf):

- **True risk**: $R(p, h) = \mathbb{E}_{(x,y)\sim p}[\ell(y, h(x))]$ — a fixed unknown scalar
- **Test error**: $\hat{R}(\mathcal{S}_n, h) = \frac{1}{n}\sum_i \ell(y_i, h(x_i))$ — a random variable that depends on $\mathcal{S}_n$

By the **Law of Large Numbers**, $\hat{R}(\mathcal{S}_n, h) \to R(p, h)$ as $n \to \infty$. The question is: how close are they for finite $n$?

**Key insight**: the losses $z_i = \ell(y_i, h(x_i))$ are themselves i.i.d. random variables bounded in $[\ell_{\min}, \ell_{\max}]$. The test error is their sample mean. This connects directly to Hoeffding's inequality.

The textbook stresses the independence condition: a fresh validation/test set gives a tight estimate for a fixed trained predictor because the examples were not used to construct that predictor (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). If the same held-out set is repeatedly used for model selection, it effectively becomes part of training and can be overfit (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Hoeffding's Inequality

For i.i.d. random variables $z_i \in [a, b]$ with sample mean $\hat{\mu}_n = \frac{1}{n}\sum_i z_i$ and true mean $\mu$ (source: ml-fundamentals/ls26-lecture-predeval.pdf):

$$\mathbb{P}(|\hat{\mu}_n - \mu| \geq \varepsilon) \leq 2\exp\left(-\frac{2n\varepsilon^2}{(b-a)^2}\right)$$

Properties: general for any bounded i.i.d. random variables, vanishes as $n \to \infty$, cheap to compute, and conservative in many practical settings.

## Confidence Interval for True Risk

Applying Hoeffding to predictor evaluation (source: ml-fundamentals/ls26-lecture-predeval.pdf):

$$\mathbb{P}(|\hat{R}(\mathcal{S}_n, h) - R(p, h)| \geq \varepsilon) \leq 2\exp\left(-\frac{2n\varepsilon^2}{(\ell_{\max} - \ell_{\min})^2}\right)$$

Setting the RHS equal to $\delta$ and solving:

### Given $n$ and $\delta$, compute $\varepsilon$ (error margin):

$$\varepsilon = (\ell_{\max} - \ell_{\min})\sqrt{\frac{1}{2n}\ln\left(\frac{2}{\delta}\right)}$$

### Given $\varepsilon$ and $\delta$, compute $n$ (minimum test size):

$$n = \frac{(\ell_{\max} - \ell_{\min})^2}{2\varepsilon^2}\ln\left(\frac{2}{\delta}\right)$$

For a single fixed predictor and bounded loss in $[0,1]$, this bound does not depend on the size or VC dimension of the hypothesis class that produced $h$; the price is that the test examples must be independent of the training/model-selection process (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Loss-Specific Formulas

The key quantity is the **loss range** $\ell_{\max} - \ell_{\min}$ (source: ml-fundamentals/ls_seminar_predeval_sol.pdf):

| Loss function | Range $[\ell_{\min}, \ell_{\max}]$ | $\ell_{\max} - \ell_{\min}$ | $\varepsilon$ formula |
|--------------|---------------------|---------------|----------------|
| 0/1: $\ell = \mathbb{1}[y \neq y']$ | $[0, 1]$ | $1$ | $\sqrt{\frac{1}{2n}\ln\left(\frac{2}{\delta}\right)}$ |
| Absolute: $\ell = \|y - y'\|$ with $Y$ classes | $[0, Y-1]$ | $Y-1$ | $(Y-1)\sqrt{\frac{1}{2n}\ln\left(\frac{2}{\delta}\right)}$ |
| Threshold: $\ell = \mathbb{1}[\|y-y'\| \geq K]$ | $[0, 1]$ | $1$ | $\sqrt{\frac{1}{2n}\ln\left(\frac{2}{\delta}\right)}$ |

### Seminar example: 0/1-loss confidence interval

For $\varepsilon = 1\%$ and 95% confidence ($\delta = 0.05$) (source: ml-fundamentals/ls_seminar_predeval_sol.pdf):

$$n \geq \frac{1}{2 \cdot 0.01^2}\ln\left(\frac{2}{0.05}\right) = 5000 \ln(40) \approx 18{,}445 \text{ test examples}$$

### Seminar example: Digit pair prediction

Loss $\ell(y_A, y_B, \hat{y}_A, \hat{y}_B) = |y_A + y_B - \hat{y}_A - \hat{y}_B|$ with $\mathcal{Y} = \{0,\ldots,9\}$. The range is $[0, 18]$, so $\ell_{\max} - \ell_{\min} = 18$ (source: ml-fundamentals/ls_seminar_predeval_sol.pdf).

For $n = 10{,}000$ and $\varepsilon = 1$:

$$\mathbb{P}(|\hat{R} - R| \geq 1) \leq 2\exp\left(-\frac{20000}{324}\right) \approx 3.1 \times 10^{-27}$$

The confidence interval is essentially guaranteed — the bound is astronomically small.

## Homework: General Confidence Interval Implementation

The homework asks to implement a Python function that computes $(R_L, R_R)$ such that $R(p,h) \in (R_L, R_R)$ with probability at least $1 - \delta$ (source: ml-fundamentals/hw_assignment_confidence_interval_ws2025.pdf):

```
def confidence_interval(true_y, pred_y, loss, delta):
    # 1. Compute per-sample losses: z_i = loss[true_y[i], pred_y[i]]
    # 2. Compute empirical risk: R_hat = mean(z)
    # 3. Find l_min, l_max from the loss matrix
    # 4. Compute epsilon = (l_max - l_min) * sqrt(ln(2/delta) / (2n))
    # 5. Return (R_hat - epsilon, R_hat + epsilon)
```

The key insight: $\ell_{\min}$ and $\ell_{\max}$ come from the **loss matrix**, not from the observed losses. This ensures the bound holds for any distribution.

## Train/Test Split

Split available data into (source: ml-fundamentals/ls26-lecture-predeval.pdf):
- **Training data** (m examples): used to learn the predictor
- **Test data** (n examples): used only for evaluation

The test error on held-out data gives an unbiased estimate of the true risk, and Hoeffding's inequality quantifies its accuracy.

## Connection to Other Topics

- The same Hoeffding + union bound technique gives [[empirical-risk-minimization|ERM generalization bounds]] and [[pac-learning|PAC sample complexity]]
- The confidence interval formula here is for a **single fixed** predictor. The ERM bound requires the union bound over all $h \in \mathcal{H}$ because the predictor is chosen based on the data
- [[generative-learning#fisher-information-and-cramér-rao-bound|Fisher information]] provides an alternative (asymptotic) approach to quantifying estimator uncertainty

## Related pages

- [[ml-fundamentals/understanding-machine-learning]]
- [[empirical-risk-minimization]]
- [[pac-learning]]
- [[generative-learning]]
