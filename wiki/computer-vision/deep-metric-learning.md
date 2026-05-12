# Deep Metric Learning

**Summary**: Learning embedding spaces where distances reflect semantic similarity. Covers contrastive loss, triplet loss, hard negative mining, and angular margin losses (ArcFace, CosFace) for face recognition and verification.

**Course**: computer-vision

**Sources**: mpv24_deepmetriclearning.pdf

**Last updated**: 2026-05-11

---

## Motivation

Instead of classifying into fixed classes, learn an **embedding function** $f: \mathcal{X} \to \mathbb{R}^d$ such that similar inputs map to nearby points and dissimilar inputs map to distant points (source: computer-vision/mpv24_deepmetriclearning.pdf).

Key advantage: generalizes to unseen classes at test time (zero-shot / few-shot) — no retraining needed.

## Contrastive Loss

Trains on pairs $(\mathbf{x}_i, \mathbf{x}_j)$ with a label indicating same/different (source: computer-vision/mpv24_deepmetriclearning.pdf):

$$L = (1-y) \cdot \frac{1}{2} d^2 + y \cdot \frac{1}{2} \max(0, m - d)^2$$

where $d = \|f(\mathbf{x}_i) - f(\mathbf{x}_j)\|$, $y = 0$ for positive pairs, $y = 1$ for negative pairs, and $m$ is the margin.

- **Positive pairs**: minimize distance
- **Negative pairs**: push apart, but only if closer than margin $m$

## Triplet Loss

Trains on triplets (anchor, positive, negative) (source: computer-vision/mpv24_deepmetriclearning.pdf):

$$L = \max\left(0,\; \|f(\mathbf{a}) - f(\mathbf{p})\|^2 - \|f(\mathbf{a}) - f(\mathbf{n})\|^2 + m\right)$$

Push the anchor closer to the positive than to the negative by at least margin $m$.

### Hard Negative Mining
Random triplets quickly become uninformative (loss = 0). Effective training requires (source: computer-vision/mpv24_deepmetriclearning.pdf):
- **Hard negatives**: negatives closest to the anchor (most confusing)
- **Semi-hard negatives**: negatives farther than the positive but within the margin
- **Online mining**: compute all pairwise distances within a mini-batch, select the hardest triplets
- Batch construction: ensure each identity appears multiple times per batch

## Angular Margin Losses

Modern losses that operate on the angle between embedding vectors (source: computer-vision/mpv24_deepmetriclearning.pdf):

### Normalized Softmax
First normalize both features and classifier weights to unit norm, then apply softmax with a temperature/scale parameter $s$:

$$L = -\log \frac{\exp(s \cos(\theta_{y_i}))}{\sum_j \exp(s \cos(\theta_j))}$$

### ArcFace (Additive Angular Margin)
Add an angular margin penalty $m$ to the target angle (source: computer-vision/mpv24_deepmetriclearning.pdf):

$$L = -\log \frac{\exp(s \cos(\theta_{y_i} + m))}{\exp(s \cos(\theta_{y_i} + m)) + \sum_{j \neq y_i} \exp(s \cos(\theta_j))}$$

Forces a larger angular gap between classes — more discriminative embeddings.

### CosFace (Additive Cosine Margin)
Subtract a margin from the target cosine similarity:

$$L = -\log \frac{\exp(s(\cos(\theta_{y_i}) - m))}{\exp(s(\cos(\theta_{y_i}) - m)) + \sum_{j \neq y_i} \exp(s \cos(\theta_j))}$$

### Comparison
| Loss | Margin type | Geometry |
|------|-----------|----------|
| Softmax | None | No explicit separation |
| ArcFace | Additive angular | Geodesic margin on hypersphere |
| CosFace | Additive cosine | Cosine margin |
| SphereFace | Multiplicative angular | Multiplicative penalty |

## Face Recognition and Verification

Primary application of deep metric learning (source: computer-vision/mpv24_deepmetriclearning.pdf):

### Verification (1:1)
Given two images, decide if they're the same person:
- Compute embeddings, compare distance against threshold
- Metrics: True Accept Rate (TAR) at fixed False Accept Rate (FAR)

### Identification (1:N)
Given a probe image, find the identity in a gallery:
- Compute embedding of probe, find nearest neighbor in gallery embeddings
- Metrics: Rank-1 accuracy, CMC curve

### Training at Scale
- Large-scale datasets: MS-Celeb-1M (~10M images, ~100K identities)
- ArcFace/CosFace enable training with very large numbers of classes
- **$L_2$-normalization** of embeddings: all embeddings live on a hypersphere

## Related pages

- [[computer-vision/cnn-architectures]]
- [[computer-vision/self-supervised-learning]]
- [[computer-vision/image-retrieval]]
- [[shared/cnn-fundamentals]]
