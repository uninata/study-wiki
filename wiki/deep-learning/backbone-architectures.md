# Backbone Architectures

**Summary**: Evolution of deep learning backbone architectures from AlexNet (2012) to ConvNeXt (2022). Covers CNN architectures (VGG, GoogLeNet/Inception, ResNet, ResNeXt, SENet, DenseNet, EfficientNet), Neural Architecture Search, and non-CNN architectures (Vision Transformer, MLP-Mixer).

**Course**: deep-learning

**Sources**: 08_backbonearchitectures.pdf

**Last updated**: 2026-04-15

---

## Timeline and Context

ImageNet Large Scale Visual Recognition Challenge (ILSVRC) drove architecture innovation from 2012-2017. Key milestones (source: deep-learning/08_backbonearchitectures.pdf):

| Year | Architecture | Top-5 Error | Key Innovation |
|------|-------------|-------------|----------------|
| 2012 | AlexNet | 16.4% | Deep CNNs + GPU training |
| 2014 | VGG | 7.3% | Deeper with small 3x3 filters |
| 2014 | GoogLeNet | 6.7% | Inception modules (multi-scale) |
| 2015 | ResNet | 3.6% | Residual connections |
| 2017+ | SENet, NASNet | ~2% | Attention, automated search |

## AlexNet (2012)

The architecture that started the deep learning revolution (source: deep-learning/08_backbonearchitectures.pdf):
- 5 conv layers + 3 FC layers, ~60M parameters
- ReLU activation, max pooling, dropout
- Trained on 2 GPUs (model parallelism — split filters across GPUs)
- Local Response Normalization (LRN) — now obsolete, replaced by batch norm

## VGG (2014)

Design principle: use only 3x3 convolutions, go deeper (source: deep-learning/08_backbonearchitectures.pdf):
- VGG-16 (16 layers) and VGG-19 (19 layers)
- Two 3x3 convs have the same receptive field as one 5x5 but fewer parameters and more nonlinearity
- ~138M parameters (mostly in FC layers)
- Simple, uniform architecture — easy to implement and modify
- Still used as a feature extractor and perceptual loss backbone

## GoogLeNet / Inception (2014)

### Inception Module
Process input at multiple scales simultaneously (source: deep-learning/08_backbonearchitectures.pdf):
- Parallel branches: 1x1, 3x3, 5x5 convolutions + 3x3 max pooling
- 1x1 convolutions used as bottlenecks before 3x3 and 5x5 to reduce computation
- Concatenate outputs along channel dimension

