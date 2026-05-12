# Visual Tracking

**Summary**: Methods for tracking objects across video frames. Covers the KLT tracker (Lucas-Kanade optical flow), KCF (kernelized correlation filters), TLD (Tracking-Learning-Detection with P-N learning), and modern tracking by segmentation (VOS, SAM-based).

**Course**: computer-vision

**Sources**: matas-2018.04-klt-only.pdf, kcf_lecture2016.pdf, tld.pdf, tracking_by_segmentation.pdf

**Last updated**: 2026-05-11

---

## KLT Tracker (Kanade-Lucas-Tomasi)

Template-based tracking using optical flow (source: computer-vision/matas-2018.04-klt-only.pdf):

### Lucas-Kanade Optical Flow
Assumes **brightness constancy** and **small motion**: $I(x, y, t) = I(x + u, y + v, t + 1)$.

First-order Taylor expansion gives:

$$I_x u + I_y v + I_t = 0$$

(optical flow constraint equation)

One equation, two unknowns — **aperture problem**. Lucas-Kanade solves by assuming constant flow in a local window, solving the overdetermined system via least squares:

$$\begin{bmatrix} u \\ v \end{bmatrix} = (\mathbf{A}^\top \mathbf{A})^{-1} \mathbf{A}^\top \mathbf{b}$$

where $\mathbf{A}$ contains spatial gradients and $\mathbf{b}$ contains temporal gradients for all pixels in the window.

### Iterative Refinement
For larger displacements (source: computer-vision/matas-2018.04-klt-only.pdf):
1. Warp the template toward the current estimate
2. Recompute the flow residual
3. Repeat until convergence

### Pyramidal KLT
Build image pyramids, estimate flow coarse-to-fine — handles larger displacements while maintaining precision.

### Affine Motion Model
Extend to 6-parameter affine transformation for more complex motions (rotation, scaling, shearing) instead of pure translation.

### Good Features to Track
The matrix $\mathbf{A}^\top \mathbf{A}$ (same structure as Harris matrix) must be well-conditioned. Use eigenvalue criterion: both eigenvalues $> \theta$ — these are the "good features to track" (Shi-Tomasi corners).

## KCF (Kernelized Correlation Filters)

Fast discriminative tracker using correlation in the frequency domain (source: computer-vision/kcf_lecture2016.pdf):

### Core Idea
Train a ridge regression classifier to distinguish the target from its surroundings. Exploit the structure of **circulant matrices** to do this efficiently via DFT.

### Circulant Matrices and DFT
All cyclic shifts of the training patch form a circulant matrix $\mathbf{C}$. Key property (source: computer-vision/kcf_lecture2016.pdf):

$$\mathbf{C} = \mathbf{F} \, \text{diag}(\mathbf{F}\mathbf{x}) \, \mathbf{F}^H$$

(diagonalized by DFT)

This means ridge regression $\mathbf{w} = (\mathbf{C}^\top \mathbf{C} + \lambda \mathbf{I})^{-1} \mathbf{C}^\top \mathbf{y}$ can be solved element-wise in the Fourier domain:

$$\mathbf{W} = \frac{\mathbf{Y}^* \odot \mathbf{X}}{\mathbf{X}^* \odot \mathbf{X} + \lambda}$$

where capitals denote DFTs and $\odot$ is element-wise multiplication.

### Kernelized Extension
Replace dot products with a kernel (e.g., RBF). The kernel matrix of all cyclic shifts is also circulant, so the same DFT trick applies (source: computer-vision/kcf_lecture2016.pdf):

$$\boldsymbol{\alpha} = \frac{\mathbf{Y}}{\mathbf{K}^{xx} + \lambda}$$

Detection:

$$f(\mathbf{z}) = \mathbf{K}^{xz} \odot \boldsymbol{\alpha}$$

(all in frequency domain)

### HOG Features
Use HOG (Histogram of Oriented Gradients) features instead of raw pixels for better robustness — multi-channel extension of the correlation filter framework.

### Properties
- Very fast: FFT-based, runs at hundreds of FPS
- Handles scale changes poorly (fixed template size)
- No mechanism for re-detection after tracking failure

## TLD (Tracking-Learning-Detection)

Long-term tracking framework that combines a tracker, detector, and online learning (source: computer-vision/tld.pdf):

### Three Components
1. **Tracker**: median-flow tracker (tracks sparse points, detects failure via forward-backward consistency)
2. **Detector**: scanning-window classifier (cascade: variance filter → ensemble of ferns → nearest-neighbor)
3. **Integrator**: combines tracker and detector outputs

### P-N Learning
Online semi-supervised learning from video (source: computer-vision/tld.pdf):
- **P-expert** (positive): uses structural constraints (tracker trajectory) to find missed detections → adds positive examples
- **N-expert** (negative): uses multi-scale constraint (object can only be at one location) → identifies false positives
- Iteratively improves the detector as tracking progresses

### Properties
- Handles full occlusion and re-appearance (detector can re-acquire target)
- Self-correcting through P-N learning feedback loop
- Can drift if P-N learning introduces errors (error accumulation)

## Tracking by Segmentation

Modern approach: track objects as segmentation masks rather than bounding boxes (source: computer-vision/tracking_by_segmentation.pdf):

### Video Object Segmentation (VOS)
- **Semi-supervised**: given first-frame mask, propagate to subsequent frames
- **Memory-based**: store features from past frames, match to current frame
- Key methods: STM (Space-Time Memory), XMem, Cutie

### SAM-Based Tracking
Leveraging the Segment Anything Model for tracking (source: computer-vision/tracking_by_segmentation.pdf):
- **SAM 2**: extends SAM to video with temporal memory and mask propagation
- Prompt with a point/box in the first frame, SAM propagates the segmentation
- Handles occlusion through memory mechanism

### Advantages over Box Tracking
- Precise object boundaries (not just rectangular approximation)
- Better handling of deformable objects and partial occlusion
- Natural integration with downstream tasks (e.g., video editing)

## Related pages

- [[computer-vision/local-features]]
- [[computer-vision/cnn-architectures]]
- [[computer-vision/object-detection-segmentation]]
- [[shared/kernel-functions]]
