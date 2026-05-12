# Bayes Classifier

**Summary**: The Bayes classifier (or Bayes predictor) is the theoretically optimal predictor that minimizes the true risk. Its form depends on the loss function. It sets the baseline for approximation error analysis across multiple courses.

**Course**: ml-fundamentals (primary), pattern-recognition, ml-methods, stai

**Sources**: ls26_lecture_erm.pdf, ls26_seminar_intro_sol.pdf, ls26_seminar_erm_solution.pdf, pr_01_intro_and_bayes_2021.pdf

**Last updated**: 2026-05-03

---

## Definition

The **Bayes predictor** minimizes the true risk over all measurable functions (source: ml-fundamentals/ls26_lecture_erm.pdf):

$$h^*(x) = \arg\min_{\hat{y}} \sum_y p(y|x)\, \ell(y, \hat{y})$$

The **Bayes risk** $R(p, h^*)$ is the irreducible error — no predictor can do better.

## Bayes Predictor for Different Losses

| Loss function | Bayes predictor $h^*(x)$ | Name |
|--------------|----------------------|------|
| 0/1: $\ell = \mathbb{1}[y \neq \hat{y}]$ | $\arg\max_y p(y \mid x)$ | MAP rule |
| Squared: $\ell = (y - \hat{y})^2$ | $\mathbb{E}[y \mid x]$ | Conditional mean |
| Absolute: $\ell = \lvert y - \hat{y} \rvert$ | $\text{Median}(y \mid x)$ | Conditional median |
| Threshold: $\ell = \mathbb{1}[\lvert y - \hat{y} \rvert > K]$ | $\arg\max_{\hat{y}} \sum_{y:\, \lvert y - \hat{y} \rvert \leq K} p(y \mid x)$ | Mode of smoothed posterior |

(source: ml-fundamentals/ls26_seminar_intro_sol.pdf)

## Role in Error Decomposition

The risk of any learned hypothesis decomposes as (see [[ml-fundamentals/empirical-risk-minimization]]):

$$R(p, h_m) = \underbrace{R(p, h^*)}_{\text{Bayes risk}} + \underbrace{R(p, h_H) - R(p, h^*)}_{\text{approximation error}} + \underbrace{R(p, h_m) - R(p, h_H)}_{\text{estimation error}}$$

- $R(p, h^*)$ = Bayes risk (irreducible)
- $R(p, h_H) - R(p, h^*)$ = **approximation error** (is $h^*$ in $H$?)
- $R(p, h_m) - R(p, h_H)$ = estimation error (finite sample)

## Approximation Error Analysis

A key exam pattern: "Is the approximation error zero for hypothesis class $H$?" This asks whether $h^*$ belongs to $H$.

### For linear $H$ (source: ml-fundamentals/ls26_seminar_erm_solution.pdf):

| Data model | Bayes boundary | Approx. error for linear $H$ |
|-----------|---------------|--------------------------|
| Naive Bayes (binary features) | Linear (log-odds is linear in $x$) | **Zero** |
| Gaussian, equal covariances $\mathbf{C}_+ = \mathbf{C}_-$ | Linear | **Zero** |
| Gaussian, unequal covariances $\mathbf{C}_+ \neq \mathbf{C}_-$ | Quadratic (conic) | **Positive** |

### Discriminant function approach

For binary Gaussian class-conditionals, the log-odds $f(x) = \log\frac{p(x, y=+1)}{p(x, y=-1)}$ determines the Bayes boundary (source: ml-fundamentals/ls26_seminar_intro_sol.pdf):

$$f(x) = ax^2 + bx + c$$

- **Equal variances**: $a = 0$, so the boundary is a threshold (linear)
- **Equal means, different variances**: the boundary is quadratic — can have 0, 1, or 2 thresholds depending on priors

## Plug-in Bayes Classifier

When the true $p(y|x)$ is unknown, replace it with an estimate (source: ml-fundamentals/ls26_lecture_erm.pdf):

$$\hat{h}(x) = \arg\min_{\hat{y}} \sum_y \hat{p}(y|x)\, \ell(y, \hat{y})$$

This is used in:
- [[ml-fundamentals/generative-learning]]: estimate $p(y|x)$ via MLE of $p(x,y)$ and Bayes' rule
- [[ml-fundamentals/linear-classifiers#logistic-regression]]: model $p(y|x)$ directly as a logistic function

## Related pages

- [[ml-fundamentals/empirical-risk-minimization]]
- [[ml-fundamentals/generative-learning]]
- [[ml-fundamentals/linear-classifiers]]
- [[ml-fundamentals/prediction-evaluation]]
- [[pattern-recognition/bayesian-decision-theory]]
- [[pattern-recognition/parameter-estimation]]
- [[ml-fundamentals/bayesian-learning]]
