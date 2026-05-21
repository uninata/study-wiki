# Empirical Risk Minimization (ERM)

**Summary**: The foundational principle of statistical learning: choose the hypothesis that minimizes the average loss on training data. ERM connects the true risk $R(p,h)$ to its empirical estimate, but only generalizes when paired with an appropriate inductive bias and complexity control.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_erm.pdf, ls26_seminar_erm_solution.pdf, ls26_seminar_intro_sol.pdf, understanding-machine-learning-theory-algorithms.pdf

**Last updated**: 2026-05-16

---

## The Learning Problem

Given:
- Input space $\mathcal{X}$, label space $\mathcal{Y}$
- Unknown distribution $p(x, y)$
- Training sample $\mathcal{T}_m = \{(x_i, y_i)\}_{i=1}^m$ drawn i.i.d. from $p(x, y)$
- Loss function $\ell: \mathcal{Y} \times \mathcal{Y} \to \mathbb{R}_+$
- Hypothesis class $\mathcal{H}$

**True risk**: 
$$R(p, h) = \mathbb{E}_{(x,y)\sim p}[\ell(y, h(x))]$$

**Empirical risk**: 
$$\hat{R}(\mathcal{T}_m, h) = \frac{1}{m} \sum_{i=1}^{m} \ell(y_i, h(x_i))$$

**ERM principle**: 
$$h_m = \arg\min_{h \in \mathcal{H}} \hat{R}(\mathcal{T}_m, h)$$

## Why ERM Needs Inductive Bias

