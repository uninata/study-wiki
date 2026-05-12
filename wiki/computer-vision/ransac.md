# RANSAC

**Summary**: RANdom SAmple Consensus — a robust estimation framework for fitting models in the presence of outliers. Uses a hypothesis-and-verify loop with minimal sample sets. Covers the basic algorithm, termination criterion, and key variants (PROSAC, LO-RANSAC, USAC).

**Course**: computer-vision

**Sources**: 2021_ransac.pdf

**Last updated**: 2026-05-11

---

## The Problem

Standard least-squares estimation fails when data contains **outliers** (gross errors). Even a single outlier can arbitrarily distort the solution. RANSAC provides robust estimation by separating inliers from outliers (source: computer-vision/2021_ransac.pdf).

## Basic Algorithm

1. **Sample**: randomly select a minimal sample (smallest set that determines the model)
2. **Hypothesize**: fit the model to the minimal sample
3. **Verify**: count inliers — data points consistent with the model (error $< \theta$)
4. **Repeat**: keep the model with the most inliers after $k$ iterations
5. **Refine**: re-fit the model to all inliers of the best hypothesis

### Minimal Sample Sizes

| Model | Min samples | Parameters |
|-------|------------|------------|
| Line (2D) | 2 | 2 |
| Homography | 4 | 8 |
| Fundamental matrix (7-pt) | 7 | 7 |
| Fundamental matrix (8-pt) | 8 | 8 |

(source: computer-vision/2021_ransac.pdf)

## Termination Criterion

The number of iterations needed to find an all-inlier sample with probability $p$ (source: computer-vision/2021_ransac.pdf):

$$k = \frac{\log(1 - p)}{\log(1 - w^n)}$$

where $w$ is the inlier ratio and $n$ is the minimal sample size. For example, with $w = 0.5$, $n = 7$ (fundamental matrix), $p = 0.99$: $k \approx 4{,}177$ iterations.

The inlier ratio $w$ is typically unknown — estimate it adaptively from the best model found so far.

## Inlier Threshold

The threshold $\theta$ for deciding inlier/outlier is critical (source: computer-vision/2021_ransac.pdf):
- **Too small**: miss valid inliers, unstable estimation
- **Too large**: include outliers, contaminate the model
- Can be set from noise statistics: for Gaussian noise with variance $\sigma^2$, the squared error follows $\chi^2$ with $k$ degrees of freedom

## Variants

### PROSAC (Progressive Sample Consensus)
Sample from progressively larger subsets sorted by quality (e.g., descriptor match confidence) (source: computer-vision/2021_ransac.pdf):
- Start by sampling only from the most confident matches
- Gradually expand to include all correspondences
- Finds good models much faster when a quality ordering is available

### LO-RANSAC (Locally Optimized RANSAC)
When a promising model is found, apply **local optimization** before counting inliers (source: computer-vision/2021_ransac.pdf):
- Inner RANSAC loop: re-sample from the current inlier set
- Iteratively re-estimate and re-classify inliers
- Dramatically improves accuracy, especially for near-degenerate configurations

### USAC (Universal RANSAC)
Unified framework combining best practices (source: computer-vision/2021_ransac.pdf):
- PROSAC sampling
- SPRT (Sequential Probability Ratio Test) for fast model verification
- Local optimization (LO-RANSAC style)
- Degeneracy detection and handling
- State-of-the-art practical RANSAC implementation

## Degeneracy

Some data configurations make the model underdetermined (source: computer-vision/2021_ransac.pdf):
- **Homography degeneracy for fundamental matrix**: if all sampled points lie on a plane, the fundamental matrix is degenerate (a homography relates the points instead)
- **Detection**: test whether the inlier set is better explained by a simpler model
- **Handling**: fit the degenerate model, identify non-degenerate points, combine

## Applications

- **Homography estimation**: image stitching, planar object recognition
- **[[computer-vision/epipolar-geometry|Fundamental matrix estimation]]**: two-view geometry
- **Essential matrix estimation**: calibrated stereo
- **3D model fitting**: plane fitting in point clouds

## Related pages

- [[computer-vision/local-features]]
- [[computer-vision/epipolar-geometry]]
- [[computer-vision/camera-models-calibration]]
- [[computer-vision/learned-matching]]
