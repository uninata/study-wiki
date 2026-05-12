# Implicit Layers

**Summary**: Implicit layers define their output as the solution to an equation rather than through an explicit forward computation. Covers Neural ODEs (continuous-depth networks), Deep Equilibrium Models (DEQ, infinite-depth weight-tied networks), and differentiable optimization layers. These provide memory efficiency, adaptive computation, and physics-aware architectures.

**Course**: deep-learning

**Sources**: 11_implicitlayers.pdf

**Last updated**: 2026-04-15

---

## Motivation

Standard neural networks define each layer explicitly: h^(l+1) = f(h^(l), theta^(l)). Implicit layers instead define the output as the solution to a condition (source: deep-learning/11_implicitlayers.pdf):

- Neural ODE: output is the solution of an ODE at time T
- DEQ: output is a fixed point h* where h* = f(h*, theta)
- Optimization layer: output is the solution of an optimization problem

Key advantage: backpropagation through implicit layers doesn't require storing intermediate activations — only the final output and the defining equation are needed.

## Neural ODEs

### From ResNets to ODEs
ResNet: h^(l+1) = h^(l) + f(h^(l), theta^(l)) — Euler discretization of an ODE with step size 1 (source: deep-learning/11_implicitlayers.pdf).

Taking the continuous limit: **dh/dt = f(h(t), t, theta)**

The output is h(T) obtained by integrating from h(0) = x to time T using an ODE solver (e.g., Dormand-Prince / RK45).

### Forward Pass
Use a standard ODE solver to compute h(T) from h(0) = x:

**h(T) = h(0) + integral_0^T f(h(t), t, theta) dt**

The solver adaptively chooses step sizes based on error tolerance — more computation for complex dynamics, less for simple ones.

### Backward Pass (Adjoint Method)
Instead of backpropagating through solver steps (memory-expensive), solve an augmented ODE backward in time (source: deep-learning/11_implicitlayers.pdf):

1. Define adjoint: a(t) = dL/dh(t)
2. Adjoint ODE: da/dt = -a^T * df/dh
3. Parameter gradient: dL/d(theta) = -integral_T^0 a(t)^T * df/d(theta) dt

- **O(1) memory**: doesn't store intermediate activations
- Trade-off: slower (solve ODE backwards) but constant memory regardless of depth

### Properties
- Continuous depth — no fixed number of layers
- Adaptive computation — solver uses more steps where dynamics are complex
- Invertible by construction (solve ODE backwards) — useful for normalizing flows
- Naturally models continuous-time dynamics (physics, time series)

## Deep Equilibrium Models (DEQ)

### Concept
A network with infinite depth and shared weights. The output is the fixed point of a single layer applied repeatedly (source: deep-learning/11_implicitlayers.pdf):

**h* = f(h*, x, theta)** where h* is the equilibrium point

Instead of iterating f many times, directly solve for h* using root-finding methods (e.g., Broyden's method, Anderson acceleration).

### Forward Pass
Find h* such that g(h*) = f(h*, x, theta) - h* = 0:
- Use quasi-Newton methods (Broyden's method) — doesn't require full Jacobian
- Much faster than iterating f to convergence

### Backward Pass (Implicit Differentiation)
At the fixed point, apply the implicit function theorem (source: deep-learning/11_implicitlayers.pdf):

From h* = f(h*, x, theta), differentiate both sides:

**dh*/d(theta) = (I - df/dh)^{-1} * df/d(theta)**

Compute dL/d(theta) = dL/dh* * dh*/d(theta) by solving a linear system — no need to store any intermediate iterates.

### Properties
- **O(1) memory** in both forward and backward pass
- Single set of parameters (weight-tied) — dramatic parameter reduction
- Theoretical connection: infinite-depth ResNet converges to DEQ
- Risk: fixed point may not exist or convergence may be slow

## Differentiable Optimization Layers

### Concept
Include an optimization problem as a layer in the network (source: deep-learning/11_implicitlayers.pdf):

**y* = argmin_y h(y, x, theta)** subject to g(y, x, theta) <= 0

The output y* depends on the input x and parameters theta through the optimization problem.

### Backward Pass
Use KKT conditions and implicit differentiation (source: deep-learning/11_implicitlayers.pdf):
- At optimality, KKT conditions define implicit equations relating y*, x, theta
- Differentiate KKT conditions to get dy*/d(theta) and dy*/dx
- Requires solving a linear system involving the Hessian of the Lagrangian

### Applications
- **Physics-aware networks**: embed physical constraints (conservation laws, contact mechanics)
- **Differentiable rendering**: optimization-based rendering as a layer
- **Robotics**: embed trajectory optimization or collision avoidance as differentiable layers
- **Graybox architectures**: combine learned components with optimization-based components — explainable yet end-to-end trainable

## Comparison

| | Neural ODE | DEQ | Optimization Layer |
|---|---|---|---|
| Defining equation | ODE: dh/dt = f(h,t) | Fixed point: h* = f(h*) | KKT: optimality conditions |
| Forward solver | ODE solver (RK45) | Root finding (Broyden) | QP/convex solver |
| Memory | O(1) via adjoint | O(1) via implicit diff | O(1) via implicit diff |
| Use case | Continuous dynamics | Infinite-depth nets | Constrained problems |

(source: deep-learning/11_implicitlayers.pdf)

## Connection to RL and Robotics

The final RL lecture motivates implicit layers for robotics (source: deep-learning/10_reinforcement_learning.pdf):

- Traditional robotics: white-box pipeline (SLAM + planning + control) — explainable but components trained independently
- Pure RL: black-box end-to-end — jointly optimized but not explainable
- **Graybox with implicit layers**: embed robot models, Neural ODEs, and optimization layers into a differentiable architecture — both explainable and jointly trainable via policy gradient

## Related pages

- [[deep-learning/neural-networks]]
- [[deep-learning/reinforcement-learning]]
- [[deep-learning/optimization]]
