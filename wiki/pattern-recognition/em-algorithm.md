# EM Algorithm

**Summary**: Iterative algorithm for maximum likelihood estimation with latent variables. Covers the general EM framework, Gaussian mixture models (GMMs), connection to K-means, convergence properties, and variational interpretation.

**Course**: pattern-recognition

**Sources**: em_2020.pdf

**Last updated**: 2026-04-15

---

## Motivation

When the model has **latent (hidden) variables** $\mathbf{z}$, direct MLE is intractable because the log-likelihood involves a sum inside the log (source: pattern-recognition/em_2020.pdf):

$$\log p(\mathbf{X}|\boldsymbol{\theta}) = \log \sum_{\mathbf{z}} p(\mathbf{X}, \mathbf{z}|\boldsymbol{\theta})$$

EM avoids this by iterating between inferring the latent variables and updating parameters.

## General EM Algorithm

Starting from initial parameters $\boldsymbol{\theta}^{(0)}$, iterate (source: pattern-recognition/em_2020.pdf):

### E-step (Expectation)
Compute the **posterior distribution** of latent variables given current parameters:

$$q(\mathbf{z}) = p(\mathbf{z}|\mathbf{X}, \boldsymbol{\theta}^{(t)})$$

Equivalently, compute the expected complete-data log-likelihood:

$$Q(\boldsymbol{\theta}, \boldsymbol{\theta}^{(t)}) = \mathbb{E}_{\mathbf{z}|\mathbf{X},\boldsymbol{\theta}^{(t)}} \left[\log p(\mathbf{X}, \mathbf{z}|\boldsymbol{\theta})\right]$$

### M-step (Maximization)
Maximize $Q$ with respect to $\boldsymbol{\theta}$:

$$\boldsymbol{\theta}^{(t+1)} = \arg\max_{\boldsymbol{\theta}} Q(\boldsymbol{\theta}, \boldsymbol{\theta}^{(t)})$$

## Gaussian Mixture Models (GMMs)

A GMM models the data as a weighted sum of $K$ Gaussian components (source: pattern-recognition/em_2020.pdf):

$$p(\mathbf{x}|\boldsymbol{\theta}) = \sum_{k=1}^{K} \pi_k \mathcal{N}(\mathbf{x}|\boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k)$$

where $\pi_k$ are mixing weights (sum to 1), and each component has mean $\boldsymbol{\mu}_k$ and covariance $\boldsymbol{\Sigma}_k$.

### EM for GMMs

**E-step**: compute **responsibilities** — the posterior probability that point $\mathbf{x}_i$ belongs to component $k$ (source: pattern-recognition/em_2020.pdf):

$$r_{ik} = \frac{\pi_k \mathcal{N}(\mathbf{x}_i|\boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k)}{\sum_{j} \pi_j \mathcal{N}(\mathbf{x}_i|\boldsymbol{\mu}_j, \boldsymbol{\Sigma}_j)}$$

**M-step**: update parameters using responsibilities as soft assignments:

- $N_k = \sum_{i} r_{ik}$ (effective number of points in cluster $k$)
- $$\boldsymbol{\mu}_k = \frac{1}{N_k} \sum_{i} r_{ik} \mathbf{x}_i$$
- $$\boldsymbol{\Sigma}_k = \frac{1}{N_k} \sum_{i} r_{ik} (\mathbf{x}_i - \boldsymbol{\mu}_k)(\mathbf{x}_i - \boldsymbol{\mu}_k)^\top$$
- $$\pi_k = \frac{N_k}{n}$$

## Connection to K-Means

K-means is a special case of EM for GMMs with (source: pattern-recognition/em_2020.pdf):

- All covariances equal to $\sigma^2 \mathbf{I}$ (spherical, same for all components)
- In the limit $\sigma^2 \to 0$, the responsibilities become **hard assignments** ($r_{ik} \in \{0, 1\}$)
- The E-step becomes: assign each point to the nearest center
- The M-step becomes: recompute centers as cluster means

K-means = "hard EM" for isotropic Gaussian mixtures.

## Convergence

EM has the following convergence properties (source: pattern-recognition/em_2020.pdf):

1. **Monotonic increase**: the log-likelihood never decreases: $\log p(\mathbf{X}|\boldsymbol{\theta}^{(t+1)}) \geq \log p(\mathbf{X}|\boldsymbol{\theta}^{(t)})$
2. **Convergence to a stationary point** (local maximum or saddle point)
3. **Not guaranteed to find the global maximum** — depends on initialization
4. **Linear convergence rate** — can be slow near the optimum (compared to Newton's quadratic convergence)

### Proof Sketch
The log-likelihood decomposes as: 

$$\log p(\mathbf{X}|\boldsymbol{\theta}) = \mathcal{L}(q, \boldsymbol{\theta}) + D_{\text{KL}}(q \| p(\mathbf{z}|\mathbf{X},\boldsymbol{\theta}))$$

where $\mathcal{L}$ is the Evidence Lower Bound (ELBO). The E-step sets $D_{\text{KL}} = 0$ by choosing $q = p(\mathbf{z}|\mathbf{X},\boldsymbol{\theta})$. The M-step maximizes $\mathcal{L}$ over $\boldsymbol{\theta}$. Since $D_{\text{KL}} \geq 0$, this guarantees the log-likelihood increases.

## Variational Interpretation

EM can be viewed as coordinate ascent on the ELBO (source: pattern-recognition/em_2020.pdf):

$$\mathcal{L}(q, \boldsymbol{\theta}) = \sum_{\mathbf{z}} q(\mathbf{z}) \log \left[\frac{p(\mathbf{X}, \mathbf{z}|\boldsymbol{\theta})}{q(\mathbf{z})}\right]$$

- E-step: maximize $\mathcal{L}$ over $q$ (with $\boldsymbol{\theta}$ fixed) $\Rightarrow q = p(\mathbf{z}|\mathbf{X}, \boldsymbol{\theta})$
- M-step: maximize $\mathcal{L}$ over $\boldsymbol{\theta}$ (with $q$ fixed)

This perspective generalizes to **variational inference** where the exact E-step is replaced by an approximate optimization over a restricted family of distributions.

## Related pages

- [[pattern-recognition/clustering]]
- [[pattern-recognition/parameter-estimation]]
- [[pattern-recognition/pca]]
- [[ml-fundamentals/generative-learning]]
