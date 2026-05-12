# CNN Architectures for Computer Vision

**Summary**: Survey of CNN architectures from LeNet-5 to ResNet, with emphasis on design principles, training techniques, and their role in computer vision. Course-specific details on data augmentation strategies, batch normalization, and transfer learning for vision tasks.

**Course**: computer-vision

**Sources**: deep_learning_mpv_2026.pdf

**Last updated**: 2026-04-15

---

## Architecture Evolution

See [[shared/cnn-fundamentals]] for general CNN building blocks (convolutions, pooling, activations).

### LeNet-5 (1998)
First successful CNN for handwritten digit recognition. 2 conv + 2 pooling + 3 FC layers, ~60K parameters (source: computer-vision/deep_learning_mpv_2026.pdf).

### AlexNet (2012)
ImageNet breakthrough — top-5 error 16.4% vs 26.2% previous best (source: computer-vision/deep_learning_mpv_2026.pdf):
- 5 conv + 3 FC layers, ~60M parameters
- Key innovations: ReLU, dropout, data augmentation, multi-GPU training
- First demonstration that deep learning could dominate classical CV

### VGG-16/19 (2014)
"Deeper is better" with uniform 3x3 filters (source: computer-vision/deep_learning_mpv_2026.pdf):
- Two 3x3 convolutions = same receptive field as one 5x5, fewer parameters, more nonlinearity
- 138M parameters — expensive but good feature extractor for transfer learning

### GoogLeNet / Inception (2014)
Inception modules with parallel multi-scale convolutions (source: computer-vision/deep_learning_mpv_2026.pdf):
- 1x1, 3x3, 5x5 convolutions + max pooling in parallel, concatenated
- 1x1 convolutions as "bottleneck" for dimensionality reduction
- Only ~5M parameters despite 22 layers
- Auxiliary classifiers at intermediate layers for training stability

### ResNet (2015)
Residual learning: **y = F(x) + x** (source: computer-vision/deep_learning_mpv_2026.pdf):
- Skip connections allow training very deep networks (50, 101, 152 layers)
- Solves the **degradation problem**: without residuals, deeper networks paradoxically have higher training error
- **Bottleneck blocks** (1x1 → 3x3 → 1x1) for efficiency in deeper variants
- 3.57% top-5 on ImageNet (superhuman performance)

## Training Techniques for Vision

### Data Augmentation
Apply random transformations during training (source: computer-vision/deep_learning_mpv_2026.pdf):
- **Basic**: random crops, horizontal flips, color jitter, rotation
- **Scale/aspect ratio**: resize to random scale, crop to fixed size
- **Advanced**: Cutout (random erasing), Mixup (convex combination of images/labels), CutMix (paste patches between images)
- Crucial for preventing overfitting, especially with limited data

### Transfer Learning
Pre-train on ImageNet (1.2M images, 1000 classes), then adapt (source: computer-vision/deep_learning_mpv_2026.pdf):
- **Feature extraction**: freeze conv layers, retrain FC head — fast, works with small datasets
- **Fine-tuning**: unfreeze later layers, train with small learning rate — better with more data
- **Rationale**: early layers learn universal features (edges, textures, colors) transferable across tasks

### Batch Normalization
Normalize each channel's activations across the mini-batch (source: computer-vision/deep_learning_mpv_2026.pdf):
- Enables higher learning rates and faster convergence
- At test time: use running statistics accumulated during training

## ImageNet Competition Timeline

| Year | Model | Top-5 error | Key innovation |
|------|-------|------------|----------------|
| 2012 | AlexNet | 16.4% | Deep CNN + GPU |
| 2014 | VGG | 7.3% | Deeper, 3x3 only |
| 2014 | GoogLeNet | 6.7% | Inception modules |
| 2015 | ResNet | 3.57% | Residual connections |

(source: computer-vision/deep_learning_mpv_2026.pdf)

Human performance is estimated at ~5% top-5 error.

## Related pages

- [[shared/cnn-fundamentals]]
- [[computer-vision/object-detection-segmentation]]
- [[computer-vision/generative-models]]
- [[computer-vision/self-supervised-learning]]
- [[computer-vision/deep-metric-learning]]
- [[pattern-recognition/neural-networks]]
