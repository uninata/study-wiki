# Normalization

**Summary**: Normalization techniques for stabilizing and accelerating deep network training. Covers batch normalization, layer normalization, instance normalization, group normalization, weight normalization, and weight standardization — their formulations, when to use each, and practical considerations.

**Course**: deep-learning

**Sources**: 06_normalization.pdf

**Last updated**: 2026-04-15

---

## Why Normalize?

Training deep networks is hard because the distribution of each layer's inputs changes as preceding layers update their weights — a phenomenon called **internal covariate shift** (source: deep-learning/06_normalization.pdf).

Normalization techniques:
- Stabilize training by keeping activations in a well-behaved range
- Allow higher learning rates
- Reduce sensitivity to initialization
- Act as mild regularizers
- Speed up convergence

## General Normalization Framework

All activation normalization methods follow the same pattern (source: deep-learning/06_normalization.pdf):

**y = gamma * (x - mu) / sqrt(sigma^2 + epsilon) + beta**

where:
- mu, sigma^2 are mean and variance computed over some set of activations
- gamma, beta are learned scale and shift parameters (per-channel)
- epsilon is a small constant for numerical stability

The methods differ only in **which set of activations** is used to compute mu and sigma^2.

## Batch Normalization (BN)

Normalize across the **batch dimension** for each channel (source: deep-learning/06_normalization.pdf):

For a feature map of shape (N, C, H, W), compute mean and variance over **(N, H, W)** for each channel c:

**mu_c = (1/NHW) sum_{n,h,w} x_{n,c,h,w}**
**sigma^2_c = (1/NHW) sum_{n,h,w} (x_{n,c,h,w} - mu_c)^2**

### Training vs Inference
- **Training**: use batch statistics (mu, sigma^2 from current mini-batch)
- **Inference**: use running averages accumulated during training (exponential moving average)

### Advantages
- Very effective for CNNs with large batch sizes
- Strong regularization effect (each sample's normalization depends on other samples in the batch)

### Limitations
- **Batch size dependence**: statistics become noisy with small batches (< 8-16)
- **Not suitable for RNNs**: sequence lengths vary, batch statistics are ill-defined
- **Train/test discrepancy**: running averages may not match test distribution

## Layer Normalization (LN)

Normalize across **all channels and spatial dimensions** for each sample independently (source: deep-learning/06_normalization.pdf):

Compute mean and variance over **(C, H, W)** for each sample n:

**mu_n = (1/CHW) sum_{c,h,w} x_{n,c,h,w}**

### Key Properties
- No batch dependence — works with batch size 1
- No train/test discrepancy — same computation at both times
- **Default for transformers** (BERT, GPT, ViT)
- Less effective than BN for CNNs in standard settings

## Instance Normalization (IN)

Normalize across **spatial dimensions only** for each sample and channel independently (source: deep-learning/06_normalization.pdf):

Compute mean and variance over **(H, W)** for each (sample n, channel c) pair:

**mu_{n,c} = (1/HW) sum_{h,w} x_{n,c,h,w}**

### Key Properties
- Removes per-instance, per-channel contrast/style information
- **Default for style transfer** (removes style statistics)
- Used in image generation tasks

## Group Normalization (GN)

Normalize across **spatial dimensions and a group of channels** for each sample (source: deep-learning/06_normalization.pdf):

Divide C channels into G groups (each with C/G channels). Compute statistics over **(C/G, H, W)** for each (sample, group):

### Key Properties
- Interpolates between LN (G=1, one group = all channels) and IN (G=C, each channel is its own group)
- **Independent of batch size** — works well with small batches
- Effective for detection/segmentation tasks where batch sizes are small due to high-resolution inputs
- Typical choice: G = 32

## Comparison Table

| Method | Stats computed over | Batch independent? | Best for |
|--------|-------------------|--------------------|----------|
| **Batch Norm** | (N, H, W) per channel | No | CNNs with large batches |
| **Layer Norm** | (C, H, W) per sample | Yes | Transformers, RNNs |
| **Instance Norm** | (H, W) per sample+channel | Yes | Style transfer |
| **Group Norm** | (C/G, H, W) per sample+group | Yes | Detection/segmentation (small batch) |

(source: deep-learning/06_normalization.pdf)

## Weight Normalization

Instead of normalizing activations, reparameterize the weight vector (source: deep-learning/06_normalization.pdf):

**w = g * (v / ||v||)**

where v is the unnormalized weight vector and g is a learned scalar magnitude.

- Decouples the direction and magnitude of weights
- No batch dependence, no running statistics
- Cheaper than BN (no mean/variance computation)
- Less effective as a regularizer than BN

## Weight Standardization

Standardize the weights of convolutional filters (source: deep-learning/06_normalization.pdf):

**W_hat = (W - mean(W)) / std(W)**

computed over the (C_in, K, K) dimensions of each filter.

- Combined with Group Norm: achieves BN-level performance without batch dependence
- Smooths the loss landscape, enabling larger learning rates
- Particularly effective for training with very small batch sizes

## Practical Guidelines

1. **Large batch CNNs** (ImageNet classification): Batch Norm
2. **Small batch CNNs** (detection, segmentation): Group Norm (+ Weight Standardization)
3. **Transformers / NLP**: Layer Norm
4. **Style transfer / generation**: Instance Norm
5. **When in doubt**: start with Batch Norm for CNNs, Layer Norm for transformers

(source: deep-learning/06_normalization.pdf)

## Related pages

- [[deep-learning/neural-networks]]
- [[deep-learning/optimization]]
- [[deep-learning/backbone-architectures]]
- [[shared/cnn-fundamentals]]
