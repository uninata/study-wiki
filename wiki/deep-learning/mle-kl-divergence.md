# MLE and KL Divergence

**Summary**: Information-theoretic foundations of deep learning. Covers entropy, cross-entropy, KL divergence, the connection between MLE and KL minimization, and the exponential family of distributions.

**Course**: deep-learning

**Sources**: 03_maximumlikelihoodkldivergence.pdf

**Last updated**: 2026-04-15

---

## Information Theory Basics

### Entropy
Measures the average "surprise" or uncertainty in a random variable (source: deep-learning/03_maximumlikelihoodkldivergence.pdf):

$$H(p) = -\sum_x p(x) \log p(x)$$

- Uniform distribution has maximum entropy
- Deterministic distribution (one outcome certain) has entropy 0
- Units: bits (log base 2) or nats (natural log)

### Cross-Entropy
Measures the average surprise when using distribution $q$ to encode samples from $p$ (source: deep-learning/03_maximumlikelihoodkldivergence.pdf):

$$H(p, q) = -\sum_x p(x) \log q(x)$$

- Always $\geq H(p)$ (entropy of true distribution)
- Equals $H(p)$ only when $q = p$
- This is the standard classification loss: $p$ is the one-hot label, $q$ is the model's softmax output

### KL Divergence
Measures how much $q$ differs from $p$ (source: deep-learning/03_maximumlikelihoodkldivergence.pdf):

$$D_{\text{KL}}(p \| q) = \sum_x p(x) \log\left(\frac{p(x)}{q(x)}\right) = H(p, q) - H(p)$$

Properties:
- $D_{\text{KL}}(p \| q) \geq 0$ (Gibbs' inequality)
- $D_{\text{KL}}(p \| q) = 0$ if and only if $p = q$
- **Not symmetric**: $D_{\text{KL}}(p \| q) \neq D_{\text{KL}}(q \| p)$ in general
- Not a true metric (no triangle inequality)

### Forward vs Reverse KL

| | Forward KL: $D_{\text{KL}}(p \| q)$ | Reverse KL: $D_{\text{KL}}(q \| p)$ |
|---|---|---|
| **Behavior** | $q$ must cover all of $p$'s mass | $q$ can ignore parts of $p$ |
| **Mode behavior** | Mode-covering (mean-seeking) | Mode-collapsing (mode-seeking) |
| **Use case** | Variational inference (ELBO) | Policy optimization |

## MLE as KL Minimization

The key insight connecting MLE to information theory (source: deep-learning/03_maximumlikelihoodkldivergence.pdf):

$$\text{Minimizing } H(p_{\text{data}}, q_\theta) = \text{Minimizing } D_{\text{KL}}(p_{\text{data}} \| q_\theta) = \text{Maximizing likelihood}$$

Proof sketch:
1. $D_{\text{KL}}(p_{\text{data}} \| q_\theta) = H(p_{\text{data}}, q_\theta) - H(p_{\text{data}})$
2. $H(p_{\text{data}})$ is constant w.r.t. $\theta$
3. So minimizing KL = minimizing cross-entropy
4. Cross-entropy = $-\mathbb{E}_{x \sim p_{\text{data}}}[\log q_\theta(x)]$ = negative log-likelihood

This means the standard cross-entropy loss used in classification is doing MLE, which is doing KL minimization.

## Maximum Likelihood Estimation

### MLE Framework
Given data $\{x_1, \ldots, x_n\}$ i.i.d. from unknown $p_{\text{data}}$, find parameters $\theta$ that maximize (source: deep-learning/03_maximumlikelihoodkldivergence.pdf):

$$\theta_{\text{MLE}} = \arg\max_\theta \prod_{i} q_\theta(x_i) = \arg\max_\theta \sum_{i} \log q_\theta(x_i)$$

### Properties of MLE
- **Consistent**: converges to true parameters as $n \to \infty$
- **Asymptotically efficient**: achieves Cramér-Rao lower bound
- **Asymptotically normal**: $\theta_{\text{MLE}} \sim \mathcal{N}(\theta^*, I(\theta^*)^{-1}/n)$ where $I$ is Fisher information
- Connection to [[ml-fundamentals/generative-learning]] and [[pattern-recognition/parameter-estimation]]

## Exponential Family

A family of distributions with the form (source: deep-learning/03_maximumlikelihoodkldivergence.pdf):

$$p(x \mid \boldsymbol{\eta}) = h(x) \exp(\boldsymbol{\eta}^T \mathbf{T}(x) - A(\boldsymbol{\eta}))$$

where:
- $\boldsymbol{\eta}$ = natural parameters
- $\mathbf{T}(x)$ = sufficient statistics
- $A(\boldsymbol{\eta})$ = log-partition function (normalizer)
- $h(x)$ = base measure

### Examples
| Distribution | Natural parameter $\boldsymbol{\eta}$ | Sufficient statistic $\mathbf{T}(x)$ |
|-------------|----------------------|--------------------------|
| Bernoulli | $\log\frac{p}{1-p}$ | $x$ |
| Gaussian (known var) | $\frac{\mu}{\sigma^2}$ | $x$ |
| Poisson | $\log\lambda$ | $x$ |
| Categorical | $\log\frac{p_k}{p_K}$ | one-hot$(x)$ |

### Key Properties
- MLE for exponential family: match empirical moments to model moments — $\mathbb{E}_{\text{model}}[\mathbf{T}(x)] = \frac{1}{n}\sum_i \mathbf{T}(x_i)$
- $A(\boldsymbol{\eta})$ is convex $\Rightarrow$ MLE has a unique solution
- Gradient: $\frac{dA}{d\boldsymbol{\eta}} = \mathbb{E}[\mathbf{T}(x)]$ (mean of sufficient statistics)
- Softmax output layer implements a categorical exponential family

## Connection to Deep Learning Losses

| Loss | Equivalent to | Distribution assumed |
|------|--------------|---------------------|
| Cross-entropy | MLE under categorical | Categorical (softmax) |
| MSE | MLE under Gaussian | Gaussian with fixed variance |
| Binary cross-entropy | MLE under Bernoulli | Bernoulli (sigmoid) |

(source: deep-learning/03_maximumlikelihoodkldivergence.pdf)

## Related pages

- [[deep-learning/linear-classifiers]]
- [[deep-learning/neural-networks]]
- [[ml-fundamentals/generative-learning]]
- [[pattern-recognition/parameter-estimation]]
- [[pattern-recognition/logistic-regression]]
