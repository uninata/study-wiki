# Local Features

**Summary**: Local feature detection and description pipeline: interest point detection (Harris, scale-space, blob detection), descriptor computation (SIFT, BRIEF, ORB), and affine covariant regions (MSER). Covers the detect-describe-match paradigm that underpins classical computer vision.

**Course**: computer-vision

**Sources**: MPV2026_local_features_lctures_1_2.pdf

**Last updated**: 2026-05-11

---

## Interest Point Detection

### Harris Corner Detector

Detects points where the image intensity changes significantly in multiple directions (source: computer-vision/MPV2026_local_features_lctures_1_2.pdf):

1. Compute the **structure tensor** (second moment matrix):

$$\mathbf{M}(\mathbf{x}) = \sum_{(u,v) \in W} w(u,v) \begin{bmatrix} I_x^2 & I_x I_y \\ I_x I_y & I_y^2 \end{bmatrix}$$

   where $I_x$, $I_y$ are image gradients and $w$ is a Gaussian weighting window.

2. Analyze eigenvalues $\lambda_1$, $\lambda_2$ of $\mathbf{M}$:
   - Both large $\Rightarrow$ **corner** (intensity changes in two directions)
   - One large, one small $\Rightarrow$ **edge**
   - Both small $\Rightarrow$ **flat region**

3. **Harris response**: $R = \det(\mathbf{M}) - \kappa \, \text{tr}(\mathbf{M})^2$, where $\kappa \sim 0.04$–$0.06$. Avoids explicit eigenvalue computation since $\det(\mathbf{M}) = \lambda_1 \lambda_2$ and $\text{tr}(\mathbf{M}) = \lambda_1 + \lambda_2$.

4. Non-maximum suppression to select final corner points.

**Properties**: rotation invariant (eigenvalues don't change), NOT scale invariant.

### Scale-Space

To achieve **scale invariance**, analyze features across multiple scales (source: computer-vision/MPV2026_local_features_lctures_1_2.pdf):

- **Gaussian scale-space**: $L(x, y, \sigma) = G(x, y, \sigma) * I(x, y)$, where $G$ is a Gaussian with standard deviation $\sigma$
- **Automatic scale selection**: detect features at the scale where a normalized response function achieves a maximum across scales
- **Characteristic scale**: the scale $\sigma$ at which the feature response is strongest

### Blob Detection

- **Laplacian of Gaussian (LoG)**: $\sigma^2 \nabla^2 G$ detects blob-like structures. Peaks in scale-space indicate blobs at characteristic scales.
- **Difference of Gaussians (DoG)**: efficient approximation: $\text{DoG} = G(\sigma_2) - G(\sigma_1) \approx (\sigma_2 - \sigma_1) \, \sigma \, \nabla^2 G$. Used in SIFT.

## SIFT (Scale-Invariant Feature Transform)

Complete pipeline: detection + description (source: computer-vision/MPV2026_local_features_lctures_1_2.pdf):

### Keypoint Detection
1. Build DoG pyramid: compute DoG images at multiple octaves and scales
2. Find extrema: compare each point to 26 neighbors (8 spatial + 9 above + 9 below)
3. Sub-pixel localization via Taylor expansion
4. Reject low-contrast points and edge responses (ratio of principal curvatures)

### Orientation Assignment
1. Compute gradient orientations in a region around the keypoint
2. Build a 36-bin orientation histogram weighted by gradient magnitude
3. Assign dominant orientation (peak of histogram) — provides **rotation invariance**
4. Multiple orientations $\Rightarrow$ multiple keypoints at same location

### SIFT Descriptor (128-dimensional)
1. Take a 16x16 patch around the keypoint, aligned to dominant orientation
2. Divide into 4x4 grid of sub-regions
3. In each sub-region, compute an 8-bin gradient orientation histogram
4. Concatenate: 4 x 4 x 8 = **128 dimensions**
5. Normalize to unit length, clip values > 0.2, re-normalize

**Properties**: invariant to rotation, robust to scale changes, moderate illumination changes, and small viewpoint changes.

## Binary Descriptors

### BRIEF (Binary Robust Independent Elementary Features)
Compare pairs of pixel intensities in a patch (source: computer-vision/MPV2026_local_features_lctures_1_2.pdf):
- Binary string: bit $k = 1$ if $I(p_k) > I(q_k)$, else 0
- Matching via **Hamming distance** (XOR + popcount) — extremely fast
- Not rotation invariant

### ORB (Oriented FAST + Rotated BRIEF)
- FAST keypoint detector + orientation from intensity centroid
- Rotate BRIEF pattern by keypoint orientation $\Rightarrow$ **rotation invariant**
- Learned pair selection for decorrelated, high-variance bits

## Affine Covariant Regions

### MSER (Maximally Stable Extremal Regions)
Connected components that are stable over a range of intensity thresholds (source: computer-vision/MPV2026_local_features_lctures_1_2.pdf):
- Threshold image at all levels, track connected components
- Select regions whose area changes minimally:

$$q(i) = \frac{|R_{i+\Delta} - R_{i-\Delta}|}{|R_i|}$$

- Affine covariant: MSER boundaries transform covariantly under affine image transformations

### Affine Adaptation
Iteratively estimate the local affine shape of a feature region by fitting an ellipse to the second moment matrix. Normalizes the region to a canonical circle for descriptor computation.

## Descriptor Evaluation

Performance is measured by (source: computer-vision/MPV2026_local_features_lctures_1_2.pdf):
- **Repeatability**: fraction of features detected in both images of a pair
- **Matching score**: fraction of correctly matched features
- **ROC curves**: true positive rate vs false positive rate at varying thresholds
- **1-precision vs recall**: standard retrieval metric adapted for feature matching

## Related pages

- [[computer-vision/ransac]]
- [[computer-vision/learned-matching]]
- [[computer-vision/epipolar-geometry]]
- [[computer-vision/image-retrieval]]
