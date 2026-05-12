# Unsupervised Learning and EM

**Summary**: Unsupervised learning as maximum likelihood with latent variables. The Expectation-Maximization (EM) algorithm alternates between computing soft assignments (E-step) and fitting parameters (M-step). Covers the EM derivation via Jensen's inequality, GMM fitting, and the coordinate ascent interpretation.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_unsuper.pdf

**Last updated**: 2026-05-12

---

## Problem Formulation

Given unlabeled data $\{\mathbf{x}_i\}_{i=1}^m$, fit a model with **latent variables** $\mathbf{z}$ (source: ml-fundamentals/ls26_lecture_unsuper.pdf):

$$p(\mathbf{x}; \theta) = \sum_{\mathbf{z}} p(\mathbf{x}, \mathbf{z}; \theta)$$

The log-likelihood involves a log of a sum, making direct MLE intractable:

$$\log p(\mathbf{x}; \theta) = \log \sum_{\mathbf{z}} p(\mathbf{x}, \mathbf{z}; \theta)$$

## Gaussian Mixture Models (GMM)

The canonical latent-variable model (source: ml-fundamentals/ls26_lecture_unsuper.pdf):

$$p(\mathbf{x}; \theta) = \sum_{k=1}^K \pi_k\, \mathcal{N}(\mathbf{x}; \boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k)$$

- Latent variable $z_i \in \{1, \ldots, K\}$: which component generated $\mathbf{x}_i$
- Parameters $\theta = \{\pi_k, \boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k\}_{k=1}^K$
- No closed-form MLE due to the sum inside the log

## The EM Algorithm

### Jensen's Inequality Lower Bound

For any distribution $q(\mathbf{z})$ over latent variables (source: ml-fundamentals/ls26_lecture_unsuper.pdf):

$$\log p(\mathbf{x}; \theta) = \log \sum_{\mathbf{z}} q(\mathbf{z}) \frac{p(\mathbf{x}, \mathbf{z}; \theta)}{q(\mathbf{z})} \geq \sum_{\mathbf{z}} q(\mathbf{z}) \log \frac{p(\mathbf{x}, \mathbf{z}; \theta)}{q(\mathbf{z})} = \mathcal{L}(q, \theta)$$

by Jensen's inequality (log is concave). The bound $\mathcal{L}(q, \theta)$ is called the **Evidence Lower Bound (ELBO)**.

### The Gap

$$\log p(\mathbf{x}; \theta) - \mathcal{L}(q, \theta) = D_{\text{KL}}(q(\mathbf{z}) \| p(\mathbf{z} \mid \mathbf{x}; \theta)) \geq 0$$

The gap is the KL divergence between $q$ and the true posterior. The bound is tight when $q(\mathbf{z}) = p(\mathbf{z} \mid \mathbf{x}; \theta)$.

### E-Step (Expectation)

Set $q(\mathbf{z}) = p(\mathbf{z} \mid \mathbf{x}; \theta^{\text{old}})$ — compute the posterior over latent variables using current parameters:

For GMMs, this gives the **responsibilities** (soft assignments):

$$\gamma_{ik} = p(z_i = k \mid \mathbf{x}_i; \theta^{\text{old}}) = \frac{\pi_k\, \mathcal{N}(\mathbf{x}_i; \boldsymbol{\mu}_k, \boldsymbol{\Sigma}_k)}{\sum_j \pi_j\, \mathcal{N}(\mathbf{x}_i; \boldsymbol{\mu}_j, \boldsymbol{\Sigma}_j)}$$

### M-Step (Maximization)

Maximize $\mathcal{L}(q, \theta)$ with respect to $\theta$. With $q$ fixed from the E-step, this reduces to **weighted MLE** — supervised learning with soft labels (source: ml-fundamentals/ls26_lecture_unsuper.pdf):

For GMMs:

$$\boldsymbol{\mu}_k = \frac{\sum_i \gamma_{ik}\, \mathbf{x}_i}{\sum_i \gamma_{ik}}, \quad \boldsymbol{\Sigma}_k = \frac{\sum_i \gamma_{ik}\, (\mathbf{x}_i - \boldsymbol{\mu}_k)(\mathbf{x}_i - \boldsymbol{\mu}_k)^\top}{\sum_i \gamma_{ik}}, \quad \pi_k = \frac{1}{m} \sum_i \gamma_{ik}$$

### EM as Coordinate Ascent

EM alternates two steps on the ELBO $\mathcal{L}(q, \theta)$ (source: ml-fundamentals/ls26_lecture_unsuper.pdf):

1. **E-step**: maximize $\mathcal{L}$ over $q$ (with $\theta$ fixed) → closes the KL gap
2. **M-step**: maximize $\mathcal{L}$ over $\theta$ (with $q$ fixed) → increases the lower bound

Each step is guaranteed to not decrease the log-likelihood, so **EM monotonically increases $\log p(\mathbf{x}; \theta)$** and converges to a local maximum.

## Properties

- **Guaranteed convergence** to a local maximum (not necessarily global)
- **Sensitive to initialization** — commonly use K-means++ or random restarts
- **Connection to K-means**: hard EM (assigning each point to its most likely cluster) recovers K-means as a special case with isotropic, equal-variance Gaussians
- **Generalizes beyond GMMs**: HMMs (Baum-Welch), topic models, missing data problems

## Related pages

- [[ml-fundamentals/generative-learning]]
- [[ml-fundamentals/bayesian-learning]]
- [[pattern-recognition/em-algorithm]]
- [[pattern-recognition/clustering]]
- [[ml-fundamentals/empirical-risk-minimization]]
