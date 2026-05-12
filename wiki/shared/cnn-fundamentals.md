# CNN Fundamentals

**Summary**: Convolutional neural networks exploit spatial structure in data through local receptive fields, weight sharing, and pooling. This page covers core CNN building blocks shared across courses: convolution, pooling, key architectures (LeNet through ResNet), training techniques (batch norm, dropout, data augmentation), and transfer learning.

**Course**: pattern-recognition, computer-vision, deep-learning

**Sources**: neural_networks_2020.pdf, deep_learning_mpv_2026.pdf, 05_cnns.pdf, 08_backbonearchitectures.pdf, 09_taskspecificarchitectures.pdf

**Last updated**: 2026-04-15

---

## Core Building Blocks

### Convolutional Layers
Local receptive fields with shared weights provide **translation equivariance** — the same feature detector is applied at every spatial location (source: pattern-recognition/neural_networks_2020.pdf, computer-vision/deep_learning_mpv_2026.pdf):

- **Filter/kernel**: small weight matrix (e.g., 3x3, 5x5) convolved across the input
- **Feature maps**: each filter produces one output channel
- **Stride**: step size of the convolution
- **Padding**: zero-padding to control output spatial dimensions

### Pooling Layers
Spatial downsampling that provides some **translation invariance** (source: pattern-recognition/neural_networks_2020.pdf):
- **Max pooling**: take the maximum value in each local region
- **Average pooling**: take the mean value
- **Global average pooling**: reduce each feature map to a single value (used in modern architectures instead of FC layers)

### Fully Connected Layers
Final classification layers after feature extraction. Modern architectures minimize FC layers to reduce parameter count.

## Activation Functions

| Function | Formula | Properties |
|----------|---------|------------|
| ReLU | max(0, z) | Default for hidden layers, sparse, no saturation for z>0 |
| Sigmoid | 1/(1+exp(-z)) | Output (0,1), used in output layers, vanishing gradients |
| Softmax | exp(z_k)/sum_j exp(z_j) | Multi-class output, calibrated probabilities |

(source: pattern-recognition/neural_networks_2020.pdf)

## Key Architectures

### LeNet-5 (1998)
First successful CNN for digit recognition. Two convolutional + pooling layers followed by FC layers. ~60K parameters (source: computer-vision/deep_learning_mpv_2026.pdf).

### AlexNet (2012)
Won ImageNet 2012, started the deep learning revolution (source: computer-vision/deep_learning_mpv_2026.pdf):
- 8 layers (5 conv + 3 FC), ~60M parameters
- ReLU activation, dropout, data augmentation, GPU training
- Top-5 error: 16.4% (vs 26.2% previous best)

### VGG-16/19 (2014)
Uniform architecture: only 3x3 convolutions stacked deep (source: computer-vision/deep_learning_mpv_2026.pdf):
- 16-19 layers, ~138M parameters
- Key insight: two 3x3 convs have the same receptive field as one 5x5 but fewer parameters and more nonlinearity

### GoogLeNet / Inception (2014)
**Inception modules**: parallel paths with 1x1, 3x3, 5x5 convolutions and max pooling, concatenated (source: computer-vision/deep_learning_mpv_2026.pdf):
- 22 layers but only ~5M parameters (efficient design)
- 1x1 convolutions for dimensionality reduction
- Auxiliary classifiers for training deep networks

### ResNet (2015)
**Residual connections**: learn F(x) = H(x) - x instead of H(x) directly (source: computer-vision/deep_learning_mpv_2026.pdf):
- Skip connections enable training of very deep networks (50, 101, 152 layers)
- Solves the degradation problem (deeper networks performing worse than shallow ones)
- Won ImageNet 2015 with 3.57% top-5 error (superhuman)

## Training Techniques

### Batch Normalization
Normalize activations within each mini-batch to zero mean and unit variance, then apply learnable scale/shift (source: computer-vision/deep_learning_mpv_2026.pdf, pattern-recognition/neural_networks_2020.pdf):
- Reduces internal covariate shift
- Allows higher learning rates
- Acts as mild regularizer

### Dropout
Randomly zero out neurons during training with probability p (typically 0.5). At test time, scale by (1-p). Prevents co-adaptation, acts as implicit ensemble (source: pattern-recognition/neural_networks_2020.pdf).

### Data Augmentation
Generate transformed copies of training images (source: computer-vision/deep_learning_mpv_2026.pdf):
- Random crops, horizontal flips, color jitter
- Scale/aspect ratio variation
- Cutout, Mixup, CutMix (advanced)

### Transfer Learning
Pre-train on large dataset (e.g., ImageNet), then fine-tune on target task (source: computer-vision/deep_learning_mpv_2026.pdf):
- **Feature extraction**: freeze pre-trained layers, train only new classifier head
- **Fine-tuning**: unfreeze some/all layers, train with small learning rate
- Works because early layers learn general features (edges, textures)

## Optimization

- **SGD with momentum**: standard for CNN training, momentum smooths gradients
- **Adam**: adaptive per-parameter learning rates, combines momentum with second moments
- **Learning rate scheduling**: step decay, cosine annealing, warmup

(source: pattern-recognition/neural_networks_2020.pdf, computer-vision/deep_learning_mpv_2026.pdf)

## Backpropagation

Efficient gradient computation via the chain rule (source: pattern-recognition/neural_networks_2020.pdf):
1. **Forward pass**: compute all activations layer by layer
2. **Output error**: delta^(L) = dL/dh^(L) * f'(z^(L))
3. **Backward pass**: delta^(l) = (W^(l+1))^T delta^(l+1) * f'(z^(l))
4. **Gradients**: dL/dW^(l) = delta^(l) (h^(l-1))^T

Computes all gradients in O(n_parameters) time.

## Universal Approximation

A single hidden layer with sufficiently many neurons can approximate any continuous function on a compact set. However, deeper networks are often exponentially more parameter-efficient (source: pattern-recognition/neural_networks_2020.pdf).

## Related pages

- [[computer-vision/cnn-architectures]]
- [[computer-vision/object-detection-segmentation]]
- [[deep-learning/cnns]]
- [[deep-learning/backbone-architectures]]
- [[deep-learning/task-specific-architectures]]
- [[pattern-recognition/neural-networks]]
- [[pattern-recognition/logistic-regression]]
- [[pattern-recognition/perceptron]]
- [[shared/svm]]
