# Bayesian Learning

**Summary**: Bayesian learning treats model parameters as random variables with prior distributions, updating beliefs via Bayes' rule. Covers MLE vs MAP vs full Bayesian inference, conjugate priors, the linear-Gaussian model with closed-form posterior, and the predictive posterior with aleatoric-epistemic uncertainty decomposition.

**Course**: ml-fundamentals

**Sources**: ls25_lecture_bayesian.pdf

**Last updated**: 2026-05-12

---

## Frequentist vs Bayesian

| | Frequentist | Bayesian |
|--|-------------|----------|
| Parameters | Fixed unknown constants | Random variables with distributions |
| Inference | Point estimate ($\hat{\theta}$) | Posterior distribution $p(\theta \mid \mathcal{T}_m)$ |
| Prediction | $p(y \mid \mathbf{x}, \hat{\theta})$ | $p(y \mid \mathbf{x}, \mathcal{T}_m) = \int p(y \mid \mathbf{x}, \theta)\, p(\theta \mid \mathcal{T}_m)\, d\theta$ |

(source: ml-fundamentals/ls25_lecture_bayesian.pdf)

## Three Levels of Inference

### Maximum Likelihood Estimation (MLE)

$$\hat{\theta}_{\text{MLE}} = \arg\max_\theta p(\mathcal{T}_m \mid \theta) = \arg\max_\theta \sum_{i=1}^m \log p(y_i \mid \mathbf{x}_i, \theta)$$

- Uses only the data; no prior information
- Can overfit with limited data
- See [[ml-fundamentals/generative-learning]] for detailed MLE examples

### Maximum A Posteriori (MAP)

$$\hat{\theta}_{\text{MAP}} = \arg\max_\theta p(\theta \mid \mathcal{T}_m) = \arg\max_\theta \left[\sum_{i=1}^m \log p(y_i \mid \mathbf{x}_i, \theta) + \log p(\theta)\right]$$

- Incorporates prior $p(\theta)$ as a regularizer
- **Gaussian prior** $\theta \sim \mathcal{N}(\mathbf{0}, \sigma_\theta^2 \mathbf{I})$ → L2 penalty → **Ridge regression**
- **Laplace prior** $p(\theta_j) \propto \exp(-\lambda |\theta_j|)$ → L1 penalty → **Lasso**
- Still a point estimate — does not capture parameter uncertainty

(source: ml-fundamentals/ls25_lecture_bayesian.pdf)

### Full Bayesian Inference

Compute the full posterior over parameters:

$$p(\theta \mid \mathcal{T}_m) = \frac{p(\mathcal{T}_m \mid \theta)\, p(\theta)}{p(\mathcal{T}_m)}$$

where $p(\mathcal{T}_m) = \int p(\mathcal{T}_m \mid \theta)\, p(\theta)\, d\theta$ is the marginal likelihood (evidence).

- Captures full uncertainty over $\theta$
- Predictions integrate over all plausible parameters
- Usually intractable — requires conjugate priors or approximations

## Conjugate Priors

A prior $p(\theta)$ is **conjugate** to a likelihood $p(x \mid \theta)$ if the posterior $p(\theta \mid x)$ belongs to the same family as the prior (source: ml-fundamentals/ls25_lecture_bayesian.pdf):

| Likelihood | Conjugate prior | Posterior |
|-----------|----------------|-----------|
| Gaussian (known $\sigma^2$) | Gaussian | Gaussian |
| Bernoulli | Beta | Beta |
| Multinomial | Dirichlet | Dirichlet |
| Poisson | Gamma | Gamma |
| Gaussian (unknown $\sigma^2$) | Inverse-Gamma | Inverse-Gamma |

Conjugate priors enable **closed-form** posterior updates, avoiding costly integration.

## Linear-Gaussian Model

The canonical example of tractable Bayesian inference (source: ml-fundamentals/ls25_lecture_bayesian.pdf):

**Model**: $y = \mathbf{w}^\top \mathbf{x} + \varepsilon$, where $\varepsilon \sim \mathcal{N}(0, \sigma^2)$

**Prior**: $\mathbf{w} \sim \mathcal{N}(\mathbf{0}, \sigma_w^2 \mathbf{I})$

### Posterior (closed form)

$$p(\mathbf{w} \mid \mathcal{T}_m) = \mathcal{N}(\boldsymbol{\mu}_w, \boldsymbol{\Sigma}_w)$$

where:

$$\boldsymbol{\Sigma}_w = \left(\frac{1}{\sigma^2} \mathbf{X}^\top \mathbf{X} + \frac{1}{\sigma_w^2} \mathbf{I}\right)^{-1}$$

$$\boldsymbol{\mu}_w = \frac{1}{\sigma^2} \boldsymbol{\Sigma}_w \mathbf{X}^\top \mathbf{y}$$

Note: The MAP estimate $\hat{\mathbf{w}}_{\text{MAP}} = \boldsymbol{\mu}_w$ coincides with the Ridge regression solution with $\lambda = \sigma^2 / \sigma_w^2$.

## Predictive Posterior

For a new input $\mathbf{x}_*$, integrate over all parameter values (source: ml-fundamentals/ls25_lecture_bayesian.pdf):

$$p(y_* \mid \mathbf{x}_*, \mathcal{T}_m) = \int p(y_* \mid \mathbf{x}_*, \mathbf{w})\, p(\mathbf{w} \mid \mathcal{T}_m)\, d\mathbf{w} = \mathcal{N}(\boldsymbol{\mu}_w^\top \mathbf{x}_*, \sigma_*^2)$$

### Uncertainty Decomposition

$$\sigma_*^2 = \underbrace{\sigma^2}_{\text{aleatoric}} + \underbrace{\mathbf{x}_*^\top \boldsymbol{\Sigma}_w \mathbf{x}_*}_{\text{epistemic}}$$

- **Aleatoric uncertainty** ($\sigma^2$): inherent noise in the data — irreducible, does not decrease with more data
- **Epistemic uncertainty** ($\mathbf{x}_*^\top \boldsymbol{\Sigma}_w \mathbf{x}_*$): uncertainty due to limited knowledge of parameters — decreases as $m \to \infty$ (since $\boldsymbol{\Sigma}_w \to \mathbf{0}$)

This decomposition is a key advantage of Bayesian methods: knowing *what you don't know*.

## Connection to Regularization

| Prior | Regularizer | Method |
|-------|------------|--------|
| $\mathcal{N}(\mathbf{0}, \sigma_w^2 \mathbf{I})$ | $\lambda \|\mathbf{w}\|_2^2$ | Ridge |
| $\text{Laplace}(0, b)$ | $\lambda \|\mathbf{w}\|_1$ | Lasso |
| Spike-and-slab | $\lambda \|\mathbf{w}\|_0$ | Best subset |

MAP estimation with a prior is equivalent to penalized MLE. Full Bayes goes further by maintaining the entire distribution.

## Related pages

- [[ml-fundamentals/generative-learning]]
- [[ml-fundamentals/empirical-risk-minimization]]
- [[shared/bayes-classifier]]
- [[pattern-recognition/parameter-estimation]]
- [[ml-fundamentals/model-selection]]
