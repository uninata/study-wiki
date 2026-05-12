# Self-Supervised Learning

**Summary**: Learning visual representations without manual labels by designing pretext tasks or contrastive objectives. Covers pretext tasks, contrastive learning (SimCLR, MoCo, BYOL), vision transformers for SSL, DINO, and masked autoencoders (MAE).

**Course**: computer-vision

**Sources**: mpv24_ssl.pdf

**Last updated**: 2026-05-11

---

## Motivation

Labeled data is expensive; unlabeled images are abundant. Self-supervised learning (SSL) designs **proxy objectives** that create supervision from the data itself, learning transferable representations (source: computer-vision/mpv24_ssl.pdf).

## Pretext Tasks

Early SSL approaches — design a task where labels come for free (source: computer-vision/mpv24_ssl.pdf):

- **Rotation prediction**: predict which of {0°, 90°, 180°, 270°} rotation was applied
- **Jigsaw puzzle**: rearrange image patches, predict the permutation
- **Colorization**: predict color from grayscale
- **Inpainting**: predict missing image regions

These tasks force the network to learn useful visual features as a by-product. However, they introduce task-specific biases.

## Contrastive Learning

Core idea: **positive pairs** (two augmented views of the same image) should be close; **negative pairs** (views from different images) should be far apart (source: computer-vision/mpv24_ssl.pdf).

### InfoNCE Loss

$$L = -\log \frac{\exp(\text{sim}(\mathbf{z}_i, \mathbf{z}_j) / \tau)}{\sum_{k \neq i} \exp(\text{sim}(\mathbf{z}_i, \mathbf{z}_k) / \tau)}$$

where $\text{sim}$ is cosine similarity and $\tau$ is a temperature parameter. This is a softmax cross-entropy over the positive pair against all negatives.

### SimCLR (Simple Contrastive Learning of Representations)
(source: computer-vision/mpv24_ssl.pdf):
1. Apply two random augmentations to each image in a batch → $2N$ views
2. Encode with shared backbone → representations
3. Project through MLP head → embeddings $\mathbf{z}$
4. Apply InfoNCE loss: positive = two views of same image, negatives = all other views in batch

Key findings:
- **Composition of augmentations** matters most: random crop + color jitter is critical
- **Larger batches** provide more negatives → better performance
- **Projection head** (discarded after training) improves downstream quality

### MoCo (Momentum Contrast)
Decouples batch size from number of negatives (source: computer-vision/mpv24_ssl.pdf):
- Maintain a **queue** of negative embeddings (e.g., 65536)
- **Momentum encoder**: slowly updated copy of the encoder (EMA): $\boldsymbol{\theta}_k = m \cdot \boldsymbol{\theta}_k + (1 - m) \cdot \boldsymbol{\theta}_q$
- Query encoder is updated by backprop, key encoder by momentum
- Allows many negatives without huge batches

### BYOL (Bootstrap Your Own Latent)
No negatives at all (source: computer-vision/mpv24_ssl.pdf):
- **Online network**: encoder + projector + predictor
- **Target network**: encoder + projector (momentum-updated)
- Loss: minimize distance between online prediction and target projection
- Avoids collapse through the asymmetric architecture (predictor only in online branch) and momentum update

## Vision Transformers for SSL

### DINO (Self-Distillation with No Labels)
Self-supervised training of Vision Transformers (ViT) (source: computer-vision/mpv24_ssl.pdf):
- Student and teacher networks (teacher = EMA of student)
- Student sees local crops, teacher sees global crops
- **Cross-entropy loss** between student and teacher outputs (after centering + sharpening)
- Key finding: ViT features trained with DINO contain explicit **object segmentation** information — attention maps highlight objects without any segmentation supervision

### DINOv2
Scaled-up version with improved training recipe:
- Larger datasets (LVD-142M curated images)
- Better augmentations and regularization
- State-of-the-art general-purpose visual features

## Masked Autoencoders (MAE)

Inspired by masked language modeling in NLP (source: computer-vision/mpv24_ssl.pdf):

1. **Mask** a large fraction (75%) of image patches randomly
2. **Encode** only the visible patches with a ViT encoder
3. **Decode** — reconstruct the masked patches from the encoded visible patches + mask tokens using a lightweight decoder
4. **Loss**: MSE between reconstructed and original pixel values of masked patches

### Key Design Choices
- **High masking ratio** (75%): makes the task hard enough to learn useful representations; also makes training efficient (encoder processes only 25% of patches)
- **Asymmetric architecture**: heavy encoder + lightweight decoder
- The decoder is discarded after pre-training; only the encoder is used for downstream tasks

### Properties
- Scales well to very large ViTs
- Learns different features than contrastive methods — complementary
- Simpler than contrastive methods (no negatives, no momentum, no augmentation engineering)

## Method Comparison

| Method | Negatives? | Architecture | Key ingredient |
|--------|-----------|-------------|----------------|
| SimCLR | Yes (in-batch) | Siamese | Large batch, strong augmentation |
| MoCo | Yes (queue) | Momentum encoder | Queue of negatives |
| BYOL | No | Asymmetric + momentum | Predictor head |
| DINO | No | Student-teacher | Cross-entropy, centering |
| MAE | No | Encoder-decoder | High mask ratio |

## Related pages

- [[computer-vision/cnn-architectures]]
- [[computer-vision/deep-metric-learning]]
- [[computer-vision/image-retrieval]]
- [[shared/cnn-fundamentals]]
