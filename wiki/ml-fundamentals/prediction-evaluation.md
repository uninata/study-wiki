# Prediction Evaluation

**Summary**: After training a predictor, how do we estimate its true error? This page covers confidence intervals via Hoeffding's inequality, sample size requirements for different loss functions, and the train/test split methodology.

**Course**: ml-fundamentals

**Sources**: ls26-lecture-predeval.pdf, ls_seminar_predeval_sol.pdf, hw_assignment_confidence_interval_ws2025.pdf

**Last updated**: 2026-04-14

---

## The Evaluation Problem

Given a fixed predictor h: X -> Y and an i.i.d. test set S_n = {(x_i, y_i)}_{i=1}^n from p(x,y) (source: ml-fundamentals/ls26-lecture-predeval.pdf):

- **True risk**: R(p, h) = E_{(x,y)~p}[l(y, h(x))] — a fixed unknown scalar
- **Test error**: R_hat(S_n, h) = (1/n) sum_i l(y_i, h(x_i)) — a random variable (depends on S_n)

By the **Law of Large Numbers**, R_hat(S_n, h) -> R(p, h) as n -> infinity. The question is: how close are they for finite n?

**Key insight**: the losses z_i = l(y_i, h(x_i)) are themselves i.i.d. random variables bounded in [l_min, l_max]. The test error is their sample mean. This connects directly to Hoeffding's inequality.

## Hoeffding's Inequality

For i.i.d. random variables z_i in [a, b] with sample mean mu_hat_n = (1/n) sum z_i and true mean mu (source: ml-fundamentals/ls26-lecture-predeval.pdf):

**P(|mu_hat_n - mu| >= epsilon) <= 2 exp(-2n epsilon² / (b-a)²)**

Properties: (+) General — works for any bounded i.i.d. r.v.s. (+) Vanishing — bound -> 0 as n -> infinity. (+) Cheap to compute. (-) Conservative — typically not tight.

## Confidence Interval for True Risk

Applying Hoeffding to predictor evaluation (source: ml-fundamentals/ls26-lecture-predeval.pdf):

**P(|R_hat(S_n, h) - R(p, h)| >= epsilon) <= 2 exp(-2n epsilon² / (l_max - l_min)²)**

Setting the RHS = delta and solving:

### Given n and delta, compute epsilon (error margin):

**epsilon = (l_max - l_min) sqrt((1/2n) ln(2/delta))**

### Given epsilon and delta, compute n (minimum test size):

**n = ((l_max - l_min)² / (2 epsilon²)) ln(2/delta)**

## Loss-Specific Formulas

The key quantity is the **loss range** l_max - l_min (source: ml-fundamentals/ls_seminar_predeval_sol.pdf):

| Loss function | Range [l_min, l_max] | l_max - l_min | epsilon formula |
|--------------|---------------------|---------------|----------------|
| 0/1: l = [[y != y']] | [0, 1] | 1 | sqrt((1/2n) ln(2/delta)) |
| Absolute: l = \|y - y'\| with Y classes | [0, Y-1] | Y-1 | (Y-1) sqrt((1/2n) ln(2/delta)) |
| Threshold: l = [[\|y-y'\| >= K]] | [0, 1] | 1 | sqrt((1/2n) ln(2/delta)) |

### Seminar example: 0/1-loss confidence interval

For epsilon = 1% and 95% confidence (delta = 0.05) (source: ml-fundamentals/ls_seminar_predeval_sol.pdf):

n >= (1/(2 * 0.01²)) ln(2/0.05) = 5000 * ln(40) ≈ **18,445 test examples**

### Seminar example: Digit pair prediction

Loss l(y_A, y_B, y_hat_A, y_hat_B) = |y_A + y_B - y_hat_A - y_hat_B| with Y = {0,...,9}. The range is [0, 18], so l_max - l_min = 18 (source: ml-fundamentals/ls_seminar_predeval_sol.pdf).

For n = 10,000 and epsilon = 1:

P(|R_hat - R| >= 1) <= 2 exp(-20000/324) ≈ 3.1 * 10^{-27}

The confidence interval is essentially guaranteed — the bound is astronomically small.

## Homework: General Confidence Interval Implementation

The homework asks to implement a Python function that computes (R_L, R_R) such that R(p,h) in (R_L, R_R) with probability at least 1 - delta (source: ml-fundamentals/hw_assignment_confidence_interval_ws2025.pdf):

```
def confidence_interval(true_y, pred_y, loss, delta):
    # 1. Compute per-sample losses: z_i = loss[true_y[i], pred_y[i]]
    # 2. Compute empirical risk: R_hat = mean(z)
    # 3. Find l_min, l_max from the loss matrix
    # 4. Compute epsilon = (l_max - l_min) * sqrt(ln(2/delta) / (2n))
    # 5. Return (R_hat - epsilon, R_hat + epsilon)
```

The key insight: l_min and l_max come from the **loss matrix**, not from the observed losses. This ensures the bound holds for any distribution.

## Train/Test Split

Split available data into (source: ml-fundamentals/ls26-lecture-predeval.pdf):
- **Training data** (m examples): used to learn the predictor
- **Test data** (n examples): used only for evaluation

The test error on held-out data gives an unbiased estimate of the true risk, and Hoeffding's inequality quantifies its accuracy.

## Connection to Other Topics

- The same Hoeffding + union bound technique gives [[empirical-risk-minimization|ERM generalization bounds]] and [[pac-learning|PAC sample complexity]]
- The confidence interval formula here is for a **single fixed** predictor. The ERM bound requires the union bound over all h in H because the predictor is chosen based on the data
- [[generative-learning#fisher-information-and-cramér-rao-bound|Fisher information]] provides an alternative (asymptotic) approach to quantifying estimator uncertainty

## Related pages

- [[empirical-risk-minimization]]
- [[pac-learning]]
- [[generative-learning]]
