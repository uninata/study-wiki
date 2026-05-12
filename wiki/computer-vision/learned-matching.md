# Learned Matching

**Summary**: Deep learning approaches to feature matching, replacing or augmenting classical detect-describe-match pipelines. Covers SuperPoint (learned detector+descriptor), SuperGlue (graph neural network matcher with attention), and LoFTR (detector-free transformer-based coarse-to-fine matching).

**Course**: computer-vision

**Sources**: MPV2025_Learned_matching.pdf

**Last updated**: 2026-04-15

---

## Classical vs Learned Pipelines

The classical pipeline (source: computer-vision/MPV2025_Learned_matching.pdf):
1. **Detect** keypoints (Harris, SIFT, ORB) — see [[computer-vision/local-features]]
2. **Describe** each keypoint (SIFT descriptor, BRIEF)
3. **Match** descriptors (nearest neighbor, ratio test)
4. **Filter** with geometric verification ([[computer-vision/ransac|RANSAC]])

Learned methods replace one or more of these stages with neural networks.

## SuperPoint

Learned keypoint detector + descriptor (source: computer-vision/MPV2025_Learned_matching.pdf):

### Architecture
- Shared encoder backbone (VGG-like)
- Two decoder heads:
  - **Detector head**: predicts a heatmap of keypoint locations (65-channel output for 8×8 grid cells + dustbin)
  - **Descriptor head**: dense descriptor map, interpolated at keypoint locations (256-dim)

### Training
- **Homographic adaptation** (self-supervised): apply random homographies to images, detect keypoints in each, aggregate to build pseudo ground-truth
- No need for manually labeled keypoints
- Trained on synthetic shapes first, then adapted to real images

### Properties
- Real-time inference
- Repeatable keypoints across viewpoint changes
- Descriptors trained jointly with detection — better than post-hoc pairing

## SuperGlue

Learned feature matching via graph neural networks (source: computer-vision/MPV2025_Learned_matching.pdf):

### Architecture
- Input: two sets of keypoints with descriptors (e.g., from SuperPoint)
- **Keypoint encoding**: MLP embeds position + descriptor into a joint representation
- **Attentional GNN**: alternating layers of:
  - **Self-attention**: message passing within each image (contextualizes features)
  - **Cross-attention**: message passing between images (finds correspondences)
- **Optimal transport layer**: produces a soft assignment matrix (including dustbin for unmatched points)

### Sinkhorn Algorithm
Solves the optimal transport problem to produce the assignment matrix (source: computer-vision/MPV2025_Learned_matching.pdf):
- Iteratively normalizes rows and columns of the score matrix
- **Dustbin** row/column handles unmatched keypoints (occlusions, non-overlapping regions)

### Properties
- Learns to use both visual appearance and spatial context
- Handles repetitive structures and textureless regions better than nearest-neighbor matching
- Jointly reasons about all correspondences (not just pairwise)

## LoFTR (Detector-Free Local Feature Matching with Transformers)

Removes the detection step entirely — matches directly on dense feature maps (source: computer-vision/MPV2025_Learned_matching.pdf):

### Architecture
1. **Feature extraction**: CNN backbone extracts multi-scale features
2. **Coarse matching** (at 1/8 resolution):
   - Flatten features from both images
   - Apply **transformer** with self-attention and cross-attention layers
   - Produce a coarse confidence matrix → extract mutual nearest neighbors
3. **Fine matching** (sub-pixel):
   - For each coarse match, crop a local window from fine-resolution features
   - Apply correlation to refine to sub-pixel accuracy

### Key Innovations
- **No detector needed**: works in textureless or repetitive regions where detectors fail
- **Coarse-to-fine**: efficient — transformer operates on low-res features, refinement is local
- **Linear attention** (optional): reduces quadratic cost of standard attention

### Performance
- State-of-the-art on indoor and outdoor matching benchmarks
- Particularly strong on challenging scenes (large viewpoint changes, textureless surfaces)
- Slower than SuperPoint + SuperGlue but more robust

## Pipeline Comparison

| Method | Detector | Descriptor | Matcher | Speed |
|--------|----------|-----------|---------|-------|
| SIFT + NN | SIFT | SIFT (128-d) | Nearest neighbor | Moderate |
| SuperPoint + NN | SuperPoint | SuperPoint (256-d) | Nearest neighbor | Fast |
| SuperPoint + SuperGlue | SuperPoint | SuperPoint | SuperGlue (GNN) | Moderate |
| LoFTR | None | CNN + Transformer | Integrated | Slower |

## Related pages

- [[computer-vision/local-features]]
- [[computer-vision/ransac]]
- [[computer-vision/epipolar-geometry]]
- [[computer-vision/3d-reconstruction]]
