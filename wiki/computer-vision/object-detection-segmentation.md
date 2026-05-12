# Object Detection and Segmentation

**Summary**: Deep learning methods for localizing and classifying objects (detection) and assigning per-pixel labels (segmentation). Covers the R-CNN family, YOLO, SSD, anchor-based vs anchor-free detectors, FCN, U-Net, dilated convolutions, and Mask R-CNN for instance segmentation.

**Course**: computer-vision

**Sources**: deep_learning_2_mpv_2025.pdf

**Last updated**: 2026-04-15

---

## Object Detection

The task: given an image, output bounding boxes and class labels for all objects.

### Two-Stage Detectors (R-CNN Family)

**R-CNN** (source: computer-vision/deep_learning_2_mpv_2025.pdf):
1. Generate ~2000 region proposals (Selective Search)
2. Warp each region to fixed size, extract CNN features
3. Classify each region with SVMs; refine box with regression
- Slow: CNN runs once per proposal

**Fast R-CNN**: share CNN computation across proposals:
1. Run CNN on the entire image → feature map
2. Project proposals onto the feature map
3. **RoI pooling**: extract fixed-size features per proposal
4. Classify + regress in one forward pass

**Faster R-CNN**: replace Selective Search with a learned **Region Proposal Network (RPN)** (source: computer-vision/deep_learning_2_mpv_2025.pdf):
- RPN shares convolutional features with the detector
- Predicts objectness score + box refinement at each spatial location
- **Anchors**: predefined boxes at multiple scales and aspect ratios at each feature map location
- End-to-end trainable, near real-time

### Single-Stage Detectors

**YOLO (You Only Look Once)** (source: computer-vision/deep_learning_2_mpv_2025.pdf):
- Divide image into S×S grid
- Each cell predicts B bounding boxes + confidence + class probabilities
- Single forward pass — very fast (real-time)
- Lower accuracy than two-stage, especially for small objects

**SSD (Single Shot MultiBox Detector)**:
- Multi-scale detection: predict boxes from multiple feature map resolutions
- Anchors at each scale — better for objects of varying size
- Combines YOLO's speed with better accuracy

### Anchor-Based vs Anchor-Free

- **Anchor-based** (Faster R-CNN, SSD, YOLOv2+): predefine box templates, predict offsets
- **Anchor-free** (FCOS, CenterNet): predict object centers + distances to edges, or center + width/height
- Anchor-free avoids hyperparameter tuning of anchor sizes/ratios

## Semantic Segmentation

The task: assign a class label to every pixel.

### Fully Convolutional Networks (FCN)
Replace FC layers with 1x1 convolutions — output a spatial map of class predictions (source: computer-vision/deep_learning_2_mpv_2025.pdf):
- **Encoder**: standard CNN (VGG, ResNet) extracts features at reduced resolution
- **Decoder**: upsample back to input resolution via transposed convolutions
- **Skip connections**: combine low-level (fine spatial) and high-level (semantic) features

### U-Net
Symmetric encoder-decoder with dense skip connections (source: computer-vision/deep_learning_2_mpv_2025.pdf):
- Skip connections concatenate encoder features with decoder features at each level
- Originally designed for biomedical image segmentation
- Excels with limited training data due to strong spatial priors

### Dilated (Atrous) Convolutions
Expand the receptive field without downsampling (source: computer-vision/deep_learning_2_mpv_2025.pdf):
- Insert gaps ("holes") in the convolution kernel
- Dilation rate r: kernel covers (2r-1) × (2r-1) region but with standard number of parameters
- Used in DeepLab and other segmentation architectures

## Instance Segmentation

The task: detect objects AND produce a per-pixel mask for each instance.

### Mask R-CNN
Extends Faster R-CNN with a mask prediction branch (source: computer-vision/deep_learning_2_mpv_2025.pdf):
- Adds a small FCN that predicts a binary mask for each RoI
- **RoI Align** (replaces RoI Pool): bilinear interpolation avoids spatial quantization — critical for pixel-accurate masks
- Three outputs per proposal: class, box, mask
- State-of-the-art instance segmentation

## Related pages

- [[computer-vision/cnn-architectures]]
- [[shared/cnn-fundamentals]]
- [[computer-vision/generative-models]]
- [[pattern-recognition/neural-networks]]
