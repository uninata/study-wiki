# Reinforcement Learning

**Summary**: Comprehensive treatment of reinforcement learning for the deep learning course. Covers Markov Decision Processes, value functions, temporal difference learning, Deep Q-Networks (DQN), policy gradient methods (REINFORCE, actor-critic, PPO/TRPO, DDPG), reward engineering, imitation learning, and inverse RL. Includes application case studies in games, robotics, and learned augmentation.

**Course**: deep-learning

**Sources**: 10_reinforcement_learning.pdf, 10_reinforcementlearning.pdf

**Last updated**: 2026-04-15

---

## Markov Decision Process (MDP)

The formal framework for sequential decision-making (source: deep-learning/10_reinforcement_learning.pdf):

- **State** x ∈ X: current situation
- **Action** u ∈ U: decision taken by the agent
- **Transition** p(x' | x, u): probability of next state given current state and action
- **Reward** r(x, u, x'): immediate scalar feedback
- **Policy** π(u | x): mapping from states to actions (stochastic) or π(x) -> u (deterministic)
- **Discount factor** gamma ∈ [0, 1]: trade-off between immediate and future rewards

**Goal**: find policy π* that maximizes the expected discounted return:

**J(π) = E[sum_{t=0}^{T} gamma^t r_{t+1}]**

## Value Functions

### State Value Function V^π(x)
Expected return starting from state x, following policy π (source: deep-learning/10_reinforcement_learning.pdf):

**V^π(x) = E_π[sum_{t=0}^{T} gamma^t r_{t+1} | x_0 = x]**

### Action Value Function Q^π(x, u)
Expected return starting from state x, taking action u, then following π:

**Q^π(x, u) = E_π[sum_{t=0}^{T} gamma^t r_{t+1} | x_0 = x, u_0 = u]**

### Advantage Function A^π(x, u)
How much better action u is compared to the average (source: deep-learning/10_reinforcement_learning.pdf):

**A^π(x, u) = Q^π(x, u) - V^π(x)**

- A > 0: action is better than average
- A < 0: action is worse than average
- Used to reduce variance in policy gradient methods

### Bellman Equations
**V^π(x) = E_{u~π}[r + gamma * V^π(x')]**
**Q^π(x, u) = E[r + gamma * E_{u'~π}[Q^π(x', u')]]**

## Temporal Difference Learning

Bootstrapping: update value estimates using other value estimates (source: deep-learning/10_reinforcement_learning.pdf).

### TD(0)
**V(x) <- V(x) + alpha * [r + gamma * V(x') - V(x)]**

- The term [r + gamma * V(x') - V(x)] is the **TD error**
- Updates after each step (online)
- Low variance but biased (bootstraps from estimated value)

### TD(1) = Monte Carlo
**V(x) <- V(x) + alpha * [G - V(x)]** where G = sum of actual discounted rewards

- Unbiased but high variance (uses actual returns)
- Must wait until episode ends

### TD(lambda)
Interpolate between TD(0) and MC (source: deep-learning/10_reinforcement_learning.pdf):

**G^lambda = (1-lambda) sum_{n=1}^{inf} lambda^{n-1} G^(n)**

- lambda = 0: TD(0)
- lambda = 1: Monte Carlo
- lambda ∈ (0,1): trade-off between bias and variance

## Q-Learning and DQN

### Q-Learning
Off-policy TD control — learn Q* directly (source: deep-learning/10_reinforcement_learning.pdf):

**Q(x, u) <- Q(x, u) + alpha * [r + gamma * max_{u'} Q(x', u') - Q(x, u)]**

### Deep Q-Network (DQN)
Approximate Q with a neural network Q_theta(x, u) (source: deep-learning/10_reinforcement_learning.pdf):

Key innovations for stable training:
1. **Experience replay**: store transitions (x, u, r, x') in a replay buffer, sample mini-batches randomly — decorrelates samples
2. **Target network**: separate network theta_bar for computing targets, updated slowly: theta_bar <- alpha * theta + (1-alpha) * theta_bar — stabilizes training

DQN algorithm:
1. Collect transition [x, u, r, x'] => ReplayMemory
2. Sample transitions from ReplayMemory
3. Estimate target: y = r + gamma * max_{u'} Q_{theta_bar}(x', u')
4. Update: minimize ||Q_theta(x, u) - y||^2
5. Slowly update target network

**Limitation**: requires discrete action space (output one Q-value per action). For continuous actions, need DDPG.

## Deep Deterministic Policy Gradient (DDPG)

Actor-critic for continuous action spaces (source: deep-learning/10_reinforcement_learning.pdf):

- **Critic** Q_{theta^Q}(x, u): estimates Q-value (takes state AND action as input, outputs scalar)
- **Actor** π_{theta^π}(x): outputs continuous action

The critic solves the DQN bottleneck: instead of max over discrete actions, use the actor to provide the action:

**y = r + gamma * Q_{theta_bar^Q}(x', π_{theta_bar^π}(x'))**

Update actor by maximizing Q: **argmax_{theta^π} sum_x Q_{theta^Q}(x, π_{theta^π}(x))**

Both actor and critic have target networks with soft updates. Off-policy (uses replay buffer).

## On-Policy vs Off-Policy

| | Off-policy (DQN, DDPG) | On-policy (REINFORCE, PPO) |
|---|---|---|
| Data | Can reuse old transitions (replay buffer) | Must use fresh data from current policy |
| Sample efficiency | Higher | Lower |
| Stability | Can be unstable | More stable |
| Exploration | Can use any exploration strategy | Exploration tied to current policy |

(source: deep-learning/10_reinforcement_learning.pdf)

## Policy Gradient Methods

### REINFORCE
On-policy, stochastic policy gradient (source: deep-learning/10_reinforcement_learning.pdf):

**dJ/d(theta) ≈ sum_t d(log π_theta(u_t | x_t))/d(theta) * r(τ)**

where r(τ) is the total return of the trajectory.

Intuition: increase probability of actions that led to high returns, decrease for low returns.

Algorithm:
1. Initialize policy π_theta
2. Collect trajectories with π_theta
3. Update: theta <- theta + alpha * dJ/d(theta)
4. Repeat from 2

**High variance**: multiplying by full trajectory return is noisy.

### Actor-Critic
Replace trajectory return with advantage estimate (source: deep-learning/10_reinforcement_learning.pdf):

**Actor loss**: L_actor = sum log π_theta(u | x) * A_omega(u, x)

where **A_omega = r + gamma * V_omega(x') - V_omega(x)** (TD advantage estimate)

**Critic loss**: L_critic = (r + gamma * V_omega(x') - V_omega(x))^2

- Actor: policy network (outputs action distribution)
- Critic: value network (estimates V(x))
- Lower variance than REINFORCE because advantage function is relative
- On-policy: data from current policy only

### PPO (Proximal Policy Optimization)
Constrain policy updates to stay close to the previous policy (source: deep-learning/10_reinforcement_learning.pdf):

**L_PPO = E[min(r_t * A_hat, clip(r_t, 1-epsilon, 1+epsilon) * A_hat)]**

where r_t = π_theta(u|x) / π_{theta_old}(u|x) is the probability ratio.

- Clipping prevents too-large policy updates (epsilon typically 0.2)
- Surrogate objective is a lower bound on the true objective
- Simple to implement, stable, strong performance
- **The default RL algorithm** for most applications (robotics, game playing, RLHF)

### TRPO (Trust Region Policy Optimization)
Same motivation as PPO but uses KL divergence constraint (source: deep-learning/10_reinforcement_learning.pdf):

**maximize E[r_t * A_hat]** subject to **E[D_KL(π_theta || π_{theta_old})] <= epsilon**

- More theoretically grounded than PPO
- Harder to implement (requires conjugate gradient solver)
- PPO is usually preferred in practice

## Reward Engineering

Designing reward functions is critical and challenging (source: deep-learning/10_reinforcement_learning.pdf):

- **Sparse rewards**: easy to specify correctly (e.g., win/lose) but hard to learn from (signal is rare)
- **Dense rewards**: easier to learn from (feedback every step) but can lead to unintended behavior if poorly designed
- Well-chosen dense rewards smooth the optimization landscape toward the true objective
- Badly chosen dense rewards can shift the optimum away from the desired behavior

### Typical Problems in RL
| Problem | Given | Find |
|---------|-------|------|
| Model identification | Trajectories | Transition model p(x'|x,u) |
| Planning/MPC | Model + reward | Optimal policy |
| Reinforcement learning | Trajectories + rewards | Optimal policy |
| Inverse RL | Optimal trajectories | Reward function |

## Imitation Learning

Learn from expert demonstrations instead of rewards (source: deep-learning/10_reinforcement_learning.pdf):

**argmin_theta sum_{(x_i, a_i) ∈ τ*} ||π_theta(x_i) - a_i||^2**

- Statistically inconsistent (distribution mismatch between expert and learned policy)
- Black-box approach — doesn't understand why expert acts that way

### Inverse Reinforcement Learning
Learn the reward function from expert trajectories, then solve the RL problem:
1. Collect expert trajectories τ*
2. Find reward function r_w that makes expert trajectories optimal
3. Solve the RL/control task with learned reward

More principled than direct imitation — reward function transfers to new situations.

## Applications

RL has achieved superhuman performance in several domains (source: deep-learning/10_reinforcement_learning.pdf):

- **Games**: DQN on Atari, AlphaStar on StarCraft II, AlphaZero on Chess/Go/Shogi, OpenAI Five on Dota 2
- **Robotics**: locomotion in simulation (MuJoCo), manipulation (grasping from pixels), legged robots (sim-to-real transfer), drone racing (champion-level using PPO)
- **AutoML**: Neural Architecture Search, learned data augmentation (AutoAugment, PBA)
- **Language models**: RLHF (Reinforcement Learning from Human Feedback) for alignment

## Related pages

- [[deep-learning/neural-networks]]
- [[deep-learning/optimization]]
- [[deep-learning/mle-kl-divergence]]
- [[deep-learning/implicit-layers]]
- [[ml-methods/reinforcement-learning]] — MDP theory, Bellman equations, policy/value iteration
