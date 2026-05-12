# Deep Learning — Course Overview

**Summary**: Course structure, lecture topics, and key exam themes for the Deep Learning course at CVUT. Covers the full deep learning pipeline from linear classifiers through CNNs, optimization, backbone architectures, task-specific architectures, reinforcement learning, and implicit layers.

**Course**: deep-learning

**Sources**: 01_machinelearning101.pdf, 02_linearclassifier.pdf, 03_maximumlikelihoodkldivergence.pdf, 04_neuralnetworks.pdf, 05_cnns.pdf, 06_normalization.pdf, 07_optimization.pdf, 08_backbonearchitectures.pdf, 09_taskspecificarchitectures.pdf, 10_reinforcement_learning.pdf, 11_implicitlayers.pdf

**Last updated**: 2026-04-15

---

## Lecture Overview

| # | Topic | Key Concepts |
|---|-------|-------------|
| 01 | ML Basics | Supervised/unsupervised learning, overfitting, bias-variance tradeoff, cross-validation, regularization |
| 02 | Linear Classifiers | Score functions, hinge loss, softmax/cross-entropy loss, SGD, computational graphs, automatic differentiation |
| 03 | MLE & KL Divergence | Information theory, entropy, cross-entropy, KL divergence, MLE as KL minimization, exponential family |
| 04 | Neural Networks | Activation functions (ReLU, GELU, Swish, Mish), backpropagation, weight initialization (Xavier/He), residual connections |
| 05 | CNNs | Convolution types (standard, depthwise separable, transposed, dilated, deformable), pooling, receptive fields |
| 06 | Normalization | Batch norm, layer norm, instance norm, group norm, weight norm, weight standardization |
| 07 | Optimization | Adam, AdamW, LR scheduling (cosine, warmup), weight decay vs L2, gradient clipping, label smoothing, SAM, SWA |
| 08 | Backbone Architectures | AlexNet, VGG, GoogLeNet, ResNet, ResNeXt, SENet, DenseNet, EfficientNet, ViT, MLP-Mixer, ConvNeXt, NAS |
| 09 | Task-Specific Architectures | Object detection (R-CNN, YOLO, SSD), segmentation (FCN, U-Net, DeepLab, Mask R-CNN), evaluation metrics |
| 10 | Reinforcement Learning | MDPs, value/Q functions, TD learning, DQN, DDPG, policy gradients, actor-critic, PPO, inverse RL |
| 11 | Implicit Layers | Neural ODEs, deep equilibrium models, differentiable optimization layers |

## Key Themes

- **From linear to deep**: The course builds progressively — linear classifiers (lecture 02) become neural networks (04) become CNNs (05) become full architectures (08-09)
- **Optimization is central**: Three lectures (02, 06, 07) focus on how to train networks effectively — loss functions, normalization for stable training, and optimizer design
- **Theory meets practice**: MLE/KL divergence (03) provides the statistical foundation; architectures (08-09) show real-world engineering
- **Beyond supervised learning**: Reinforcement learning (10) and implicit layers (11) extend deep learning beyond standard classification/regression

## Cross-Course Connections

- CNN fundamentals overlap with [[computer-vision/cnn-architectures]] and [[shared/cnn-fundamentals]]
- Object detection/segmentation overlaps with [[computer-vision/object-detection-segmentation]]
- Neural network basics overlap with [[pattern-recognition/neural-networks]]
- MLE connects to [[pattern-recognition/parameter-estimation]] and [[ml-fundamentals/generative-learning]]
- Linear classifiers connect to [[ml-fundamentals/linear-classifiers]] and [[pattern-recognition/logistic-regression]]

## Related pages

- [[deep-learning/linear-classifiers]]
- [[deep-learning/mle-kl-divergence]]
- [[deep-learning/neural-networks]]
- [[deep-learning/cnns]]
- [[deep-learning/normalization]]
- [[deep-learning/optimization]]
- [[deep-learning/backbone-architectures]]
- [[deep-learning/task-specific-architectures]]
- [[deep-learning/reinforcement-learning]]
- [[deep-learning/implicit-layers]]
