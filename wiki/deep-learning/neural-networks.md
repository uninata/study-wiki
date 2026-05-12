# Neural Networks

**Summary**: Feedforward neural network architecture, modern activation functions (ReLU, GELU, Swish, Mish), backpropagation via computational graphs, weight initialization strategies (Xavier, He), and techniques for addressing vanishing/exploding gradients including residual connections.

**Course**: deep-learning

**Sources**: 04_neuralnetworks.pdf

**Last updated**: 2026-04-15

---

## Architecture

A feedforward neural network computes (source: deep-learning/04_neuralnetworks.pdf):

**h^(l) = f(W^(l) h^(l-1) + b^(l))** for layers l = 1, ..., L

where f is a nonlinear activation, h^(0) = x is the input, and the output layer produces predictions.

### Universal Approximation Theorem
A single hidden layer with sufficient width can approximate any continuous function on a compact set. However, depth is exponentially more efficient — deep networks can represent functions that would require exponentially many neurons in a shallow network (source: deep-learning/04_neuralnetworks.pdf).

## Activation Functions

| Function | Formula | Properties |
|----------|---------|------------|
| **Sigmoid** | 1/(1+exp(-z)) | Output (0,1), saturates, vanishing gradients |
| **Tanh** | (exp(z)-exp(-z))/(exp(z)+exp(-z)) | Output (-1,1), zero-centered, saturates |
| **ReLU** | max(0, z) | No positive saturation, sparse, dying ReLU problem |
| **Leaky ReLU** | max(alpha*z, z), alpha=0.01 | Fixes dying ReLU, non-zero gradient for z<0 |
| **ELU** | z if z>0, alpha*(exp(z)-1) if z<=0 | Smooth, pushes mean toward zero |
| **GELU** | z * Phi(z) where Phi is Gaussian CDF | Smooth approximation of ReLU, used in transformers |
| **Swish** | z * sigmoid(beta*z) | Self-gated, smooth, non-monotonic |
| **Mish** | z * tanh(softplus(z)) | Similar to Swish, smooth, non-monotonic |

(source: deep-learning/04_neuralnetworks.pdf)

### Key Observations
- **ReLU** is the default for hidden layers — simple, fast, no saturation for positive inputs
- **GELU** is preferred in transformers (BERT, GPT) — smooth stochastic regularization interpretation
- **Swish/Mish** often slightly outperform ReLU in deep networks but are more expensive
- Sigmoid/softmax are used for output layers only (binary/multi-class classification)

## Backpropagation

Efficient computation of gradients using the chain rule on computational graphs (source: deep-learning/04_neuralnetworks.pdf):

1. **Forward pass**: compute all activations h^(l) from input to output, storing intermediate values
2. **Compute output gradient**: dL/dh^(L) from the loss function
3. **Backward pass**: for each layer l from L to 1:
   - delta^(l) = dL/dh^(l) * f'(z^(l))
   - dL/dW^(l) = delta^(l) (h^(l-1))^T
   - dL/dh^(l-1) = (W^(l))^T delta^(l)

Complexity: O(n_parameters) — same order as a single forward pass.

### Computational Graph View
Every operation is a node in a DAG. Forward pass evaluates; backward pass propagates gradients via local Jacobians. This is reverse-mode automatic differentiation — the foundation of PyTorch's `autograd` and TensorFlow's `GradientTape` (source: deep-learning/04_neuralnetworks.pdf).

## Weight Initialization

Poor initialization leads to vanishing or exploding activations/gradients (source: deep-learning/04_neuralnetworks.pdf).

### Xavier/Glorot Initialization
For layers with symmetric activations (tanh, sigmoid):

**W ~ N(0, 2/(n_in + n_out))** or **W ~ U(-sqrt(6/(n_in + n_out)), sqrt(6/(n_in + n_out)))**

Maintains variance of activations and gradients across layers.

### He/Kaiming Initialization
For ReLU activations (accounts for the factor of 2 from zeroing negative half):

**W ~ N(0, 2/n_in)**

Using He init with ReLU is the standard practice.

### Why It Matters
- Too small init: activations shrink to zero layer by layer (vanishing)
- Too large init: activations explode layer by layer (exploding)
- Correct init: activations maintain roughly constant variance across layers

## Vanishing and Exploding Gradients

### The Problem
In a deep network with L layers, the gradient of the loss w.r.t. early layers involves a product of L Jacobian matrices (source: deep-learning/04_neuralnetworks.pdf):

**dL/dh^(1) = dL/dh^(L) * prod_{l=2}^{L} dh^(l)/dh^(l-1)**

If each factor has spectral norm < 1: gradient vanishes exponentially. If > 1: gradient explodes.

### Solutions
1. **Proper initialization** (Xavier/He) — sets initial scale correctly
2. **Normalization** ([[deep-learning/normalization]]) — maintains activation statistics
3. **Residual connections** — provide gradient shortcuts
4. **Gradient clipping** — caps gradient magnitude ([[deep-learning/optimization]])
5. **Careful activation choice** — ReLU doesn't saturate for positive inputs

## Residual Connections

The key innovation from ResNet (source: deep-learning/04_neuralnetworks.pdf):

**h^(l) = h^(l-1) + F(h^(l-1))**

where F is the residual function (typically 2-3 conv layers with normalization).

### Why They Work
- Gradient flows directly through the identity path: dh^(l)/dh^(l-1) = I + dF/dh^(l-1)
- Even if dF/dh is small, the gradient is at least I — no vanishing
- The network only needs to learn the residual (deviation from identity), which is often small
- Enables training of networks with 100+ layers

See [[deep-learning/backbone-architectures]] for ResNet and its variants.

## Related pages

- [[deep-learning/linear-classifiers]]
- [[deep-learning/cnns]]
- [[deep-learning/normalization]]
- [[deep-learning/optimization]]
- [[deep-learning/backbone-architectures]]
- [[shared/cnn-fundamentals]]
- [[pattern-recognition/neural-networks]]
