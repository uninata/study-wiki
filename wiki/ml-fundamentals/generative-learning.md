# Generative Learning

**Summary**: Generative learning models the joint distribution $p(\mathbf{x},y)$ and derives classifiers via Bayes' rule. This page covers Maximum Likelihood Estimation, Fisher information, Cramér-Rao bound, and the plug-in Bayes classifier.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_genlearn.pdf, ls26_seminar_genlearn.pdf, understanding-machine-learning-theory-algorithms.pdf

**Last updated**: 2026-05-16

---

## Generative vs. Discriminative

| Approach | Models | Examples |
|----------|--------|----------|
| **Generative** | $p(\mathbf{x}, y) = p(y)\, p(\mathbf{x}|y)$ | Naive Bayes, Gaussian mixture, HMM |
| **Discriminative** | $p(y|\mathbf{x})$ directly, or $h(\mathbf{x})$ directly | Logistic regression, SVM, neural nets |

Generative models are more informative (can generate synthetic data, handle missing features) but require stronger assumptions. If the model is correct, they can be more sample-efficient.

The textbook frames this as **parametric density estimation**: instead of directly learning a predictor, a generative approach assumes a parametric form for the data distribution and estimates its parameters (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). This can be useful when the model itself is needed for interpretation, missing-data handling, later downstream tasks, or computationally simpler estimation, but it can be harder than directly optimizing prediction accuracy (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Maximum Likelihood Estimation (MLE)

Given i.i.d. data $T_m$ from a parametric model $p(\mathbf{x}, y; \theta)$, the MLE is:

$$\hat{\theta} = \arg\max_{\theta} L(T_m, \theta) = \arg\max_{\theta} \sum_i \log p(\mathbf{x}_i, y_i; \theta)$$

MLE is ERM with the **log-loss**:

$$\ell(\theta,\mathbf{x}) = -\log p_{\theta}(\mathbf{x})$$

so maximizing likelihood is equivalent to minimizing empirical negative log-likelihood (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). If the true distribution $p$ is outside the assumed model family, the best parameter is the one closest in relative entropy/KL divergence, so model misspecification creates irreducible approximation error even with unlimited data (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

### Gaussian mixture model (seminar worked example)

Model: $p(x, y; \theta) = p(y) \cdot \frac{1}{\sqrt{2\pi\sigma}} \exp\!\left(-\frac{(x - \mu_y)^2}{2\sigma^2}\right)$

with shared variance $\sigma^2$ across classes (source: ml-fundamentals/ls26_seminar_genlearn.pdf).

**MLE solutions**:

| Parameter | MLE estimate | Derivation |
|-----------|-------------|-----------|
| Prior $p(y)$ | $\hat{p}(y) = n_y / m$ | Lagrange multiplier on $\sum p(y)=1$ |
| Class mean $\mu_y$ | $\hat{\mu}_y = \frac{1}{n_y} \sum_{i \in I_y} x_i$ | Set $\frac{\partial L}{\partial \mu_y} = 0$ per class |
| Common variance $\sigma^2$ | $\hat{\sigma}^2 = \frac{1}{m} \sum_i (x_i - \hat{\mu}_{y_i})^2$ | Set $\frac{\partial L}{\partial \sigma^2} = 0$ |

where $I_y = \{i : y_i = y\}$ and $n_y = |I_y|$.

## Plug-in Bayes Classifier

Replace unknown $p(y|\mathbf{x})$ with estimates from the fitted model (source: ml-fundamentals/ls26_seminar_genlearn.pdf):

$$\hat{h}(\mathbf{x}) = \arg\min_{\hat{y}} \sum_y \hat{p}(y|\mathbf{x})\, \ell(y, \hat{y})$$

where $\hat{p}(y|\mathbf{x}) = \frac{\hat{p}(y)\, \hat{p}(\mathbf{x}|y)}{\sum_k \hat{p}(k)\, \hat{p}(\mathbf{x}|k)}$

### For 0/1-loss (most common on exams):

$$\hat{h}(\mathbf{x}) = \arg\max_y \hat{p}(y|\mathbf{x}) = \arg\max_y \left[\log \hat{p}(y) - \frac{(x - \hat{\mu}_y)^2}{2\hat{\sigma}^2}\right]$$

The normalization constant cancels; only the log-prior and the Mahalanobis distance matter.

Two textbook examples show how generative assumptions reduce the number of parameters. Naive Bayes assumes conditional independence of features given the class, reducing a binary-feature conditional distribution from exponentially many parameters to $O(d)$ parameters (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). Linear Discriminant Analysis assumes class-conditionals are Gaussian with shared covariance, which makes the log-likelihood ratio linear in $\mathbf{x}$ and therefore yields a linear Bayes classifier (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Plug-in Conditional Risk (Prediction Uncertainty)

The conditional risk at point $\mathbf{x}$ quantifies how uncertain the prediction is (source: ml-fundamentals/ls26_seminar_genlearn.pdf):

$$\hat{r}(\mathbf{x}) = \min_{\hat{y}} \sum_y \hat{p}(y|\mathbf{x})\, \ell(\hat{y}, y)$$

For 0/1-loss: $\hat{r}(\mathbf{x}) = 1 - \max_y \hat{p}(y|\mathbf{x})$

- $\hat{r}(\mathbf{x}) = 0$ means the classifier is fully confident
- $\hat{r}(\mathbf{x})$ close to $1 - 1/|Y|$ means near-uniform uncertainty (essentially guessing)

## Fisher Information and Cramér-Rao Bound

For a single-parameter model $p(x; \theta)$ (source: ml-fundamentals/ls26_lecture_genlearn.pdf):

**Fisher information**: $I(\theta) = \mathbb{E}\!\left[\left(\frac{\partial}{\partial\theta} \log p(x; \theta)\right)^2\right] = -\mathbb{E}\!\left[\frac{\partial^2}{\partial\theta^2} \log p(x; \theta)\right]$

**Cramér-Rao lower bound**: For any unbiased estimator $\hat{\theta}$ of $\theta$:

$$\mathrm{Var}(\hat{\theta}) \geq \frac{1}{m \cdot I(\theta)}$$

The MLE is **asymptotically efficient**: as $m \to \infty$, it achieves the Cramér-Rao bound. Its asymptotic distribution is:

$$\hat{\theta}_{\text{MLE}} \sim \mathcal{N}\!\left(\theta,\; \frac{1}{m \cdot I(\theta)}\right)$$

### Exam relevance

Fisher information quantifies how much information each sample carries about $\theta$. Higher $I(\theta)$ means tighter confidence intervals and fewer samples needed. This connects to [[prediction-evaluation|prediction evaluation]].

## Connection to Approximation Error

The [[empirical-risk-minimization|approximation error]] is zero when the Bayes classifier lies in $\mathcal{H}$. For generative models with Gaussian class-conditionals:

- **Equal covariances**: Bayes classifier is linear → zero approximation error for linear $\mathcal{H}$
- **Unequal covariances**: Bayes classifier is quadratic → positive approximation error for linear $\mathcal{H}$

See [[empirical-risk-minimization#exam-tip-approximation-error-questions]] for the full analysis.

## Related pages

- [[ml-fundamentals/understanding-machine-learning]]
- [[shared/bayes-classifier]]
- [[empirical-risk-minimization]]
- [[prediction-evaluation]]
- [[linear-classifiers]]
- [[ml-fundamentals/bayesian-learning]]
- [[ml-fundamentals/unsupervised-learning]]
