# Multilingual Machine Translation

**Summary**: Techniques for translating between multiple language pairs using shared models, transfer learning, and zero-shot translation. Enables translation of low-resource language pairs via cross-lingual transfer.

**Course**: nlp

**Sources**: 10-multilingual-mt.pdf

**Last updated**: 2026-05-02

---

## Overview

Multilingual MT addresses the sparsity problem in low-resource language pairs by leveraging data from related languages. Key approaches: pivot-based translation, cross-lingual transfer via shared vocabularies, and single massively-multilingual models trained for all pairs simultaneously.

---

## Pivot-Based Machine Translation

Using a high-resource intermediary language to bridge low-resource pairs (source: nlp/10-multilingual-mt.pdf).

### Two-Stage Pipeline

**Stage 1**: Translate source $\to$ pivot language (e.g., English)
**Stage 2**: Translate pivot $\to$ target language

**Advantages**:
- Leverages abundant data for source-pivot and pivot-target pairs
- Can use pre-trained models for both stages
- Simple pipeline; easy to diagnose errors

**Disadvantages**:
- Cascade errors: mistakes in Stage 1 propagate to Stage 2
- Intermediate representation may lose nuance
- Typically lower quality than direct translation

### Cascade Quality Loss

Error propagation is roughly multiplicative. If each stage has $70\%$ quality, cascade achieves $0.7 \times 0.7 = 49\%$. Effective for extremely low-resource pairs where no direct data exists; less useful when some direct data is available.

### Language Selection

Pivot choice matters: prefer pivot languages that are linguistically similar to source and target (e.g., use Swahili as pivot for Bantu language pairs). Pivot should have abundant training data.

---

## Cross-Lingual Transfer

Sharing parameters and representations across language pairs (source: nlp/10-multilingual-mt.pdf).

### Shared Vocabularies

**BPE (Byte-Pair Encoding)** applied to all languages jointly creates shared vocabulary:
- Common prefixes/suffixes shared across languages
- Morphologically similar languages benefit most
- Reduces parameter counts vs. language-specific vocabularies

**Example**: "un-" prefix in English (unhappy), German (unglücklich), and Dutch (ongelukkig) share BPE segments.

### Multilingual Embedding Spaces

**Objective**: Align monolingual embedding spaces so that translation equivalents are nearby neighbors.

**Methods**:
1. **Procrustes alignment**: Find rotation matrix $\mathbf{W}$ that minimizes $\|\mathbf{X}_{\text{target}} - \mathbf{W} \cdot \mathbf{X}_{\text{source}}\|^2$ given a seed dictionary.
2. **Self-learning**: Bootstrap alignment using nearest neighbors iteratively.
3. **MUSE (Multilingual Unsupervised and Supervised Embeddings)**: Combines unsupervised (adversarial) and supervised alignment.

**Benefit**: Cross-lingual word lookup for initialization; enables zero-shot lexicon induction.

### Shared Encoder/Decoder

Single encoder processes all source languages; single decoder generates all target languages.

**Key trick**: Prepend language tag to input (e.g., `<en>`, `<fr>`) to signal target language to decoder.

**Benefits**:
- Massive parameter reduction vs. per-language models
- Transfer from high-resource to low-resource pairs
- Soft sharing of linguistic patterns across language families

**Trade-offs**:
- Lower per-language performance than language-specific models
- Requires balancing training data across pairs (high-resource pairs can dominate)

---

## Zero-Shot Translation

Translating between two languages the model has never directly seen together (source: nlp/10-multilingual-mt.pdf).

### Direct Zero-Shot

Train single multilingual model on all (source, target) language pairs in training data. At test time, translate between pairs not in training:
- **Example**: Train on (EN$\to$FR), (FR$\to$DE), (EN$\to$DE). At test, translate (FR$\to$DE) without ever seeing that pair.
- Possible because shared encoder/decoder learn language-agnostic representations and language-specific roles.

### Mechanism

1. Encoder processes source language → abstract representation
2. Decoder uses target language tag + encoder output → target language output
3. If encoder has seen source and decoder has seen target, transfer occurs even if pair was unseen

### Pivot Zero-Shot

Explicit bridging: `source → pivot → target` via two separate models. Less effective than direct zero-shot if direct model has seen the pair.

### Evaluation

Zero-shot typically achieves $60\%$–$80\%$ of supervised BLEU for well-resourced pairs. Performance drops for distant language pairs and morphologically complex languages.

---

## Challenges in Multilingual MT

### Morphological Richness

Languages like Arabic (20+ morphological features), Turkish (agglutinative), and Finnish (rich case system) suffer in shared vocabulary approaches.

**Solutions**:
- Linguistically-informed segmentation (factored models)
- Language-specific preprocessing (lemmatization)
- Morphology-aware subword units

### Corpus Imbalance

Training data highly imbalanced: English-German abundant, Swahili-Thai scarce.

**Solutions**:
- Temperature-based sampling: sample low-resource pairs more frequently
- Data weighting: assign higher loss weight to low-resource pair examples
- Iterative back-translation: generate synthetic data for low-resource pairs

### Script Diversity

Different scripts (Latin, Arabic, Cyrillic, CJK) require separate tokenization.

**Solutions**:
- Unified byte-level vocabularies (BPE applied to UTF-8 bytes)
- Language-specific preprocessing (convert to transliteration)

### Language Similarity

Dissimilar language pairs (e.g., English-Chinese) compete for capacity in shared models.

**Solutions**:
- Multi-pivot: use multiple intermediate languages
- Language family grouping: separate models per family
- Soft language selection: gate encoder/decoder based on language pair

---

## System Design Patterns

### Massively Multilingual Models

Train single encoder-decoder on 100+ language pairs.

**Pros**: Extreme parameter sharing; low deployment cost
**Cons**: Diluted capacity per language; complex optimization

**Example**: mBART, mT5, mRASP

### Language Family Clusters

Separate models for each family (Romance, Slavic, Sino-Tibetan, etc.).

**Pros**: Better per-family performance; simpler training
**Cons**: No bridge between families; requires multiple deployments

### Hybrid (Hierarchical)

Shared high-level representations + language-specific low-level modules.

**Pros**: Balanced capacity sharing and specialization
**Cons**: Added complexity in architecture and training

---

## Related pages

- [[nlp/neural-machine-translation]] — Underlying seq2seq/transformer architecture
- [[nlp/word-and-sentence-representations]] — Multilingual embeddings enable transfer
- [[nlp/morphology-machine-translation]] — Handling morphology in low-resource pairs
- [[nlp/decoding-algorithms]] — Decoding strategies for multilingual models
- [[shared/transformers]] — Transformer architecture for multilingual encoders/decoders
- [[shared/contextual-embeddings]] — Multilingual BERT, mBERT, XLM-R
