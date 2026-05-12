# Multimodal Fusion: Vision-Text Integration

**Summary**: Techniques for combining visual and textual features into unified representations. Used in machine translation, visual question answering, image captioning, and cross-modal retrieval.

**Course**: nlp, computer-vision, deep-learning

**Sources**: nlp/11-multimodal-mt.pdf, computer-vision/deep-metric-learning.pdf

**Last updated**: 2026-05-02

---

## Overview

Multimodal fusion integrates information from multiple modalities (vision, text, audio, etc.) into a single representation or decision. Key challenge: modalities have different dimensionalities, feature scales, and semantic grounding.

---

## Vision-Text Fusion Strategies

### Early Fusion

Combine features immediately after extraction.

```
Image → CNN (ResNet-50)  ─┐
                           ├─ Concatenate ─ Shared encoder
                           │
Text → Embedding    ──────┘
```

**Implementation**:
1. Extract image: $2048\text{-D}$ vector (ResNet-50 final layer)
2. Embed text: $512\text{-D}$ vectors per word
3. Concatenate or project to common dimension
4. Pass to shared encoder (LSTM or transformer)

**Advantages**:
- Simple
- Direct feature integration
- Low latency

**Disadvantages**:
- Modality-specific preprocessing required
- Scale mismatch (image: $2048\text{-D}$, text: $512\text{-D}$) can bias optimization
- Early commitment limits interaction modeling

### Late Fusion

Separate processing streams combined during decoding.

```
Image → CNN ──────────────┐
       (ResNet-50)        ├─ Attention + Gating ─ Decoder
                          │
Text → Embedding ─ Encoder┘
```

**Implementation**:
1. Image encoder: CNN features through projection layer $\to$ $512\text{-D}$
2. Text encoder: Standard LSTM/transformer encoder $\to$ sequence of $512\text{-D}$
3. Decoder attends to both text context and image features
4. Gating mechanism: $\lambda \cdot \text{text\_attention} + (1-\lambda) \cdot \text{image\_attention}$

**Advantages**:
- Modalities processed independently
- Flexible weighting per timestep
- Can handle variable-length text + fixed image

**Disadvantages**:
- May miss early multimodal interactions
- Requires careful attention mechanism design
- Higher computational cost

### Intermediate Fusion

Multiple fusion points throughout the model.

```
Image features ──────────────┐
                             ├─ Fuse @ layer k ─ Continue encoder
Text encoder ─────────────── ┘
```

**Implementation**:
1. Process text through first k layers
2. Inject image features via concatenation or cross-attention
3. Continue encoding with multimodal context

**Advantages**:
- Captures interactions at multiple levels
- Progressive refinement
- Leverages both early and late information

**Disadvantages**:
- Complex architecture
- More hyperparameters
- Harder to debug

---

## Attention Mechanisms for Multimodal Learning

### Spatial Attention

Attend to image regions (bounding boxes from object detection).

**Use case**: Machine translation of spatially-grounded nouns.

**Mechanism**:
```
Decoder hidden h_t
    ↓
Linear transformation
    ↓
Attention scores over K regions (K = 50 typical)
    ↓
Softmax normalization
    ↓
Weighted sum of region features
```

**Example**: Translating "the dog" — attend to dog region in image.

### Cross-Modal Attention

Text attends to image (or vice versa) using standard attention.

```
Query: Text encoder hidden state
Key:   Image feature patches (e.g., 49 patches from ViT)
Value: Image features
    ↓
Multihead cross-attention
    ↓
Context vector
```

**Benefit**: Learns alignment between words and visual regions automatically.

### Gated Fusion

Learn to weight modalities dynamically.

$$\text{Gate} = \sigma(\mathbf{W}_{\text{text}} \cdot \mathbf{f}_{\text{text}} + \mathbf{W}_{\text{image}} \cdot \mathbf{f}_{\text{image}} + \mathbf{b})$$

$$\text{Output} = \text{Gate} \cdot \mathbf{f}_{\text{text}} + (1 - \text{Gate}) \cdot \mathbf{f}_{\text{image}}$$

**Benefit**: Model learns when to trust text vs. image; adaptive per example.

---

## Embeddings in Multimodal Space

### Joint Embedding Space

Learn shared representation where image and text are close if semantically related.

