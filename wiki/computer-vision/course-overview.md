# Computer Vision — Course Overview

**Summary**: Graduate-level computer vision course covering local features, geometric vision (epipolar geometry, 3D reconstruction, camera calibration), deep learning for vision (CNNs, detection, segmentation, generative models), visual tracking, image retrieval, metric learning, and self-supervised learning.

**Course**: computer-vision

**Sources**: MPV2026_local_features_lctures_1_2.pdf, 2021_ransac.pdf, deep_learning_mpv_2026.pdf, deep_learning_2_mpv_2025.pdf, MPV2025_Learned_matching.pdf, cvm-07-2026-03-30-3d_reconstruction_part_1.pdf, mpv-08-2026-04-13_3d_reconstruction_part_2.pdf, kcf_lecture2016.pdf, matas-2018.04-klt-only.pdf, mpv24_deepmetriclearning.pdf, mpv24_ssl.pdf, mpv25_retrieval.pdf, tld.pdf, tracking_by_segmentation.pdf, courses_mpv_labs_1_intro_start.pdf, courses_mpv_labs_2_correspondence_problem_start.pdf

**Last updated**: 2026-04-15

---

## Course Structure

The course covers computer vision from classical methods to modern deep learning approaches:

1. **Local features** (lectures 1-2): Harris corners, scale-space, SIFT, binary descriptors
2. **RANSAC**: robust estimation with outliers
3. **Deep learning for CV** (lectures 3-4): CNN architectures, detection, segmentation, generative models
4. **Learned matching**: SuperPoint, SuperGlue, LoFTR
5. **Epipolar geometry**: fundamental/essential matrix, correspondence problem
6. **3D reconstruction** (lectures 7-8): camera models, calibration, stereo, SfM, bundle adjustment
7. **Visual tracking**: KLT, KCF, TLD, tracking by segmentation
8. **Deep metric learning**: contrastive/triplet loss, face recognition
9. **Self-supervised learning**: SimCLR, MoCo, BYOL, DINO, MAE
10. **Image retrieval**: BoVW, VLAD, Fisher vectors, deep retrieval

## Key Themes

- **Classical vs. learned approaches**: many topics present both classical (hand-crafted features, geometric solutions) and modern learned alternatives
- **Geometry is fundamental**: camera models, epipolar constraints, and projective geometry underpin much of the course
- **Feature representations**: from hand-crafted (SIFT, ORB) to learned (SuperPoint, deep embeddings)
- **Robustness**: RANSAC for outlier handling, data augmentation for generalization

## Labs

- Lab 1: Introduction to course tools and environment
- Lab 2: Correspondence problem — fundamental matrix estimation

## Related pages

- [[computer-vision/exam-questions]]
- [[computer-vision/local-features]]
- [[computer-vision/ransac]]
- [[computer-vision/epipolar-geometry]]
- [[computer-vision/camera-models-calibration]]
- [[computer-vision/3d-reconstruction]]
- [[computer-vision/cnn-architectures]]
- [[computer-vision/object-detection-segmentation]]
- [[computer-vision/generative-models]]
- [[computer-vision/learned-matching]]
- [[computer-vision/visual-tracking]]
- [[computer-vision/deep-metric-learning]]
- [[computer-vision/self-supervised-learning]]
- [[computer-vision/image-retrieval]]
