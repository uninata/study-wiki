# Multimodal Machine Translation

**Summary**: Translation augmented with visual information from accompanying images. Vision-text fusion can disambiguate polysemy, reduce translation errors on visually-grounded terms, and improve low-resource translation.

**Course**: nlp

**Sources**: 11-multimodal-mt.pdf

**Last updated**: 2026-05-02

---

## Overview

Most MT operates on text alone. Multimodal MT adds image understanding to resolve ambiguity and provide grounding (source: nlp/11-multimodal-mt.pdf). Example: the word "bank" is ambiguous in English; paired with an image of a river disambiguates it (source-side grounding). Images enable the translator to choose the correct sense.

---

## Vision-Text Fusion Architectures

Methods for combining CNN image features and text features in a single translation model (source: nlp/11-multimodal-mt.pdf).

### Early Fusion

Image and text processed separately, then combined before encoding.

```
Image → CNN → Image features
              ↓
              Concatenate → Encoder
              ↑
Text → Embedding → Text features
```

**Approach**:
1. Extract image features (e.g., ResNet-50 layer before softmax)
2. Embed source text
3. Concatenate image + text features
4. Feed to encoder

**Pros**: Simple, direct integration
**Cons**: Different feature scales (image: $2048\text{-D}$, text: $512\text{-D}$); requires careful alignment

### Late Fusion

Separate processing streams combined at decoder or output.

```
Image → CNN → Image encoder
              ↓
              Gated fusion → Decoder
              ↑
Text → Embedding → Text encoder
```

**Approach**:
1. Image encoder (CNN layers)
2. Text encoder (LSTM or transformer)
3. Attention gates or concatenation in decoder

**Pros**: Each modality processed independently; more flexible
**Cons**: May miss multimodal interactions; higher latency

### Hierarchical Fusion

Multi-layer integration: early, intermediate, and late fusion.

**Approach**: 
- Early fusion at word embedding level
- Intermediate fusion in encoder layers (cross-modal attention)
- Late fusion at decoder

**Pros**: Captures interactions at all levels
**Cons**: Complex training, hyperparameter tuning

---

## Image Grounding Strategies

Using visual information to constrain translation choices (source: nlp/11-multimodal-mt.pdf).

### Spatial Grounding

**Object detection** (Faster R-CNN, YOLO) identifies regions and object categories.

**Use in MT**: Attention weights over spatial regions; e.g., when translating noun phrases, attend to corresponding image regions.

```
Example:
Source: "The red car parked near the tree"
Image regions: [car], [tree], [grass]
→ When translating "car", high attention to car region
→ When translating "tree", high attention to tree region
```

### Semantic Grounding

**Image captioning** generates text descriptions; used to augment source context.

**Use in MT**: If source text is vague, caption provides grounding. Example:
- Source: "It is beautiful."
- Caption: "A sunset over the ocean"
- Translation (German): "Es ist schön" uses semantic context from caption to disambiguate

### Sense Disambiguation

Visual evidence for correct word sense.

**Example**: "bank"
- Image of river bank → translate to German "Ufer" (riverbank)
- Image of financial building → translate to German "Bank" (financial institution)

**Implementation**: Compute sense probability given image; weight lexical choice probabilities accordingly.

---

## Multimodal Datasets

### Multi30K

- **30K** image-caption pairs from Flickr
- Each image captioned in English; captions translated to German, French, Czech
- Standard benchmark for multimodal MT

**Characteristics**:
- Relatively clean, high-quality captions
- Limited diversity (mostly object-centric images)
- $\approx 10$–$15$ words per caption

### Flickr30k-captions

Similar source; alternate translations available.

### MSCOCO Captions (Translated)

Larger ($\geq 120K$ images), more diverse objects and scenes. Translated versions exist for popular language pairs.

---

## Empirical Improvements

### BLEU Gains

Multimodal MT typically achieves $2\%$–$5\%$ BLEU improvement over text-only on in-domain data (e.g., Flickr image captions).

**Breakdown**:
- $1\%$–$2\%$ from reduced polysemy errors
- $0.5\%$–$1\%$ from better named entity handling (faces recognized in images)
- $0.5\%$–$2\%$ from improved syntax when image provides visual context

### Ambiguous vs. Literal Text

**Largest gains on**:
- Polysemous words (bank, letter, plant, etc.)
- Implicit references (pronouns with visual antecedents)
- Untranslatable cultural references (can use image as bridge)

**Minimal gains on**:
- Highly literal text with little visual relevance
- Abstract concepts (emotions, mathematics, philosophy)

### Domain Transfer

Multimodal improvements are largest in-domain (trained on images). Transfer to text-only domains is weaker (~1% BLEU) because visual features are less relevant.

---

## Challenges and Limitations

### Image Relevance

Not all images are informative. Example: document scan with text overlaid; image provides no new semantic information.

**Impact**: Image features can introduce noise; requires content filtering.

### Modality Mismatch

Image may not correspond precisely to text. Example:
- Text: "The car drove away"
- Image: Static photo of car parked
- Visual context contradicts narrative

**Solution**: Weighted multimodal fusion; text can override image when inconsistent.

### Computational Overhead

Processing images adds:
- CNN inference cost (~50–100ms per image)
- Feature extraction and storage
- Longer training time

**Trade-off**: Worth it for 2–5% BLEU on latency-tolerant applications (offline MT). Marginal for real-time systems.

### Small Datasets

Multimodal MT corpus (Multi30K: 30K examples) is tiny vs. typical MT corpora (millions). Prone to overfitting.

**Solutions**:
- Back-translation: generate synthetic text-image pairs
- Transfer learning: pre-train CNN on ImageNet, fine-tune on task
- Mixture of text-only and multimodal: blend datasets

### Morphology and Script

Multimodal gains modest for morphologically rich languages. Visual grounding helps most for nominal (noun) senses; less for verbal morphology.

---

## Architectural Patterns

### Minimal Extension to Standard NMT

Add image encoder branch; concatenate or attend at specific layer.

```
Text encoder + Image encoder → Shared context → Decoder
```

### Cross-Modal Attention

Decoder attends to both text context (standard attention) and image regions (spatial attention).

```
Decoder hidden state → Multihead attention over text + image features
```

### Gated Fusion

Learn weight: output = α · text_features + (1-α) · image_features. Gate α learned from context.

---

## Related pages

- [[nlp/neural-machine-translation]] — Underlying NMT architecture
- [[nlp/word-and-sentence-representations]] — Image-augmented embeddings
- [[shared/attention-mechanisms]] — Spatial and cross-modal attention
- [[shared/multimodal-fusion]] — Cross-course canonical page on vision-text fusion
- [[computer-vision/cnn-architectures]] — CNN feature extraction (ResNet, ViT)
- [[computer-vision/object-detection-segmentation]] — Object detection for spatial grounding
