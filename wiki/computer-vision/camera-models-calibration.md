# Camera Models and Calibration

**Summary**: The pinhole camera model, projective geometry, and camera calibration. Covers homogeneous coordinates, the projection matrix $\mathbf{P} = \mathbf{K}[\mathbf{R}|\mathbf{t}]$, intrinsic/extrinsic parameters, DLT calibration, Zhang's method, and radial distortion correction.

**Course**: computer-vision

**Sources**: cvm-07-2026-03-30-3d_reconstruction_part_1.pdf

**Last updated**: 2026-05-11

---

## Pinhole Camera Model

A 3D point $\mathbf{X}$ is projected to a 2D image point $\mathbf{x}$ by (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):

$$\mathbf{x} = \mathbf{P} \mathbf{X}$$

where $\mathbf{P}$ is the $3 \times 4$ **projection matrix** and we use **homogeneous coordinates** (equality up to scale).

### Homogeneous Coordinates
- 2D point $(x, y) \Rightarrow (x, y, 1)$ or any scalar multiple $(kx, ky, k)$
- 3D point $(X, Y, Z) \Rightarrow (X, Y, Z, 1)$ or any scalar multiple
- Lines and planes are also represented in homogeneous form
- Enables projective transformations as matrix multiplications

## Projection Matrix Decomposition

$$\mathbf{P} = \mathbf{K} [\mathbf{R} | \mathbf{t}]$$

where (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):

### Intrinsic Parameters ($\mathbf{K}$)
The $3 \times 3$ upper-triangular **calibration matrix**:

$$\mathbf{K} = \begin{bmatrix} f_x & s & c_x \\ 0 & f_y & c_y \\ 0 & 0 & 1 \end{bmatrix}$$

- **$f_x, f_y$**: focal length in pixels (may differ due to non-square pixels)
- **$(c_x, c_y)$**: principal point (intersection of optical axis with image plane)
- **$s$**: skew parameter (usually 0 for modern cameras)

### Extrinsic Parameters ($[\mathbf{R} | \mathbf{t}]$)
- **$\mathbf{R}$**: $3 \times 3$ rotation matrix (camera orientation in world frame)
- **$\mathbf{t}$**: $3 \times 1$ translation vector (camera position: $\mathbf{C} = -\mathbf{R}^\top \mathbf{t}$)

Total: 11 DoF (5 intrinsic + 6 extrinsic) for a general pinhole camera.

## Camera Calibration

### DLT (Direct Linear Transform)
Estimate $\mathbf{P}$ from $\geq 6$ known 3D-2D point correspondences (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):
1. Each correspondence gives 2 linear equations in the 12 entries of $\mathbf{P}$
2. Stack into a matrix $\mathbf{A}$ ($2n \times 12$), solve via SVD: $\mathbf{P}$ is the last column of $\mathbf{V}$
3. Extract $\mathbf{K}$, $\mathbf{R}$, $\mathbf{t}$ from $\mathbf{P}$ via RQ decomposition

### Zhang's Method (Flexible Calibration)
Uses a planar calibration pattern (e.g., checkerboard) viewed from multiple angles (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):
1. Each view of the plane gives a **homography** $\mathbf{H}_i$ relating pattern to image coordinates
2. Homographies constrain intrinsics via: $\mathbf{H}_i^\top \mathbf{K}^{-\top} \mathbf{K}^{-1} \mathbf{H}_i$ equations
3. Solve for $\mathbf{K}$ from $\geq 3$ views, then recover $\mathbf{R}_i$, $\mathbf{t}_i$ for each view
4. Refine all parameters via **bundle adjustment** (nonlinear least squares)

**Advantages**: no special calibration rig, just a printed pattern; widely used in practice (OpenCV `calibrateCamera`).

## Radial Distortion

Real lenses introduce nonlinear distortion, primarily radial (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):

$$x_{\text{distorted}} = x_{\text{undistorted}} (1 + k_1 r^2 + k_2 r^4 + \ldots)$$

where $r$ is the distance from the principal point. Parameters $k_1, k_2, \ldots$ are estimated during calibration (typically 2-3 coefficients suffice).

- **Barrel distortion**: $k_1 < 0$ (straight lines curve outward)
- **Pincushion distortion**: $k_1 > 0$ (straight lines curve inward)

## Learning-Based Camera Methods

Recent approaches use deep learning for tasks traditionally requiring explicit calibration (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):

- **DUSt3R**: directly predicts 3D point maps from image pairs without camera parameters
- **MASt3R**: extends DUSt3R with local feature matching capabilities
- **VGGT**: Visual Geometry Grounded Transformer — end-to-end 3D from multiple images
- **Depth Anything v1/v2/v3**: monocular depth estimation at scale, trained on massive datasets

These methods bypass the classical pipeline (calibrate → match → triangulate) by learning geometry end-to-end.

## Related pages

- [[computer-vision/epipolar-geometry]]
- [[computer-vision/3d-reconstruction]]
- [[computer-vision/ransac]]
- [[computer-vision/local-features]]
