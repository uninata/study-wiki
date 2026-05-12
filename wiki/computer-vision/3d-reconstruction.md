# 3D Reconstruction

**Summary**: Recovering 3D structure from images. Covers stereo vision, triangulation, Structure from Motion (SfM) pipeline, bundle adjustment, dense reconstruction, and modern learning-based methods (DUSt3R, MASt3R, VGGT, Depth Anything).

**Course**: computer-vision

**Sources**: mpv-08-2026-04-13_3d_reconstruction_part_2.pdf, cvm-07-2026-03-30-3d_reconstruction_part_1.pdf

**Last updated**: 2026-05-11

---

## Triangulation

Given corresponding points in two calibrated views, recover the 3D point (source: computer-vision/mpv-08-2026-04-13_3d_reconstruction_part_2.pdf):

**Linear triangulation**: set up a system from $\mathbf{x} = \mathbf{P} \mathbf{X}$ and $\mathbf{x}' = \mathbf{P}' \mathbf{X}$, solve via SVD.

In practice, rays don't intersect exactly due to noise. **Mid-point method**: find the point minimizing the sum of squared distances to both rays. Or use **optimal triangulation**: minimize reprojection error.

## Stereo Vision

Two cameras with known relative pose (source: computer-vision/mpv-08-2026-04-13_3d_reconstruction_part_2.pdf):

1. **Rectify** image pair so epipolar lines are horizontal
2. **Match** corresponding points along scanlines (disparity search)
3. **Triangulate**: depth $Z = fB/d$ (focal length $\times$ baseline / disparity)

### Dense Stereo Methods
- **Block matching**: compare windows along scanlines, winner-take-all
- **Semi-global matching (SGM)**: aggregate costs along 8-16 directions with smoothness penalty
- **Learned stereo**: cost volume + 3D convolutions (GC-Net, PSMNet, RAFT-Stereo)

## Structure from Motion (SfM)

Recover camera poses and 3D structure simultaneously from an unordered image collection (source: computer-vision/mpv-08-2026-04-13_3d_reconstruction_part_2.pdf):

### Incremental SfM Pipeline
1. **Feature extraction**: detect and describe features in all images ([[computer-vision/local-features|SIFT]], SuperPoint)
2. **Feature matching**: match features between image pairs
3. **Geometric verification**: estimate fundamental/essential matrices with [[computer-vision/ransac|RANSAC]], reject inconsistent matches
4. **Initialization**: select a good initial pair, recover relative pose, triangulate initial points
5. **Incremental registration**: add new images one at a time:
   - Find 2D-3D correspondences (image features ↔ existing 3D points)
   - Estimate camera pose via PnP (Perspective-n-Point) + RANSAC
   - Triangulate new 3D points from new correspondences
6. **Bundle adjustment**: refine all parameters jointly

### Global SfM
Alternative approach: estimate all relative rotations first, then solve for all camera poses simultaneously. Less prone to drift but more sensitive to outliers.

## Bundle Adjustment

The gold-standard refinement step — simultaneously optimize all camera parameters and 3D point positions by minimizing **total reprojection error** (source: computer-vision/mpv-08-2026-04-13_3d_reconstruction_part_2.pdf):

$$\min \sum_i \sum_j \|\mathbf{x}_{ij} - \pi(\mathbf{P}_i, \mathbf{X}_j)\|^2$$

where $\pi$ is the projection function, $\mathbf{P}_i$ are camera parameters, and $\mathbf{X}_j$ are 3D points.

### Properties:
- Nonlinear least squares — solved with **Levenberg-Marquardt**
- The Jacobian has a **sparse block structure** (each observation involves only one camera and one point)
- Exploit sparsity via the **Schur complement trick**: eliminate 3D points, solve for cameras, then back-substitute
- Used both during SfM and as a final refinement step

## Multi-View Stereo (MVS)

Dense 3D reconstruction from multiple calibrated views (source: computer-vision/mpv-08-2026-04-13_3d_reconstruction_part_2.pdf):
- **Depth map fusion**: estimate per-view depth maps, then merge into a consistent 3D model
- **Volumetric methods**: discretize space into voxels, evaluate photo-consistency
- **Point cloud to mesh**: Poisson surface reconstruction, Delaunay triangulation

## Learning-Based 3D Reconstruction

Modern deep learning approaches that bypass or augment the classical pipeline (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):

### DUSt3R
- Predicts dense 3D point maps directly from image pairs
- No camera calibration required — learns to predict geometry end-to-end
- Trained on large-scale multi-view datasets

### MASt3R
- Extends DUSt3R with local feature matching capability
- Combines dense 3D prediction with sparse feature correspondences
- Enables both reconstruction and matching in one model

### VGGT (Visual Geometry Grounded Transformer)
- End-to-end multi-view 3D reconstruction from arbitrary image sets
- Transformer architecture processes multiple views jointly
- Predicts cameras, depths, and point maps simultaneously

### Depth Anything (v1/v2/v3)
- Monocular depth estimation (single image → depth map)
- Trained on massive unlabeled datasets with semi-supervised learning
- v3: metric depth with improved accuracy and generalization

## Related pages

- [[computer-vision/camera-models-calibration]]
- [[computer-vision/epipolar-geometry]]
- [[computer-vision/ransac]]
- [[computer-vision/local-features]]
- [[computer-vision/learned-matching]]
