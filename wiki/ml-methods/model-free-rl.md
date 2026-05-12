# Model-Free Reinforcement Learning

**Summary**: Policy evaluation and control without knowing the MDP transition model. Monte Carlo methods estimate values from complete episode returns; Temporal Difference (TD) methods bootstrap from one-step lookahead. SARSA (on-policy) and Q-learning (off-policy) extend TD to control.

**Course**: ml-methods

**Sources**: mlm_lecture_9.pdf

**Last updated**: 2026-05-12

---

## Why Model-Free?

[[ml-methods/reinforcement-learning|Model-based RL]] (policy iteration, value iteration) requires knowing $P(s'|s,a)$ and $R(s,a)$. In most real problems, the model is unknown — the agent only observes transitions $(s_t, a_t, r_t, s_{t+1})$ from interaction (source: ml-methods/mlm_lecture_9.pdf).

## Monte Carlo Policy Evaluation

Estimate $V^\pi(s)$ from sampled episode returns (source: ml-methods/mlm_lecture_9.pdf):

1. Generate episodes under policy $\pi$: $s_1, a_1, r_1, s_2, a_2, r_2, \ldots, s_T$
2. Compute return from each visited state: $G_t = r_t + \gamma r_{t+1} + \gamma^2 r_{t+2} + \cdots$
3. Update: $V(s_t) \leftarrow V(s_t) + \alpha(G_t - V(s_t))$

### First-Visit vs Every-Visit

- **First-visit MC**: only use the first occurrence of state $s$ in each episode
- **Every-visit MC**: use every occurrence

Both converge to $V^\pi(s) = \mathbb{E}[G_t | X_t = s]$. First-visit gives unbiased estimates.

### Properties
- **Unbiased** but **high variance** (full episode returns are noisy)
- Requires **complete episodes** (cannot learn online mid-episode)
- No bootstrapping — does not use value estimates of other states

## Temporal Difference (TD) Learning

Bootstrap from a one-step lookahead instead of waiting for the full return (source: ml-methods/mlm_lecture_9.pdf):

$$V(s_t) \leftarrow V(s_t) + \alpha \left[ r_t + \gamma V(s_{t+1}) - V(s_t) \right]$$

The quantity $\delta_t = r_t + \gamma V(s_{t+1}) - V(s_t)$ is the **TD error**.

### MC vs TD

| | Monte Carlo | TD(0) |
|--|------------|-------|
| Target | $G_t$ (full return) | $r_t + \gamma V(s_{t+1})$ (bootstrap) |
| Bias | Unbiased | Biased (uses current $V$ estimate) |
| Variance | High | Lower |
| Episodes | Must be complete | Can learn online, step-by-step |
| Convergence | To $V^\pi$ (linear VFA) | To $V^\pi$ (tabular); chattering (linear VFA) |

## SARSA (On-Policy TD Control)

Learn $Q^\pi(s,a)$ while following policy $\pi$ (source: ml-methods/mlm_lecture_9.pdf):

$$Q(s_t, a_t) \leftarrow Q(s_t, a_t) + \alpha \left[ r_t + \gamma Q(s_{t+1}, a_{t+1}) - Q(s_t, a_t) \right]$$

**Algorithm**:
1. Initialize $Q(s,a)$ for all $(s,a)$
2. In state $s_t$, take action $a_t \sim \pi_\varepsilon(s_t)$ (ε-greedy w.r.t. $Q$)
3. Observe $r_t, s_{t+1}$; sample $a_{t+1} \sim \pi_\varepsilon(s_{t+1})$
4. Update $Q(s_t, a_t)$ using the tuple $(s_t, a_t, r_t, s_{t+1}, a_{t+1})$ — hence the name **SARSA**
5. Set $\pi \leftarrow \varepsilon$-greedy($Q$)

**Convergence**: converges to $Q^*$ if GLIE conditions hold ($\varepsilon_t = 1/t$) and step sizes satisfy Robbins-Monro ($\sum \alpha_t = \infty$, $\sum \alpha_t^2 < \infty$).

### Practical Notes
- **Tie-breaking**: break ties randomly to avoid biasing exploration toward arbitrary actions
- **Optimistic initialization**: initialize $Q$ values high to encourage exploration

## Q-Learning (Off-Policy TD Control)

Learn $Q^*$ directly, regardless of the policy being followed (source: ml-methods/mlm_lecture_9.pdf):

$$Q(s_t, a_t) \leftarrow Q(s_t, a_t) + \alpha \left[ r_t + \gamma \max_{a \in A} Q(s_{t+1}, a) - Q(s_t, a_t) \right]$$

**Key difference from SARSA**: uses $\max_a Q(s_{t+1}, a)$ instead of $Q(s_{t+1}, a_{t+1})$ — the update is independent of the behavior policy.

**Off-policy**: can follow an exploratory policy (e.g., ε-greedy) while learning the optimal Q-function. Converges if every state-action pair is visited infinitely often.

### SARSA vs Q-Learning

| | SARSA | Q-Learning |
|--|-------|-----------|
| Update target | $Q(s_{t+1}, a_{t+1})$ | $\max_a Q(s_{t+1}, a)$ |
| Policy type | On-policy | Off-policy |
| Learns | $Q^\pi$ → optimal via GLIE | $Q^*$ directly |
| Safety | More conservative (respects exploration policy) | Can be riskier (ignores behavior policy) |

## Related pages

- [[ml-methods/reinforcement-learning]]
- [[ml-methods/deep-rl]]
- [[ml-methods/multi-armed-bandits]]
- [[deep-learning/reinforcement-learning]]
