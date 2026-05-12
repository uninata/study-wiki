# Convolutional Neural Networks

**Summary**: Comprehensive treatment of convolution operations for deep learning — standard convolution, padding/stride, pooling, receptive fields, and advanced variants (depthwise separable, transposed, dilated/atrous, deformable, octave convolutions). Focuses on the building blocks rather than specific architectures.

**Course**: deep-learning

**Sources**: 05_cnns.pdf

**Last updated**: 2026-04-15

---

## Standard Convolution

### Operation
A convolutional layer applies learned filters to local regions of the input (source: deep-learning/05_cnns.pdf):

**output[i,j] = sum_{m,n} input[i+m, j+n] * filter[m, n] + bias**

For multi-channel input (C_in channels) with C_out filters of size K x K:
- Input: H x W x C_in
- Weights: C_out x C_in x K x K
- Output: H' x W' x C_out
- Parameters: C_out * C_in * K * K + C_out

### Key Properties
- **Local connectivity**: each output neuron sees only a K x K local patch
- **Weight sharing**: same filter applied across all spatial locations
- **Translation equivariance**: shifting the input shifts the output by the same amount

### Padding and Stride
- **Padding** (p): add p zeros around the border. "Same" padding (p = (K-1)/2) preserves spatial dimensions
- **Stride** (s): step size between filter applications. Stride s reduces spatial dimensions by factor s
- Output size: **H' = floor((H + 2p - K) / s) + 1**

(source: deep-learning/05_cnns.pdf)

## Pooling

Spatial downsampling that provides some translation invariance (source: deep-learning/05_cnns.pdf):

- **Max pooling**: take the maximum value in each pooling window — most common, preserves strongest activations
- **Average pooling**: take the mean — smoother, sometimes used in final layers
- **Global average pooling (GAP)**: average over entire spatial dimension to produce a single value per channel — replaces fully connected layers at the end of modern networks
- Typical: 2x2 window with stride 2 => halves spatial dimensions

## Receptive Field

The region in the input that affects a particular output neuron (source: deep-learning/05_cnns.pdf).

For a stack of L layers each with kernel size K and stride 1:
- **Receptive field = 1 + L * (K - 1)**

With stride s, the receptive field grows faster. Deeper networks have larger receptive fields, allowing them to capture larger-scale patterns.

### 1x1 Convolutions
Despite seeing only one spatial position, 1x1 convolutions (source: deep-learning/05_cnns.pdf):
- Mix information across channels (channel-wise linear combination)
- Act as dimensionality reduction/expansion (change C_out without changing H, W)
- Used extensively in Inception modules and bottleneck blocks

## Feature Maps

Each filter produces one output channel (feature map). Early layers detect low-level features (edges, textures); deeper layers detect high-level features (parts, objects) (source: deep-learning/05_cnns.pdf).

Computational cost of a conv layer:
- **FLOPs** = H' * W' * C_out * C_in * K * K
- Both FLOPs and parameter count grow linearly with C_in * C_out

## Advanced Convolution Variants

### Depthwise Separable Convolution
Factorizes standard convolution into two steps (source: deep-learning/05_cnns.pdf):

1. **Depthwise conv**: apply one K x K filter per input channel (C_in filters, each operating on one channel)
2. **Pointwise conv**: 1x1 convolution to mix channels (C_out x C_in x 1 x 1)

- Parameters: C_in * K^2 + C_out * C_in (vs C_out * C_in * K^2 for standard)
- Reduction factor: ~1/C_out + 1/K^2 (typically 8-9x fewer parameters)
- Used in MobileNet, EfficientNet, Xception

### Transposed Convolution (Deconvolution)
Upsamples the feature map — goes from lower to higher resolution (source: deep-learning/05_cnns.pdf):

- Insert zeros between input elements (stride-1 spacing), then apply standard convolution
- Output size: H' = (H - 1) * s - 2p + K
- Used in decoder networks (U-Net, generators in GANs)
- **Checkerboard artifacts**: can occur due to uneven overlap — mitigated by using nearest-neighbor upsample + conv instead

### Dilated (Atrous) Convolution
Inserts gaps between filter elements (source: deep-learning/05_cnns.pdf):

- Dilation rate d: filter elements are spaced d apart
- Effective kernel size: K_eff = K + (K-1)(d-1)
- Increases receptive field without increasing parameters or reducing resolution
- Used in DeepLab for dense prediction, WaveNet for audio

### Deformable Convolution
Learns spatial offsets for each sampling position (source: deep-learning/05_cnns.pdf):

- Standard conv samples on a regular grid
- Deformable conv adds learnable 2D offsets to each grid position
- Offsets predicted by an auxiliary conv layer from the input feature map
- Enables the network to adapt its receptive field to object shape/scale
- Bilinear interpolation handles fractional offset positions

### Octave Convolution
Factorizes feature maps into high- and low-frequency components (source: deep-learning/05_cnns.pdf):

- Low-frequency maps stored at half resolution (saves compute)
- Information flows between high and low frequency paths
- Reduces FLOPs by ~20-30% with minimal accuracy loss

## Computational Comparison

| Variant | Parameters (relative) | Use case |
|---------|----------------------|----------|
| Standard K x K | 1x (baseline) | General purpose |
| 1x1 | K^2 x cheaper | Channel mixing, bottlenecks |
| Depthwise separable | ~K^2 x cheaper | Mobile/efficient networks |
| Dilated | Same as standard | Large receptive field, dense prediction |
| Deformable | Slightly more | Adaptive receptive field |
| Transposed | Similar to standard | Upsampling / decoder |

(source: deep-learning/05_cnns.pdf)

## Related pages

- [[deep-learning/neural-networks]]
- [[deep-learning/normalization]]
- [[deep-learning/backbone-architectures]]
- [[deep-learning/task-specific-architectures]]
- [[shared/cnn-fundamentals]]
- [[computer-vision/cnn-architectures]]
