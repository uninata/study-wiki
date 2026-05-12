# Epipolar Geometry

**Summary**: The geometry of two views of the same scene. The fundamental matrix $\mathbf{F}$ encodes the epipolar constraint: corresponding points satisfy $\mathbf{x}'^\top \mathbf{F} \mathbf{x} = 0$. Covers the fundamental matrix, essential matrix, 7-point and 8-point algorithms, stereo rectification, and the correspondence problem.

**Course**: computer-vision

**Sources**: courses_mpv_labs_2_correspondence_problem_start.pdf, cvm-07-2026-03-30-3d_reconstruction_part_1.pdf, mpv-08-2026-04-13_3d_reconstruction_part_2.pdf

**Last updated**: 2026-05-11

---

## The Epipolar Constraint

Given two camera views, a point $\mathbf{x}$ in image 1 constrains its correspondence $\mathbf{x}'$ in image 2 to lie on an **epipolar line** (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):

$$\mathbf{x}'^\top \mathbf{F} \mathbf{x} = 0$$

where $\mathbf{F}$ is the $3 \times 3$ **fundamental matrix** (rank 2, 7 DoF).

### Key Concepts

- **Epipole**: projection of one camera center into the other image ($\mathbf{F} \mathbf{e} = 0$, $\mathbf{F}^\top \mathbf{e}' = 0$)
- **Epipolar line**: $\mathbf{l}' = \mathbf{F} \mathbf{x}$ (the line in image 2 where the correspondence of $\mathbf{x}$ must lie)
- **Epipolar plane**: plane through the two camera centers and the 3D point

## Fundamental Matrix

### Properties (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):
- $3 \times 3$ matrix, rank 2 ($\det(\mathbf{F}) = 0$)
- 7 degrees of freedom (9 entries - 1 scale - 1 rank constraint)
- Encodes the relative geometry of two uncalibrated cameras
- $\mathbf{F} = (\mathbf{K}')^{-\top} \mathbf{E} \mathbf{K}^{-1}$, where $\mathbf{K}$, $\mathbf{K}'$ are intrinsic matrices and $\mathbf{E}$ is the essential matrix

### 8-Point Algorithm
The simplest estimation method (source: computer-vision/courses_mpv_labs_2_correspondence_problem_start.pdf):
1. Set up linear system from $\mathbf{x}'^\top \mathbf{F} \mathbf{x} = 0$ for $\geq 8$ point correspondences
2. Solve via SVD of the constraint matrix $\mathbf{A}$: $\mathbf{F}$ is the last column of $\mathbf{V}$
3. **Enforce rank 2**: SVD of $\mathbf{F}$, set smallest singular value to zero
4. **Normalization is critical**: use Hartley's normalization (translate centroid to origin, scale so average distance $= \sqrt{2}$) — the "normalized 8-point algorithm"

### 7-Point Algorithm
Uses the minimum number of correspondences (source: computer-vision/courses_mpv_labs_2_correspondence_problem_start.pdf):
1. 7 equations give a 2D null space: $\mathbf{F} = \alpha \mathbf{F}_1 + (1-\alpha) \mathbf{F}_2$
2. Enforce $\det(\mathbf{F}) = 0$ $\Rightarrow$ cubic in $\alpha$ $\Rightarrow$ up to 3 real solutions
3. Used as the minimal solver inside [[computer-vision/ransac|RANSAC]]

## Essential Matrix

For **calibrated cameras** (known intrinsics $\mathbf{K}$, $\mathbf{K}'$), the essential matrix $\mathbf{E}$ relates normalized image coordinates (source: computer-vision/cvm-07-2026-03-30-3d_reconstruction_part_1.pdf):

$$\mathbf{E} = [\mathbf{t}]_\times \mathbf{R}$$

where $\mathbf{R}$ is the relative rotation and $\mathbf{t}$ is the relative translation (up to scale).

### Properties:
- 5 DoF (3 rotation + 2 translation direction)
- Two singular values are equal, one is zero
- **5-point algorithm**: minimal solver for calibrated cameras (Nistér, 2004) — up to 10 solutions

### Recovering $\mathbf{R}$, $\mathbf{t}$ from $\mathbf{E}$
SVD of $\mathbf{E}$ gives 4 possible $(\mathbf{R}, \mathbf{t})$ pairs. Only one places points in front of both cameras — select by **cheirality check** (positive depth).

## Stereo Matching

Given $\mathbf{F}$ (or after rectification), find correspondences between images (source: computer-vision/mpv-08-2026-04-13_3d_reconstruction_part_2.pdf):

### Rectification
Transform image pairs so epipolar lines are horizontal scanlines:
- Simplifies correspondence search to 1D
- Applied via homographies $\mathbf{H}$, $\mathbf{H}'$ that map epipoles to infinity

### Matching Methods
- **Block matching**: compare fixed-size windows along scanlines
- **Semi-global matching (SGM)**: aggregate matching costs along multiple directions
- **Deep stereo**: learned cost volumes (e.g., PSMNet, RAFT-Stereo)

### Disparity
For rectified images, the disparity $d = x - x'$ is inversely proportional to depth: $Z = fB/d$, where $f$ is focal length and $B$ is baseline.

## Related pages

- [[computer-vision/ransac]]
- [[computer-vision/camera-models-calibration]]
- [[computer-vision/3d-reconstruction]]
- [[computer-vision/local-features]]
- [[computer-vision/learned-matching]]