ERM is natural because the training set is the only snapshot of the unknown distribution available to the learner, but unrestricted ERM can overfit badly: a memorizing predictor can obtain zero empirical risk while having large true risk on unseen points (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

The fix is not to abandon ERM, but to restrict it to a hypothesis class $\mathcal{H}$ chosen before seeing the data. This restriction is the learner's **inductive bias**: it encodes prior assumptions about which predictors are plausible for the task (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). A smaller $\mathcal{H}$ protects against overfitting but can increase approximation error; a richer $\mathcal{H}$ can reduce approximation error but raises estimation error (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

The book's no-free-lunch result makes this point formal: no universal learner succeeds on all binary classification tasks without some prior restriction or assumption on the data-generating distribution (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Error Decomposition

The risk of the learned hypothesis decomposes as:

$$R(p, h_m) = R(p, h^*) + [R(p, h_\mathcal{H}) - R(p, h^*)] + [R(p, h_m) - R(p, h_\mathcal{H})]$$

where:
- **$R(p, h^*)$** = Bayes risk — the irreducible error of the best possible predictor (source: ml-fundamentals/ls26_lecture_erm.pdf)
- **$R(p, h_\mathcal{H}) - R(p, h^*)$** = **approximation error** — how much we lose by restricting to $\mathcal{H}$. Depends on the expressiveness of $\mathcal{H}$, not on data.
- **$R(p, h_m) - R(p, h_\mathcal{H})$** = **estimation error** — how much we lose because we only have $m$ samples. Decreases as $m \to \infty$.

The textbook frames the same split as a **bias-complexity tradeoff**: approximation error measures the quality of the prior knowledge encoded by $\mathcal{H}$, while estimation error measures the risk of fitting sample noise due to the size or complexity of $\mathcal{H}$ (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

### Exam tip: Approximation error questions

A common exam pattern: "Is the approximation error zero?" This means: does the [[shared/bayes-classifier|Bayes classifier]] belong to $\mathcal{H}$?

**Seminar examples** (source: ml-fundamentals/ls26_seminar_erm_solution.pdf):
- **Naive Bayes with binary features** → log-odds is linear in $x$ → Bayes classifier is linear → **approximation error $= 0$** for linear $\mathcal{H}$
- **Gaussian class-conditionals, equal covariances $\mathbf{C}_+ = \mathbf{C}_-$** → log-odds is linear → **approximation error $= 0$** for linear $\mathcal{H}$
- **Gaussian, unequal covariances $\mathbf{C}_+ \neq \mathbf{C}_-$** → log-odds contains $\mathbf{x}^\top(\mathbf{C}_+^{-1} - \mathbf{C}_-^{-1})\mathbf{x}$ quadratic term → Bayes boundary is a conic → **approximation error $> 0$** for linear $\mathcal{H}$

## Generalization Bound via Hoeffding + Union Bound

For a **finite** hypothesis class $\mathcal{H}$ with $|\mathcal{H}|$ hypotheses and $0/1$-loss:

$$\mathbb{P}\left(\max_{h \in \mathcal{H}} |\hat{R}(\mathcal{T}_m, h) - R(p, h)| \geq \varepsilon \right) \leq 2|\mathcal{H}| \exp(-2m\varepsilon^2)$$

Setting the RHS $= \delta$ and solving:

$$\varepsilon = \sqrt{\frac{1}{2m} \ln\left(\frac{2|\mathcal{H}|}{\delta}\right)}$$

$$m \geq \frac{1}{2\varepsilon^2} \ln\left(\frac{2|\mathcal{H}|}{\delta}\right)$$

This is the **uniform deviation bound** — it guarantees that the empirical risk uniformly approximates the true risk for all $h \in \mathcal{H}$ simultaneously.

### Seminar example: CNN model selection as ERM

Selecting the best among 100 CNN checkpoints on a validation set is ERM over a finite $\mathcal{H}$ with $|\mathcal{H}| = 100$. With 99% confidence ($\delta = 0.01$) and $\varepsilon = 0.01$:

$$m \geq \frac{1}{0.0002} \ln(20000) \approx 49,518 \text{ validation examples}$$

With $m = 20,000$ and 95% confidence ($\delta = 0.05$):

$$\varepsilon = \sqrt{\frac{1}{40000} \ln\left(\frac{200}{0.05}\right)} \approx 0.0144$$

(source: ml-fundamentals/ls26_seminar_erm_solution.pdf)

## The Bayes Predictor for Different Losses

The [[shared/bayes-classifier|Bayes predictor]] $h^*(x) = \arg\min_{\hat{y}} \sum_y p(y|x) \ell(y, \hat{y})$ depends on the loss function (source: ml-fundamentals/ls26_seminar_intro_sol.pdf):

| Loss | Bayes predictor |
|------|----------------|
| $0/1$: $\ell = \mathbb{1}[y \neq \hat{y}]$ | $h^*(x) = \arg\max_y p(y \mid x)$ — the MAP rule |
| Squared: $\ell = (y - \hat{y})^2$ | $h^*(x) = \mathbb{E}[y \mid x]$ — the conditional mean |
| Absolute: $\ell = \|y - \hat{y}\|$ | $h^*(x) = \text{Median}(y \mid x)$ — the conditional median |
| Threshold: $\ell = \mathbb{1}[\|y - \hat{y}\| > 5]$ | $h^*(x) = \arg\max_{\hat{y}} \sum_{y: \|y-\hat{y}\| \leq 5} p(y \mid x)$ |

## Discriminant Function Approach

For binary classification with Gaussian class-conditionals (source: ml-fundamentals/ls26_seminar_intro_sol.pdf):

$$f(x) = \log\left[\frac{p(x, y=+1)}{p(x, y=-1)}\right] = ax^2 + bx + c$$

where:
- $a = \frac{1}{2\sigma^2_{-}} - \frac{1}{2\sigma^2_{+}}$
- $b = \frac{\mu_{+}}{\sigma^2_{+}} - \frac{\mu_{-}}{\sigma^2_{-}}$
- $c = \frac{\mu^2_{-}}{2\sigma^2_{-}} - \frac{\mu^2_{+}}{2\sigma^2_{+}} + \log\left[\frac{p(+1)\sigma_{-}}{p(-1)\sigma_{+}}\right]$

**Equal variances ($\sigma_+ = \sigma_-$)**: $a = 0$, linear boundary $\Rightarrow$ threshold rule with $\theta = \frac{\mu_+ + \mu_-}{2} + \frac{\sigma^2}{\mu_- - \mu_+} \log\left[\frac{p(+1)}{p(-1)}\right]$

**Equal means, different variances ($\mu_+ = \mu_-, \sigma_+ \neq \sigma_-$)**: quadratic boundary $\Rightarrow$ can have 0, 1, or 2 thresholds depending on priors. It is even possible for the Bayes classifier to assign all inputs to a single class.

## Related pages

- [[ml-fundamentals/understanding-machine-learning]]
- [[shared/bayes-classifier]]
- [[ml-fundamentals/pac-learning]]
- [[ml-fundamentals/vc-dimension]]
- [[ml-fundamentals/prediction-evaluation]]
- [[ml-fundamentals/generative-learning]]
- [[ml-fundamentals/model-selection]]
- [[ml-fundamentals/deep-learning-generalization]]
- [[ml-fundamentals/bayesian-learning]]
