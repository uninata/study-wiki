# Deep Reinforcement Learning

**Summary**: Scaling RL to large state spaces using function approximation. Covers value function approximation (linear and neural network), Deep Q-Networks (DQN) with experience replay and fixed targets, Double Q-learning for maximization bias, and policy gradient methods (REINFORCE, actor-critic).

**Course**: ml-methods

**Sources**: mlm_lecture_9.pdf, mlm_lecture_11.pdf

**Last updated**: 2026-05-12

---

## Why Function Approximation?

Tabular RL requires storing $V(s)$ or $Q(s,a)$ for every state(-action). For Atari with 160×192 pixels and 128 colors, the state space has $128^{160 \times 192}$ states — tabular methods are impossible (source: ml-methods/mlm_lecture_11.pdf).

**Solution**: approximate $V^\pi(s; \mathbf{w})$ or $Q^\pi(s,a; \mathbf{w})$ with a parameterized function.

## Value Function Approximation (VFA)

### Linear VFA

$$\hat{V}^\pi(s; \mathbf{w}) = \mathbf{w}^\top \mathbf{x}(s)$$

where $\mathbf{x}(s)$ is a feature vector for state $s$. Requires good feature engineering (source: ml-methods/mlm_lecture_11.pdf).

### Neural Network VFA

$$\hat{V}^\pi(s; \mathbf{w}) = g(\mathbf{x}(s); \mathbf{w})$$

where $g$ is a neural network. Learns features automatically but loses convergence guarantees.

### MC VFA

Frame value approximation as supervised learning under MSE loss (source: ml-methods/mlm_lecture_9.pdf):

$$\mathbf{w} \leftarrow \mathbf{w} - \alpha \left(\hat{V}(s_t; \mathbf{w}) - G_t\right) \nabla_\mathbf{w} \hat{V}(s_t; \mathbf{w})$$

**Convergence**: with linear VFA, MC VFA converges to the weights minimizing the mean squared value error $\text{MSVE}_\pi(\mathbf{w})$ under the on-policy distribution.

### TD VFA

$$\mathbf{w} \leftarrow \mathbf{w} - \alpha \left(\hat{V}(s_t; \mathbf{w}) - [r_t + \gamma \hat{V}(s_{t+1}; \mathbf{w})]\right) \nabla_\mathbf{w} \hat{V}(s_t; \mathbf{w})$$

TD VFA is a **semi-gradient** method — the target $r_t + \gamma \hat{V}(s_{t+1}; \mathbf{w})$ also depends on $\mathbf{w}$ but its gradient is ignored. With linear VFA, converges to a solution within a factor of $\frac{1}{1-\gamma}$ of optimal MSVE (source: ml-methods/mlm_lecture_11.pdf).

### Convergence Summary

| | Tabular | Linear | Neural Network |
|--|---------|--------|---------------|
| MC | ✅ | Chattering | ❌ |
| SARSA | ✅ | Chattering | ❌ |
| Q-Learning | ✅ | ❌ | ❌ |

(source: ml-methods/mlm_lecture_11.pdf)

## Deep Q-Networks (DQN)

Q-learning with neural network function approximation. Two key tricks to stabilize training (source: ml-methods/mlm_lecture_11.pdf):

### Experience Replay
- Store transitions $(s_t, a_t, r_t, s_{t+1})$ in a **replay buffer** $\mathcal{D}$
- Sample random minibatches from $\mathcal{D}$ for gradient updates
- **Breaks correlations** between consecutive samples (i.i.d. assumption of SGD)

### Fixed Q-Targets
- Maintain a **target network** with weights $\mathbf{w}^-$ updated periodically (every $C$ steps: $\mathbf{w}^- \leftarrow \mathbf{w}$)
- Compute targets $y_i = r_i + \gamma \max_{a'} \hat{Q}(s_{i+1}, a'; \mathbf{w}^-)$
- Gradient step on $\left(y_i - \hat{Q}(s_i, a_i; \mathbf{w})\right)^2$
- **Stabilizes** the non-stationary target problem

## Double Q-Learning

Standard Q-learning suffers from **maximization bias** (source: ml-methods/mlm_lecture_11.pdf):

$$\mathbb{E}[\max\{X_1, \ldots, X_k\}] \geq \max\{\mathbb{E}[X_1], \ldots, \mathbb{E}[X_k]\}$$

Even with unbiased Q-estimates, $\max_a Q(s_{t+1}, a)$ is an overestimate of $\max_a Q^*(s_{t+1}, a)$.

**Fix**: maintain two Q-functions $Q^A$, $Q^B$. Use one for action selection, the other for value estimation:

$$Q^A(s_t, a_t) \leftarrow Q^A(s_t, a_t) + \alpha\left[r_t + \gamma Q^B\!\left(s_{t+1}, \arg\max_a Q^A(s_{t+1}, a)\right) - Q^A(s_t, a_t)\right]$$

Alternate which Q-function gets updated. In DQN, the target network naturally provides the second Q-function.

## Policy Gradient Methods

Instead of learning a value function and deriving a policy, **directly parameterize the policy** $\pi(a|s; \mathbf{w})$ (source: ml-methods/mlm_lecture_11.pdf):

### Policy Gradient Theorem

$$\nabla_\mathbf{w} \mathbb{E}[V(X_0; \mathbf{w})] = \mathbb{E}\left[\sum_{t=0}^{T-1} G_t \nabla_\mathbf{w} \log \pi(a_t | s_t; \mathbf{w})\right]$$

Key insight: the gradient does not require knowledge of the MDP transitions — only the **score function** $\nabla_\mathbf{w} \log \pi(a_t | s_t; \mathbf{w})$ of the policy.

### REINFORCE

Monte Carlo policy gradient (source: ml-methods/mlm_lecture_11.pdf):

1. Generate episode using $\pi(\cdot | \cdot; \mathbf{w})$
2. For each time step $t$: compute return $G_t$
3. Update: $\mathbf{w} \leftarrow \mathbf{w} + \alpha G_t \nabla_\mathbf{w} \log \pi(a_t | s_t; \mathbf{w})$

Simple but **high variance**. Improved with baselines (subtract a state-dependent baseline from $G_t$).

### Actor-Critic

Combines value-based and policy-based approaches:
- **Actor**: the policy $\pi(a|s; \mathbf{w})$ — decides what to do
- **Critic**: the value function $\hat{V}(s; \mathbf{v})$ — evaluates how good the action was

The critic's value estimate replaces the noisy return $G_t$ in the policy gradient, reducing variance.

## Value-Based vs Policy-Based

| Approach | Learns | Policy | Example |
|----------|--------|--------|---------|
| Value-based | $Q(s,a; \mathbf{w})$ | Implicit (ε-greedy) | DQN, Q-learning |
| Policy-based | $\pi(a|s; \mathbf{w})$ | Explicit | REINFORCE |
| Actor-critic | Both | Explicit | A2C, PPO |

(source: ml-methods/mlm_lecture_11.pdf)

## Related pages

- [[ml-methods/model-free-rl]]
- [[ml-methods/reinforcement-learning]]
- [[ml-methods/multi-armed-bandits]]
- [[deep-learning/reinforcement-learning]]
- [[deep-learning/neural-networks]]
