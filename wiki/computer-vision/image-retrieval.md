# Image Retrieval

**Summary**: Finding images similar to a query in large databases. Covers classical methods (Bag of Visual Words, VLAD, Fisher vectors), deep retrieval, large-scale search with inverted indices, spatial verification, query expansion, and zoom-in/zoom-out strategies.

**Course**: computer-vision

**Sources**: mpv25_retrieval.pdf

**Last updated**: 2026-05-11

---

## Problem Definition

Given a query image, rank all database images by visual similarity. Key challenges: scalability (millions of images), robustness to viewpoint/illumination changes, and discriminability (source: computer-vision/mpv25_retrieval.pdf).

## Bag of Visual Words (BoVW)

Analogy to text retrieval — represent images as histograms of "visual words" (source: computer-vision/mpv25_retrieval.pdf):

1. **Extract** local features (e.g., [[computer-vision/local-features|SIFT]]) from all database images
2. **Quantize**: cluster features with K-means to build a **visual vocabulary** (codebook) of $K$ visual words
3. **Assign**: map each feature to its nearest visual word
4. **Represent**: image = histogram of visual word frequencies ($K$-dimensional vector)
5. **Search**: compare histograms (e.g., cosine similarity, L1/L2 distance)

### TF-IDF Weighting
Borrowed from text retrieval (source: computer-vision/mpv25_retrieval.pdf):
- **Term frequency (TF)**: number of occurrences of word $w$ in image (normalized)
- **Inverse document frequency (IDF)**: $\log(N / n_w)$ — downweight common words
- **TF-IDF**: $\text{tf} \cdot \text{idf}$ — balances frequency and discriminability

### Vocabulary Size
- Small ($K \sim 1\text{K}$): fast, but coarse — different features land in same bin
- Large ($K \sim 1\text{M}$): discriminative, but sparse, quantization artifacts
- Typical: $K = 20\text{K}$–$200\text{K}$ for BoVW

## VLAD (Vector of Locally Aggregated Descriptors)

Instead of counting assignments, aggregate the **residuals** (source: computer-vision/mpv25_retrieval.pdf):

For each visual word $\mathbf{c}_k$, sum the residuals of all features assigned to it:

$$\mathbf{v}_k = \sum_{\mathbf{x}_i \to \mathbf{c}_k} (\mathbf{x}_i - \mathbf{c}_k)$$

Concatenate all $\mathbf{v}_k$ → VLAD vector ($K \times d$ dimensions). Apply L2 normalization (intra-normalization + full normalization).

More discriminative than BoVW with a much smaller vocabulary ($K \sim 64$–$256$).

## Fisher Vectors

Encode both first and second-order statistics of the feature distribution relative to a GMM (source: computer-vision/mpv25_retrieval.pdf):

1. Fit a GMM with $K$ components to the feature space
2. For each feature, compute soft assignments to all components
3. Aggregate weighted first-order (mean) and second-order (variance) deviations from each Gaussian

Fisher vectors are higher-dimensional than VLAD ($2Kd$) but more expressive. Apply **power normalization** (element-wise signed square root) and L2 normalization.

## Deep Retrieval

Replace hand-crafted features with CNN features (source: computer-vision/mpv25_retrieval.pdf):

### Global Descriptors
- **MAC** (Maximum Activations of Convolutions): max-pool each channel of the last conv layer
- **SPoC** (Sum-Pooled Convolutional features): average-pool each channel
- **GeM** (Generalized Mean Pooling): generalized mean between max and average, learnable parameter
- Apply PCA whitening + L2 normalization

### Fine-tuning for Retrieval
- Train with contrastive or triplet loss (see [[computer-vision/deep-metric-learning]])
- Hard negative mining from the retrieval task itself
- AP (Average Precision) loss for direct optimization of ranking

## Large-Scale Search

### Inverted Index
Map each visual word to a list of images containing it (source: computer-vision/mpv25_retrieval.pdf):
- Only score images sharing at least one visual word with the query
- Dramatically reduces search time from $O(N)$ to $O(|\text{posting lists}|)$
- Standard in million-scale retrieval systems

### Approximate Nearest Neighbors
For dense vectors (VLAD, Fisher, deep features):
- **Product quantization**: split vector into sub-vectors, quantize each independently
- **HNSW**: hierarchical navigable small-world graphs
- **IVF** (Inverted File): coarse quantization + fine search within clusters

## Spatial Verification

Re-rank top candidates using geometric consistency (source: computer-vision/mpv25_retrieval.pdf):
1. Match local features between query and candidate
2. Estimate a geometric transformation (affinity or homography) with [[computer-vision/ransac|RANSAC]]
3. Count inliers — more inliers = more geometrically consistent
4. Re-rank by number of inliers

Spatial verification is expensive but dramatically improves precision for top results.

## Query Expansion

Improve recall by expanding the query (source: computer-vision/mpv25_retrieval.pdf):
- **Average Query Expansion (AQE)**: average the descriptors of the top-k verified results with the query, re-search
- **Alpha Query Expansion**: weight by similarity score
- Iterative expansion: repeat the process multiple times

## Zoom-In / Zoom-Out

Strategies for handling scale and content mismatch (source: computer-vision/mpv25_retrieval.pdf):
- **Zoom-in**: the query shows a detail that's a small part of the database image — use local features and spatial verification
- **Zoom-out**: the query shows a scene containing the target object — detect objects or regions of interest, query with each

## Related pages

- [[computer-vision/local-features]]
- [[computer-vision/deep-metric-learning]]
- [[computer-vision/self-supervised-learning]]
- [[computer-vision/cnn-architectures]]
- [[computer-vision/ransac]]
