# Reinforcement Learning

**Summary**: Learning to make optimal sequences of decisions under uncertainty. Covers Markov reward processes, MDPs, Bellman equations, policy evaluation, dynamic programming algorithms (policy iteration, value iteration), convergence guarantees, and finite-horizon extensions. Applications span multi-agent games, robotics, and game-playing.

**Course**: ml-methods

**Sources**: lecture_8_presentation_2026.pdf

**Last updated**: 2026-04-16

---

## Motivation

RL differs from supervised learning: No labels on actions; agent learns from trial-and-error rewards. Goal: Find a **policy** (decision rule) that maximizes cumulative reward over time (source: lecture_8_presentation_2026.pdf).

**Real-world applications**:
- OpenAI's Hide and Seek: Agents emergently develop tool use and strategy
- DeepMind Atari: DQN learns to play from pixels
- Robotics: Agents learn to walk, manipulate, navigate

## Markov Reward Processes (MRPs)

Extend Markov chains with rewards at each transition.

**Components** (source: lecture_8_presentation_2026.pdf):
- $S$: State space
- $P(s' | s)$: Transition probabilities (Markov property: memoryless)
- $R(s)$: Reward at state $s$
- $\gamma \in [0, 1)$: Discount factor (prefer immediate rewards)

**Return (cumulative discounted reward)**:
$$G_t = R(s_t) + \gamma R(s_{t+1}) + \gamma^2 R(s_{t+2}) + \cdots = \sum_{k=0}^\infty \gamma^k R(s_{t+k})$$

**State value function**:
$$V(s) = \mathbb{E}[G_t | s_t = s] = R(s) + \gamma \sum_{s'} P(s' | s) V(s')$$

This is the **Bellman equation** for the value function (defines recursive relationship).

## Markov Decision Processes (MDPs)

Add **actions** (agent's choices) to MRPs.

**Components** (source: lecture_8_presentation_2026.pdf):
- $S$: State space
- $A$: Action space
- $P(s' | s, a)$: Transition probabilities (depend on action)
- $R(s, a)$: Reward for action $a$ in state $s$
- $\gamma$: Discount factor
- $\pi(a | s)$: **Policy** (probability of taking action $a$ in state $s$)

**State value function** (under policy $\pi$):
$$V^\pi(s) = \sum_a \pi(a | s) \left[ R(s, a) + \gamma \sum_{s'} P(s' | s, a) V^\pi(s') \right]$$

**State-action value function (Q-function)**:
$$Q^\pi(s, a) = R(s, a) + \gamma \sum_{s'} P(s' | s, a) V^\pi(s')$$

The Q-value is the return from taking action $a$ in state $s$, then following policy $\pi$.

## Optimal Policy and Bellman Optimality

**Goal**: Find the policy $\pi^*$ that maximizes the state value:
$$V^*(s) = \max_\pi V^\pi(s)$$

**Bellman optimality equation** (source: lecture_8_presentation_2026.pdf):
$$V^*(s) = \max_a \left[ R(s, a) + \gamma \sum_{s'} P(s' | s, a) V^*(s') \right]$$

**Key insight**: A unique optimal **deterministic** policy exists for infinite-horizon MDPs; it is **stationary** (does not change over time).

$$\pi^*(s) = \arg\max_a Q^*(s, a)$$

## Policy Evaluation

Compute the value function for a fixed policy $\pi$.

**Iterative approach** (source: lecture_8_presentation_2026.pdf):

Initialize $V_0(s) = 0$ for all $s$. For $k = 1, 2, \ldots$:

$$V_k(s) = \sum_a \pi(a | s) \left[ R(s, a) + \gamma \sum_{s'} P(s' | s, a) V_{k-1}(s') \right]$$

**Convergence**: $V_k \to V^\pi$ as $k \to \infty$ (Bellman backup is a contraction operator).

## Policy Iteration

Alternate between policy evaluation and policy improvement.

$$i = 0; \quad \pi_0 \text{ random}$$

**DO**:
1. $V^{\pi_i} \leftarrow$ Evaluate policy $\pi_i$ (policy evaluation loop)
2. $\pi_{i+1}(s) \leftarrow \arg\max_a Q^{\pi_i}(s, a)$ (policy improvement)
3. $i \leftarrow i + 1$

**WHILE** $\pi_i \neq \pi_{i-1}$ (policy has changed)

**Convergence**: Policy iteration finds the optimal policy $\pi^*$ in finitely many iterations (source: lecture_8_presentation_2026.pdf).

## Value Iteration

Directly solve Bellman optimality equation; faster than policy iteration.

Initialize $V_0(s) = 0$. For $k = 1, 2, \ldots$:

$$V_k(s) = \max_a \left[ R(s, a) + \gamma \sum_{s'} P(s' | s, a) V_{k-1}(s') \right]$$

**Convergence**: $V_k \to V^*$ exponentially fast (rate: $\gamma^k$) (source: lecture_8_presentation_2026.pdf).

Extract policy: $\pi(s) = \arg\max_a [R(s, a) + \gamma \sum_{s'} P(s'|s,a) V^*(s')]$

## Convergence: Bellman Operators as Contractions

**Key theorem** (source: lecture_8_presentation_2026.pdf):

The Bellman backup operator $\mathcal{B}$ (both policy evaluation and value iteration) is a **contraction** in the infinity norm:

$$\|\mathcal{B}[V] - \mathcal{B}[V']\|_\infty \leq \gamma \|V - V'\|_\infty$$

where $0 < \gamma < 1$.

**Banach Fixed-Point Theorem**: Any contraction has a unique fixed point, and iterating the operator converges to it from any initialization.

**Implication**: Both policy evaluation and value iteration are guaranteed to converge to the optimal value function $V^*$ (or policy value $V^\pi$).

## Finite-Horizon MDPs

For problems with a deadline (e.g., chess game, robot with battery), the horizon $H$ (number of steps remaining) is finite.

**Complication** (source: lecture_8_presentation_2026.pdf):
- Optimal policies are **non-stationary**: the optimal action in state $s$ depends on how many steps remain
- Notation: $V_k(s)$ = optimal value if we have $k$ steps left starting in state $s$
- Base case: $V_0(s) = 0$ (no steps left, no future reward)
- Recurrence: $V_k(s) = \max_a [R(s, a) + \sum_{s'} P(s'|s,a) V_{k-1}(s')]$

**Applications**: Game trees (chess depth), robotics with energy constraints.

## Applications

### Hide and Seek (OpenAI)
Multi-agent RL: Agents play hide-and-seek competitively; emergently develop tool use, cooperation, and strategy (source: lecture_8_presentation_2026.pdf).

### Atari Games (DeepMind)
DQN learns to play Atari from raw pixels using value iteration with neural network function approximation.

### Robotics
Learn gaits, manipulation, navigation via policy gradient or actor-critic methods; combine with real-world robustness (domain randomization, sim-to-real transfer).

---

## Related Pages

- [[ml-methods/course-overview]]
- [[ml-methods/model-free-rl]]
- [[ml-methods/deep-rl]]
- [[ml-methods/multi-armed-bandits]]
- [[ml-methods/decision-trees-ensembles]]
- [[deep-learning/reinforcement-learning]]