### Architecture
- 22 layers deep but only ~5M parameters (vs VGG's 138M)
- No FC layers — uses Global Average Pooling
- Auxiliary classifiers at intermediate layers for training stability (later found unnecessary)

### Inception v2/v3/v4
- v2: factorize 5x5 into two 3x3 convolutions
- v3: factorize n x n into 1 x n + n x 1 (asymmetric convolutions)
- v4: combine with residual connections

## ResNet (2015)

### Residual Learning
Instead of learning h(x), learn the residual F(x) = h(x) - x (source: deep-learning/08_backbonearchitectures.pdf):

**output = F(x) + x** (identity shortcut)

### Bottleneck Block
For deeper networks (ResNet-50+), use 1x1 -> 3x3 -> 1x1 structure:
1. 1x1 conv: reduce channels (256 -> 64)
2. 3x3 conv: spatial processing (64 -> 64)
3. 1x1 conv: restore channels (64 -> 256)

### Variants
- ResNet-18, 34: basic blocks (two 3x3 convs)
- ResNet-50, 101, 152: bottleneck blocks
- Pre-activation ResNet: BN-ReLU-Conv order (improved gradient flow)

See [[deep-learning/neural-networks]] for the theory behind residual connections.

## ResNeXt (2017)

Introduces **cardinality** — number of parallel paths — as a new dimension (source: deep-learning/08_backbonearchitectures.pdf):

- Each bottleneck block splits into C parallel branches (typically C=32)
- Each branch has smaller width (e.g., 4 channels)
- Equivalent to grouped convolution
- ResNeXt-50 32x4d: 32 groups, 4 channels each
- Better accuracy than ResNet at same parameter count

## Squeeze-and-Excitation Networks (SENet, 2017)

Add **channel attention** to any architecture (source: deep-learning/08_backbonearchitectures.pdf):

1. **Squeeze**: Global average pooling to get a channel descriptor (C x 1 x 1)
2. **Excitation**: FC -> ReLU -> FC -> Sigmoid to produce channel weights
3. **Scale**: multiply each channel by its weight

- Lightweight (adds ~2.5% parameters)
- Can be inserted into any existing block (ResNet, Inception, etc.)
- Won ILSVRC 2017

## DenseNet (2017)

Connect every layer to every other layer in a dense block (source: deep-learning/08_backbonearchitectures.pdf):

**h^(l) = f([h^(0), h^(1), ..., h^(l-1)])** (concatenation, not addition)

- Each layer receives feature maps from all preceding layers
- Encourages feature reuse, reduces parameter count
- Growth rate k: each layer adds k new feature maps
- Transition layers between dense blocks: 1x1 conv + pooling for downsampling

## EfficientNet (2019)

Systematic scaling of network width, depth, and resolution (source: deep-learning/08_backbonearchitectures.pdf):

### Compound Scaling
Scale all three dimensions with a compound coefficient phi:
- Depth: d = alpha^phi
- Width: w = beta^phi  
- Resolution: r = gamma^phi
- Constraint: alpha * beta^2 * gamma^2 ≈ 2 (to ~double FLOPs)

### Base Architecture (EfficientNet-B0)
Found by Neural Architecture Search (NAS). Uses mobile inverted bottleneck blocks (MBConv) with depthwise separable convolutions and SE attention.

EfficientNet-B0 to B7: progressively larger models, each state-of-the-art at its compute level.

## Neural Architecture Search (NAS)

Automated architecture design (source: deep-learning/08_backbonearchitectures.pdf):

- **Search space**: what operations and connections to consider
- **Search strategy**: reinforcement learning, evolutionary algorithms, or differentiable search (DARTS)
- **Performance estimation**: train and evaluate candidate architectures

Challenges: enormous compute cost (thousands of GPU-hours). Solutions: weight sharing (one-shot NAS), proxy tasks.

## Vision Transformer (ViT, 2020)

Apply the transformer architecture directly to images (source: deep-learning/08_backbonearchitectures.pdf):

1. Split image into non-overlapping patches (e.g., 16x16)
2. Linearly embed each patch (flatten + linear projection)
3. Add positional embeddings
4. Feed sequence of patch embeddings through standard transformer encoder
5. Classification token [CLS] or global average pooling for output

### Key Results
- Requires large-scale pretraining (ImageNet-21k or JFT-300M) to match CNNs
- With enough data, outperforms CNNs — global self-attention captures long-range dependencies
- Less inductive bias than CNNs (no locality, no translation equivariance built in)

## MLP-Mixer (2021)

Pure MLP architecture — no convolution, no self-attention (source: deep-learning/08_backbonearchitectures.pdf):

1. Split image into patches, linearly embed
2. Alternating layers of:
   - **Token-mixing MLP**: operates across spatial positions (shared across channels)
   - **Channel-mixing MLP**: operates across channels (shared across positions)

- Competitive with CNNs and ViT when trained on large data
- Shows that the patch-based representation is key, not the specific mixing mechanism

## ConvNeXt (2022)

Modernize ResNet with design choices from transformers (source: deep-learning/08_backbonearchitectures.pdf):

Changes applied to ResNet-50:
1. Macro design: use (3, 3, 9, 3) stage ratio (like Swin Transformer)
2. Patchify stem: non-overlapping 4x4 conv with stride 4 (like ViT)
3. Depthwise separable convolutions
4. Inverted bottleneck (expand channels in middle)
5. Larger kernel (7x7 depthwise conv)
6. Layer Norm instead of Batch Norm
7. GELU activation, fewer normalization layers

Result: matches or exceeds Swin Transformer accuracy with pure convolutions — the "convolutions are not dead" message.

## Related pages

- [[deep-learning/cnns]]
- [[deep-learning/normalization]]
- [[deep-learning/optimization]]
- [[deep-learning/neural-networks]]
- [[deep-learning/task-specific-architectures]]
- [[shared/cnn-fundamentals]]
- [[computer-vision/cnn-architectures]]
