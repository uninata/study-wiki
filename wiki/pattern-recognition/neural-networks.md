# Neural Networks

**Summary**: Multi-layer perceptrons (MLPs) and their extensions. Covers network architecture, activation functions, backpropagation, loss functions, regularization (dropout, weight decay), optimization techniques, and a survey of applications (vision, speech, NLP, genomics).

**Course**: pattern-recognition

**Sources**: neural_networks_2020.pdf

**Last updated**: 2026-04-15

---

## Architecture

A feedforward neural network (MLP) computes (source: pattern-recognition/neural_networks_2020.pdf):

**h^(l) = f(W^(l) h^(l-1) + b^(l))** for layers l = 1, ..., L

where f is a nonlinear activation function, h^(0) = x is the input, and the final layer produces the output.

### Universal Approximation Theorem
A single hidden layer with sufficiently many neurons can approximate any continuous function on a compact set to arbitrary accuracy. However, deeper networks are often exponentially more parameter-efficient.

## Activation Functions

| Function | Formula | Properties |
|----------|---------|------------|
| **Sigmoid** | 1/(1+exp(-z)) | Output in (0,1), saturates, vanishing gradients |
| **Tanh** | (exp(z)-exp(-z))/(exp(z)+exp(-z)) | Output in (-1,1), zero-centered, still saturates |
| **ReLU** | max(0, z) | No saturation for z>0, sparse activations, "dying ReLU" problem |

(source: pattern-recognition/neural_networks_2020.pdf)

ReLU is the default choice for hidden layers in modern networks. Sigmoid/softmax are used for output layers.

## Loss Functions

- **Cross-entropy** (classification): -sum_k y_k log p_k — used with softmax output layer
- **Mean squared error** (regression): (1/n) sum_i (y_i - y_hat_i)^2
- Connection to [[pattern-recognition/logistic-regression]]: a neural network with no hidden layers and sigmoid output = logistic regression

(source: pattern-recognition/neural_networks_2020.pdf)

## Backpropagation

Algorithm for computing gradients of the loss with respect to all weights (source: pattern-recognition/neural_networks_2020.pdf):

1. **Forward pass**: compute all activations h^(l) from input to output
2. **Compute output error**: delta^(L) = dL/dh^(L) * f'(z^(L))
3. **Backward pass**: propagate errors: delta^(l) = (W^(l+1))^T delta^(l+1) * f'(z^(l))
4. **Compute gradients**: dL/dW^(l) = delta^(l) (h^(l-1))^T

This is an efficient application of the chain rule — computes all gradients in O(n_parameters) time.

## Optimization

### Gradient Descent Variants
- **Batch GD**: use all training data per update — stable but slow
- **Stochastic GD (SGD)**: use one sample per update — noisy but fast
- **Mini-batch GD**: compromise — use a batch of B samples

### Momentum
Accumulate velocity: v <- beta*v - eta*gradient, then w <- w + v. Smooths out oscillations and accelerates convergence.

### Adam
Combines momentum with adaptive per-parameter learning rates. Maintains running estimates of first and second moments of the gradient (source: pattern-recognition/neural_networks_2020.pdf).

## Regularization

### Weight Decay (L2)
Add lambda * ||W||^2 to the loss. Equivalent to a Gaussian prior on weights.

### Dropout
During training, randomly set each hidden neuron's output to zero with probability p (typically 0.5). At test time, scale weights by (1-p). This prevents co-adaptation of neurons and acts as an implicit ensemble (source: pattern-recognition/neural_networks_2020.pdf).

### Batch Normalization
Normalize activations within each mini-batch to have zero mean and unit variance. Reduces internal covariate shift, allows higher learning rates, and acts as a mild regularizer.

## Convolutional Neural Networks (CNNs)

Specialized architecture for spatial data (images) (source: pattern-recognition/neural_networks_2020.pdf):
- **Convolutional layers**: local receptive fields, shared weights, translation equivariance
- **Pooling layers**: spatial downsampling, provides some translation invariance
- **Fully connected layers**: final classification after feature extraction

State-of-art for image classification since 2012 (Krizhevsky et al.), superhuman on ImageNet since 2015.

## Recurrent Neural Networks (RNNs)

For sequential data, RNNs maintain a hidden state updated at each time step (source: pattern-recognition/neural_networks_2020.pdf):

**h_t = f(W_hh h_{t-1} + W_xh x_t + b)**

- **LSTM** (Long Short-Term Memory): gating mechanisms to control information flow, solving the vanishing gradient problem
- Applications: speech recognition, text translation, video captioning

## Applications

The neural networks lecture surveys real-world applications (source: pattern-recognition/neural_networks_2020.pdf):
- **Image classification**: ImageNet, dogs vs muffins
- **Object detection**: Faster R-CNN on COCO dataset
- **Speech recognition**: CNN architectures (Cortana, Siri)
- **Medical imaging**: anomaly detection in X-rays
- **Video captioning**: CNN + LSTM pipeline
- **Machine translation**: encoder-decoder RNNs
- **Genomics**: multi-task learning for regulatory genomics
- **Robotics**: drone navigation using deep neural networks
- **Fraud detection**: deep learning for financial anomalies

## Summary

| Pros | Cons |
|------|------|
| Flexible — classification and regression | Non-convex optimization — no global minimum guarantee |
| Multiclass via softmax | Interpretability challenges |
| Outputs calibrated probabilities | Requires large datasets and compute |
| Universal approximation | Many hyperparameters to tune |

## Related pages

- [[shared/cnn-fundamentals]]
- [[deep-learning/neural-networks]] — activations, backprop, weight init, residual connections
- [[pattern-recognition/logistic-regression]]
- [[pattern-recognition/perceptron]]
- [[pattern-recognition/pca]]
- [[pattern-recognition/adaboost]]
