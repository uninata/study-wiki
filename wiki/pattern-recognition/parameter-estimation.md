# Parameter Estimation

**Summary**: Methods for estimating parameters of probabilistic models from data. Covers Maximum Likelihood Estimation (MLE), Maximum A Posteriori (MAP), full Bayesian estimation, conjugate priors, and the connection to non-parametric density estimation.

**Course**: pattern-recognition

**Sources**: pr_03_parameter_estimation_2022.pdf

**Last updated**: 2026-04-15

---

## Maximum Likelihood Estimation (MLE)

Given i.i.d. data D = {x_1, ..., x_n} from a distribution p(x|theta), the MLE is (source: pattern-recognition/pr_03_parameter_estimation_2022.pdf):

**theta_MLE = argmax_theta prod_i p(x_i|theta) = argmax_theta sum_i log p(x_i|theta)**

### MLE for Gaussian

For a univariate Gaussian N(mu, sigma^2):
- mu_MLE = (1/n) sum_i x_i (sample mean)
- sigma^2_MLE = (1/n) sum_i (x_i - mu_MLE)^2 (biased variance estimator)

For a multivariate Gaussian N(mu, Sigma):
- mu_MLE = (1/n) sum_i x_i
- Sigma_MLE = (1/n) sum_i (x_i - mu_MLE)(x_i - mu_MLE)^T

### Properties of MLE
- **Consistent**: theta_MLE -> theta_true as n -> infinity
- **Asymptotically normal**: sqrt(n)(theta_MLE - theta_true) -> N(0, I(theta)^{-1})
- **Asymptotically efficient**: achieves the Cramer-Rao lower bound
- **Invariant**: MLE of g(theta) is g(theta_MLE)
- Can overfit with small samples (no regularization)

## Maximum A Posteriori (MAP)

Incorporates a prior p(theta) over parameters (source: pattern-recognition/pr_03_parameter_estimation_2022.pdf):

**theta_MAP = argmax_theta p(theta|D) = argmax_theta [log p(D|theta) + log p(theta)]**

MAP = MLE + log-prior regularization. With a Gaussian prior on theta, MAP corresponds to L2 regularization.

## Bayesian Estimation

Instead of a point estimate, maintain the full posterior (source: pattern-recognition/pr_03_parameter_estimation_2022.pdf):

**p(theta|D) = p(D|theta) p(theta) / p(D)**

The **predictive distribution** for a new observation x integrates over all parameter values:

**p(x|D) = integral p(x|theta) p(theta|D) d(theta)**

This automatically accounts for parameter uncertainty — predictions are more spread when data is scarce.

## Conjugate Priors

A prior is **conjugate** to a likelihood if the posterior has the same functional form as the prior (source: pattern-recognition/pr_03_parameter_estimation_2022.pdf):

| Likelihood | Conjugate prior | Posterior |
|-----------|----------------|-----------|
| Gaussian (known var) | Gaussian | Gaussian |
| Gaussian (known mean) | Inverse-Gamma | Inverse-Gamma |
| Bernoulli | Beta | Beta |
| Multinomial | Dirichlet | Dirichlet |

### Example: Gaussian with Known Variance

Prior: mu ~ N(mu_0, sigma_0^2). After observing n data points with sample mean x_bar:

- Posterior mean: mu_n = (sigma^2 mu_0 + n sigma_0^2 x_bar) / (sigma^2 + n sigma_0^2)
- Posterior variance: sigma_n^2 = (sigma^2 sigma_0^2) / (sigma^2 + n sigma_0^2)

As n -> infinity, the posterior concentrates at x_bar (MLE). As sigma_0 -> infinity (vague prior), MAP -> MLE.

## MLE vs MAP vs Bayesian — Comparison

| Property | MLE | MAP | Bayesian |
|---------|-----|-----|----------|
| Point estimate | Yes | Yes | No (distribution) |
| Uses prior | No | Yes | Yes |
| Regularization | None | Implicit | Full uncertainty |
| Computational cost | Low | Low | High (integration) |
| Small sample behavior | Overfits | Better | Best |
| Asymptotic behavior | All converge to the same answer | | |

## Non-Parametric Density Estimation

When the parametric form is unknown, estimate the density directly (source: pattern-recognition/pr_03_parameter_estimation_2022.pdf):

### Histograms
Partition the space into bins and estimate p(x) = (count in bin) / (n * bin_width). Sensitive to bin width and origin placement.

### Kernel Density Estimation
See [[pattern-recognition/non-parametric-methods]] for the full treatment of Parzen windows, bandwidth selection, and multivariate extensions.

## Related pages

- [[pattern-recognition/bayesian-decision-theory]]
- [[pattern-recognition/non-parametric-methods]]
- [[pattern-recognition/logistic-regression]]
- [[pattern-recognition/em-algorithm]]
- [[ml-fundamentals/generative-learning]]
- [[shared/bayes-classifier]]
