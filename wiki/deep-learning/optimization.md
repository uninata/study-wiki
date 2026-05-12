# Optimization

**Summary**: Optimization methods for training deep neural networks. Covers SGD variants (momentum, Nesterov), adaptive methods (AdaGrad, RMSProp, Adam, AdamW), learning rate scheduling (step, cosine, warmup), weight decay vs L2 regularization, gradient clipping, label smoothing, Sharpness-Aware Minimization (SAM), and Stochastic Weight Averaging (SWA).

**Course**: deep-learning

**Sources**: 07_optimization.pdf

**Last updated**: 2026-04-15

---

## SGD Variants

### Vanilla SGD
**w <- w - eta * g** where g = dL/dw on a mini-batch (source: deep-learning/07_optimization.pdf).

### SGD with Momentum
Accumulate a velocity that smooths out gradient noise (source: deep-learning/07_optimization.pdf):

**v <- beta * v + g**
**w <- w - eta * v**

- beta = 0.9 is typical (90% of previous velocity retained)
- Accelerates through flat regions and dampens oscillations in steep directions
- Effectively increases the step size along consistent gradient directions

### Nesterov Accelerated Gradient (NAG)
"Look ahead" — evaluate gradient at the anticipated position (source: deep-learning/07_optimization.pdf):

**v <- beta * v + gradient(w - eta * beta * v)**
**w <- w - eta * v**

- Provides a correction: if momentum is carrying us too far, the gradient at the look-ahead point pulls us back
- Slightly better convergence than standard momentum in practice

## Adaptive Learning Rate Methods

### AdaGrad
Accumulate squared gradients to scale the learning rate per-parameter (source: deep-learning/07_optimization.pdf):

**G <- G + g^2**
**w <- w - eta * g / (sqrt(G) + epsilon)**

- Parameters with large historical gradients get smaller effective LR
- Good for sparse data
- **Problem**: learning rate monotonically decreases — can stop learning too early

### RMSProp
Fix AdaGrad's decaying LR with exponential moving average (source: deep-learning/07_optimization.pdf):

**G <- rho * G + (1-rho) * g^2**
**w <- w - eta * g / (sqrt(G) + epsilon)**

- rho = 0.9 typical — recent gradients matter more
- Adapts per-parameter but doesn't accumulate forever

### Adam (Adaptive Moment Estimation)
Combines momentum (first moment) with RMSProp (second moment) (source: deep-learning/07_optimization.pdf):

**m <- beta_1 * m + (1-beta_1) * g** (first moment estimate)
**v <- beta_2 * v + (1-beta_2) * g^2** (second moment estimate)
**m_hat = m / (1-beta_1^t)** (bias correction)
**v_hat = v / (1-beta_2^t)** (bias correction)
**w <- w - eta * m_hat / (sqrt(v_hat) + epsilon)**

- Default: beta_1=0.9, beta_2=0.999, eta=1e-3, epsilon=1e-8
- Bias correction handles the zero-initialization of m, v in early steps
- **The default optimizer for most deep learning tasks**

### AdamW
Decouples weight decay from the gradient-based update (source: deep-learning/07_optimization.pdf):

**w <- w - eta * (m_hat / (sqrt(v_hat) + epsilon) + lambda * w)**

See the Weight Decay section below for why this matters.

## Weight Decay vs L2 Regularization

These are **not equivalent** for adaptive optimizers (source: deep-learning/07_optimization.pdf):

### L2 Regularization
Add lambda * ||w||^2 to the loss. The gradient becomes g + 2*lambda*w. For SGD, this is equivalent to weight decay, but for Adam, the regularization term gets scaled by the adaptive learning rate — defeating the purpose.

### Weight Decay (Decoupled)
Directly shrink weights each step: **w <- (1 - eta*lambda) * w - eta * update**. This applies the same shrinkage regardless of the adaptive scaling.

**AdamW = Adam + decoupled weight decay** — the standard for training modern networks (transformers, large CNNs). L2 regularization with Adam is suboptimal.

## Learning Rate Scheduling

### Step Decay
Multiply LR by a factor (e.g., 0.1) at predetermined epochs (source: deep-learning/07_optimization.pdf).

### Cosine Annealing
**eta_t = eta_min + 0.5 * (eta_max - eta_min) * (1 + cos(pi * t / T))**

Smooth decay from eta_max to eta_min over T steps. Standard for modern training recipes.

### Warmup
Start with a very small LR and linearly increase to the target LR over the first N steps (source: deep-learning/07_optimization.pdf):

**eta_t = eta_target * t / N** for t <= N

- Prevents instability in early training when gradients are large and noisy
- Critical for transformers and large batch training
- Typical: warmup for 5-10% of total training steps

### Cosine with Warmup
The most common modern schedule: linear warmup followed by cosine decay.

## Gradient Clipping

Prevent exploding gradients by capping the gradient norm (source: deep-learning/07_optimization.pdf):

**if ||g|| > threshold: g <- threshold * g / ||g||**

- Essential for RNNs and transformers
- Does not change direction, only magnitude
- Typical threshold: 1.0 or 5.0

## Label Smoothing

Replace hard one-hot targets with soft targets (source: deep-learning/07_optimization.pdf):

**y_smooth = (1 - alpha) * y_onehot + alpha / K**

where alpha is the smoothing factor (typically 0.1) and K is the number of classes.

- Prevents overconfident predictions
- Acts as a regularizer — penalizes very large logits
- Improves calibration and generalization

## Sharpness-Aware Minimization (SAM)

Seeks parameters that lie in flat regions of the loss landscape (source: deep-learning/07_optimization.pdf):

**min_w max_{||epsilon||<=rho} L(w + epsilon)**

Approximated in two steps:
1. Compute adversarial perturbation: epsilon = rho * g / ||g||
2. Compute gradient at perturbed point: g_SAM = gradient(L(w + epsilon))
3. Update: w <- w - eta * g_SAM

- Flat minima generalize better than sharp minima
- ~2x compute cost (two forward-backward passes per step)
- Consistent improvement across tasks (+0.5-1% accuracy)

## Stochastic Weight Averaging (SWA)

Average weights from multiple points along the SGD trajectory (source: deep-learning/07_optimization.pdf):

1. Train normally until convergence
2. Continue training with a cyclic or constant LR
3. Average the weights every N steps: **w_SWA = running average of checkpoints**

- Finds wider optima than standard SGD
- Essentially free — just average checkpoints
- Often combined with SWA-Gaussian (SWAG) for uncertainty estimation

## Summary of Optimizers

| Optimizer | Key idea | When to use |
|-----------|----------|-------------|
| **SGD + Momentum** | Simple, well-understood | CNNs when tuning LR carefully |
| **Adam** | Adaptive LR, fast convergence | Default starting point |
| **AdamW** | Adam + proper weight decay | Transformers, modern training |
| **SAM** | Flat minima seeking | When max accuracy matters |
| **SWA** | Weight averaging | Final refinement step |

(source: deep-learning/07_optimization.pdf)

## Related pages

- [[deep-learning/linear-classifiers]]
- [[deep-learning/neural-networks]]
- [[deep-learning/normalization]]
- [[deep-learning/backbone-architectures]]
