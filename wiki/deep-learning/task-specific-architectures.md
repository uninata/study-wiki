# Task-Specific Architectures

**Summary**: Deep learning architectures for object detection, semantic segmentation, and instance segmentation. Covers the R-CNN family, single-shot detectors (YOLO, SSD), anchor-free methods, FCN, U-Net, DeepLab, and Mask R-CNN, along with evaluation metrics (IoU, mAP, PR curves).

**Course**: deep-learning

**Sources**: 09_taskspecificarchitectures.pdf

**Last updated**: 2026-04-15

---

## Object Detection

The task: given an image, output bounding boxes and class labels for all objects (source: deep-learning/09_taskspecificarchitectures.pdf).

### Evaluation Metrics

**Intersection over Union (IoU)**:
IoU = Area(Pred ∩ GT) / Area(Pred ∪ GT)

- IoU >= 0.5 typically counts as correct detection (PASCAL VOC)
- COCO uses multiple thresholds: IoU = 0.5:0.05:0.95

**Precision and Recall**:
- Precision = TP / (TP + FP) — how many detections are correct
- Recall = TP / (TP + FN) — how many objects are found

**Average Precision (AP)**: area under the precision-recall curve for one class.
**Mean AP (mAP)**: average AP across all classes.

(source: deep-learning/09_taskspecificarchitectures.pdf)

### R-CNN Family

#### R-CNN (2014)
1. Generate ~2000 region proposals (selective search)
2. Warp each proposal to fixed size, extract CNN features (AlexNet/VGG)
3. Classify each region with SVM + bounding box regression

Problem: very slow — CNN run separately for each proposal (source: deep-learning/09_taskspecificarchitectures.pdf).

#### Fast R-CNN (2015)
1. Run CNN once on the entire image to get feature map
2. **RoI Pooling**: project region proposals onto feature map, pool to fixed size
3. Classify + regress from pooled features

Key improvement: shared computation — CNN runs once, not 2000 times.

#### Faster R-CNN (2016)
Replace selective search with a **Region Proposal Network (RPN)** (source: deep-learning/09_taskspecificarchitectures.pdf):
- RPN shares the CNN backbone with the detector
- At each spatial position, predict k anchor boxes (different scales/ratios)
- Binary classification (object/not-object) + box regression for each anchor
- End-to-end trainable

**Anchors**: predefined boxes at multiple scales (e.g., 128, 256, 512) and aspect ratios (1:1, 1:2, 2:1). The network predicts offsets relative to anchors, not absolute coordinates.

### Single-Shot Detectors

#### YOLO (You Only Look Once)
Single forward pass — no region proposals (source: deep-learning/09_taskspecificarchitectures.pdf):
1. Divide image into S x S grid
2. Each cell predicts B bounding boxes + confidence + C class probabilities
3. Output: S x S x (B * 5 + C) tensor

- Very fast (real-time)
- Struggles with small objects and multiple objects in one grid cell
- YOLO v2/v3/v4/v5+: anchor boxes, multi-scale detection, better backbones

#### SSD (Single Shot MultiBox Detector)
Multi-scale detection from multiple feature map levels (source: deep-learning/09_taskspecificarchitectures.pdf):
- Uses feature maps at different resolutions (from backbone + extra layers)
- Each feature map predicts detections with anchor boxes at that scale
- Small feature maps detect large objects; large feature maps detect small objects
- Faster than Faster R-CNN, more accurate than original YOLO

### Feature Pyramid Network (FPN)
Build a top-down pathway with lateral connections (source: deep-learning/09_taskspecificarchitectures.pdf):
- Bottom-up: standard CNN backbone (C2, C3, C4, C5)
- Top-down: upsample coarser maps + lateral connections from bottom-up
- Produces feature maps (P2-P5) that are both semantically strong and spatially precise
- Used as the backbone for modern detectors (Faster R-CNN + FPN)

### Anchor-Free Methods
Eliminate predefined anchor boxes (source: deep-learning/09_taskspecificarchitectures.pdf):
- **CornerNet**: detect top-left and bottom-right corners, group by embedding similarity
- **CenterNet**: detect object centers + predict width/height
- **FCOS**: per-pixel prediction of distance to four box boundaries

Advantages: no anchor hyperparameters, simpler, often competitive accuracy.

## Semantic Segmentation

The task: classify every pixel in the image (source: deep-learning/09_taskspecificarchitectures.pdf).

### Fully Convolutional Network (FCN)
Replace FC layers with 1x1 convolutions to output spatial predictions (source: deep-learning/09_taskspecificarchitectures.pdf):
- Encoder: standard CNN backbone (remove FC layers)
- Decoder: transposed convolutions to upsample to input resolution
- Skip connections from encoder to decoder preserve fine spatial details
- FCN-32s, FCN-16s, FCN-8s: progressively finer skip connections

### U-Net
Symmetric encoder-decoder with skip connections at every level (source: deep-learning/09_taskspecificarchitectures.pdf):
- Encoder: conv blocks + max pooling (contract)
- Decoder: transposed conv + concatenation with corresponding encoder features
- Skip connections concatenate (not add) — preserves more spatial information
- Originally for biomedical image segmentation
- Excellent for small datasets (data augmentation heavy)

### DeepLab
Uses **atrous (dilated) convolutions** for dense prediction (source: deep-learning/09_taskspecificarchitectures.pdf):

- **Atrous Spatial Pyramid Pooling (ASPP)**: parallel atrous convolutions at multiple rates (e.g., 6, 12, 18) to capture multi-scale context
- **DeepLab v3+**: encoder-decoder structure with ASPP in the encoder
- Maintains higher resolution feature maps than standard CNNs (output stride 8 or 16 instead of 32)
- **CRF post-processing** (v1/v2): refine boundaries using conditional random fields

See [[deep-learning/cnns]] for dilated/atrous convolution details.

## Instance Segmentation

The task: detect objects AND segment each one with a pixel-level mask (source: deep-learning/09_taskspecificarchitectures.pdf).

### Mask R-CNN
Extends Faster R-CNN with a mask branch (source: deep-learning/09_taskspecificarchitectures.pdf):
1. Faster R-CNN backbone + RPN + RoI detection
2. **RoIAlign** (instead of RoI Pooling): bilinear interpolation avoids quantization — critical for pixel-level accuracy
3. Parallel branches: classification + box regression + binary mask prediction
4. Mask branch: small FCN that predicts a binary mask for each class independently

RoIAlign vs RoI Pooling: RoI Pooling rounds coordinates to integer positions, causing misalignment. RoIAlign uses bilinear interpolation at fractional positions, improving mask accuracy significantly.

## Two-Stage vs One-Stage Summary

| Property | Two-stage (Faster R-CNN) | One-stage (YOLO, SSD) |
|----------|------------------------|----------------------|
| Speed | Slower | Faster (real-time) |
| Accuracy | Generally higher | Competitive (with modern improvements) |
| Small objects | Better (with FPN) | Harder |
| Architecture | RPN + detector | Single forward pass |

(source: deep-learning/09_taskspecificarchitectures.pdf)

## Related pages

- [[deep-learning/backbone-architectures]]
- [[deep-learning/cnns]]
- [[computer-vision/object-detection-segmentation]]
- [[shared/cnn-fundamentals]]
