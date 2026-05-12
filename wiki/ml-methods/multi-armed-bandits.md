# Multi-Armed Bandits

**Summary**: The exploration-exploitation tradeoff in its simplest form — a single-state MDP where the agent repeatedly chooses among $m$ actions with unknown reward distributions. Covers regret formalization, greedy/ε-greedy failures, Upper Confidence Bound (UCB) with sublinear regret guarantee, and Thompson Sampling as Bayesian probability matching.

**Course**: ml-methods

**Sources**: bandits-intro-ucb.pdf, thompson_sampling.pdf, mlm_lecture_11.pdf

**Last updated**: 2026-05-12

---

## Problem Definition

A **multi-armed bandit** is a degenerate MDP with a single state (source: ml-methods/bandits-intro-ucb.pdf):

- $m$ actions (arms) $a_1, \ldots, a_m$
- Reward distributions $P[R_t = r | A_t = a]$ (unknown to the agent)
- At each step: choose an action, observe a reward
- Goal: maximize cumulative reward $\sum_{t=1}^T R_t$

**Action-value**: $Q(a) = \mathbb{E}[R_t | A_t = a]$

**Optimal value**: $V^* = \max_{a \in A} Q(a)$

## Regret

The formal measure of performance (source: ml-methods/bandits-intro-ucb.pdf):

**Per-step regret**: $L_t = V^* - Q(A_t)$ (expected opportunity loss at time $t$)

**Total regret**: $L_T^{\text{tot}} = \sum_{t=1}^T (V^* - Q(A_t))$

Minimizing total regret is equivalent to maximizing expected cumulative reward. We want algorithms with **sublinear** regret — meaning $L_T^{\text{tot}} / T \to 0$, i.e., the agent eventually acts optimally.

## Why Simple Strategies Fail

### Greedy

Always pick $\arg\max_a \hat{Q}(a)$. If the initial estimate is wrong (e.g., unlucky samples), the algorithm **never explores** the truly optimal arm → **linear regret** (source: ml-methods/bandits-intro-ucb.pdf).

### ε-Greedy

Explore with probability $\varepsilon$, exploit with probability $1 - \varepsilon$. With constant $\varepsilon$, each step has probability $\varepsilon - \varepsilon/|A|$ of picking a suboptimal arm → **linear regret** (source: ml-methods/bandits-intro-ucb.pdf).

Decaying $\varepsilon_t$ can work in theory but requires knowing properties of $Q(a)$ in advance.

## Upper Confidence Bound (UCB)

**Principle**: optimism in the face of uncertainty — maintain an upper confidence bound for each arm and always pull the arm with the highest bound (source: ml-methods/bandits-intro-ucb.pdf).

### Algorithm

1. **Initialize**: pull each arm once
2. **Loop**: compute UCB for each arm $a_i$:

$$U_t(a_i) = \hat{Q}(a_i) + \sqrt{\frac{1}{2N_t(a_i)} \log \frac{2t^2}{\delta}}$$

3. Pull arm $a_t = \arg\max_a U_t(a)$
4. Observe reward $r_t$; update $\hat{Q}(a_t)$ and $N_t(a_t)$

where $N_t(a)$ is the number of times arm $a$ has been pulled.

### Regret Guarantee

With probability at least $1 - 2\delta m$ (source: ml-methods/bandits-intro-ucb.pdf):

$$L_T^{\text{tot}} \leq 2\sqrt{\frac{Tm}{2} \log \frac{T}{\delta}}$$

This is **sublinear** in $T$ — specifically $O(\sqrt{Tm \log T})$.

### Proof Sketch
1. By **Hoeffding's inequality**, $\hat{Q}(a)$ concentrates around $Q(a)$ with width $\sqrt{\frac{1}{2N(a)} \log \frac{2t^2}{\delta}}$
2. **Union bound** over all arms and all time steps: all confidence bounds are valid with probability $\geq 1 - 2\delta m$ (using $\sum_{t=1}^\infty 1/t^2 = \pi^2/6 < 2$)
3. If all bounds are valid, the selected arm always satisfies $U_t(a_t) \geq Q(a^*)$
4. The regret per step is bounded by twice the confidence width, which shrinks as $1/\sqrt{N(a)}$

### Adversarial Bandits
UCB assumes **stochastic** rewards (fixed distributions). An adversary that adapts to the agent's strategy can force UCB to perform poorly. Adversarial bandit algorithms exist (e.g., EXP3) but are not covered in this course (source: ml-methods/bandits-intro-ucb.pdf).

## Thompson Sampling

A **Bayesian** approach to bandits that naturally balances exploration and exploitation (source: ml-methods/thompson_sampling.pdf).

### Bernoulli Bandits Setup
- Each arm $k$ has unknown success probability $\theta_k$
- Prior: $\theta_k \sim \text{Beta}(\alpha_k, \beta_k)$, initialized at $\text{Beta}(1, 1)$ (uniform)
- After observing reward $r_t$ from arm $k$: update $(\alpha_k, \beta_k) \leftarrow (\alpha_k + r_t,\; \beta_k + 1 - r_t)$
- Posterior remains Beta (conjugate prior) — see [[ml-fundamentals/bayesian-learning]]

### Algorithm

1. **Sample** $\hat{\theta}_k \sim \text{Beta}(\alpha_k, \beta_k)$ for each arm $k$
2. **Pull** arm $x_t = \arg\max_k \hat{\theta}_k$
3. **Observe** reward $r_t$
4. **Update**: $(\alpha_{x_t}, \beta_{x_t}) \leftarrow (\alpha_{x_t} + r_t,\; \beta_{x_t} + 1 - r_t)$

### Why It Works: Probability Matching

Thompson sampling implements **probability matching** (source: ml-methods/thompson_sampling.pdf):

$$\pi(a | h_{1:t}) = P\!\left[\forall a' : Q(a) > Q(a') \mid h_{1:t}\right]$$

Each arm is pulled with probability equal to the posterior belief that it is the best arm. Arms with uncertain posteriors (wide distributions) get explored; arms with confidently low posteriors are avoided.

### Properties
- **Natural exploration**: uncertain arms have wide posteriors → get sampled more often
- **Automatic convergence**: as evidence accumulates, posteriors concentrate → exploitation dominates
- **Regret guarantee**: $O(\sqrt{mT \log T})$ — comparable to UCB (source: ml-methods/thompson_sampling.pdf)
- **Empirically often outperforms** UCB, especially with informative priors

## UCB vs Thompson Sampling

| | UCB | Thompson Sampling |
|--|-----|-------------------|
| Framework | Frequentist | Bayesian |
| Exploration | Explicit (confidence bonus) | Implicit (posterior sampling) |
| Prior knowledge | Not used | Can incorporate priors |
| Regret | $O(\sqrt{Tm \log T})$ | $O(\sqrt{Tm \log T})$ |
| Practical performance | Good | Often better |

## Related pages

- [[ml-methods/reinforcement-learning]]
- [[ml-methods/model-free-rl]]
- [[ml-methods/deep-rl]]
- [[ml-fundamentals/bayesian-learning]]