**Loss**: Contrastive or triplet loss.

$$\mathcal{L}_{\text{contrastive}} = -\log \left[ \frac{\exp(\text{sim}(\text{img}, \text{txt}) / \tau)}{\sum_{j} \exp(\text{sim}(\text{img}, \text{txt}_j) / \tau)} \right]$$

where $\tau$ is temperature (typically $0.07$).

**Use**: Cross-modal retrieval (image to text or vice versa).

### Image Captioning Decoder

Text decoder generates captions conditioned on image embeddings.

```
Image → CNN → Linear projection → Decoder
                                      ↓
                                    Caption
```

**Variant**: Attention-based captioning where decoder attends to image regions at each step.

---

## Fusion of Text-Visual Information for Translation

### Image-as-Context

Image provides background for translating ambiguous text.

**Example**:
```
English:  "It looks delicious."
Image:    Plate of pasta
German:   "Es sieht köstlich aus."
(vs. without image context, might generate generic caption)
```

**Implementation**: Image features concatenated to encoder context; attended by decoder.

### Spatial Grounding for Entity Translation

Object detection identifies entities in image.

```
Text:     "A cat is sleeping on a mat."
Image:    [Cat region], [Mat region]

Decoder step "cat":
  ├─ Text attention: previous words
  ├─ Image attention: [Cat region]
  └─ Generate: "Katze" (German for cat)
```

**Benefit**: Reduces ambiguity in entity translation across languages.

### Multimodal Sense Disambiguation

Image disambiguates word senses.

```
English word: "bank"
Images:
  ├─ River bank (natural landscape)
  ├─ Financial bank (office building)

Given image, predict P(sense | image)
  → Constrain decoder to choose correct translation
```

---

## Challenges and Design Decisions

### Modality Imbalance

Visual information not always informative.

**Scenarios**:
- Abstract text (philosophy, math) — image irrelevant
- Misaligned image-text (image from different scene) — contradictory
- Uninformative image (random pattern) — noise

**Solutions**:
- Learn modality confidence weights
- Gated fusion where text can override image
- Curriculum learning: text-only early, add vision later

### Feature Scale and Dimensionality

Image features (ResNet: 2048-D) vs. text embeddings (typically 256–512-D).

**Solutions**:
- Projection to common dimension
- Layer normalization per modality
- Separate scaling factors (learned)

### Computational Overhead

Processing images adds:
- CNN inference: 50–100 ms per image
- Feature extraction and storage: 2048 floats per image
- Attention computation: Quadratic in sequence length

**Solutions**:
- Efficient feature extraction (MobileNet, EfficientNet)
- Distillation: train student multimodal model on teacher predictions
- Feature caching: precompute image features offline

### Dataset Alignment

Image-text pairs must be well-aligned. Noisy alignment degrades performance.

**Example**: Multi30K (30K image-caption pairs) is small and high-quality. Larger datasets like COCO often have looser alignment.

---

## Applications

### Machine Translation

Improvements: 2–5% BLEU on image-caption translation. Largest gains on polysemous words.
(See [[nlp/multimodal-machine-translation]])

### Visual Question Answering (VQA)

**Task**: Given image and text question, generate answer.

**Fusion**: Image features + question embeddings → Attention over image regions → Predict answer class.

### Image Captioning

**Task**: Generate textual description of image.

**Architecture**: CNN encoder → RNN decoder with spatial attention.

**Multimodal variant**: Encoder can be video (temporal) + audio.

### Cross-Modal Retrieval

**Task**: Given image, retrieve matching captions (or vice versa).

**Method**: Learn joint embedding space where matched pairs are close, mismatched far.

**Loss**: Contrastive loss (triplet loss, NT-Xent, etc.).

---

## Related pages

- [[nlp/multimodal-machine-translation]] — Application to translation
- [[computer-vision/cnn-architectures]] — CNN feature extractors (ResNet, EfficientNet)
- [[computer-vision/object-detection-segmentation]] — Region-based spatial grounding
- [[computer-vision/generative-models]] — VAEs, GANs for multimodal generation
- [[shared/attention-mechanisms]] — Attention over multimodal features
- [[shared/transformers]] — Vision transformers (ViT) for image encoding
- [[deep-learning/neural-networks]] — LSTM and RNN decoders
