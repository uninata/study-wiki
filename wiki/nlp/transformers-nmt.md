# Transformers for Neural Machine Translation

**Summary**: Transformer architecture applied to NMT, including self-attention replacing RNNs, encoder-decoder structure, positional encoding, LLM-based translation, and evidence about source syntax in Transformer NMT.

**Course**: nlp

**Sources**: 08-transformer-and-syntax-in-nmt.pdf, nlp/The Annotated Transformer.md, nlp/The Illustrated Transformer.md, nlp/The Illustrated Transformer (1).md, nlp/Transformer Architecture_ Attention Is All You Need.md, nlp/Promoting the knowledge of source syntax in Transformer NMT.pdf

**Last updated**: 2026-05-21

---

## Overview

Transformers revolutionized NMT by replacing recurrent architectures with pure attention-based models. The original architecture uses stacked encoder and decoder layers with self-attention, feed-forward sublayers, residual connections, and layer normalization (source: nlp/The Annotated Transformer.md; source: nlp/The Illustrated Transformer.md).

---

## Motivation: Limitations of RNN-Based NMT

### Sequential Processing Bottleneck

**RNN Architecture**:
```
Encoder:  f₁ → f₂ → f₃ → f₄ → f₅
          (sequential, cannot parallelize)

Decoder:  e₁ ← e₂ ← e₃ ← e₄
          (sequential generation)
```

**Problem**:
- **Training Parallelization**: Cannot compute all encoder states simultaneously
- **Inference Latency**: Must generate target word-by-word sequentially
- **Long Sequences**: Gradient signals weaken over long dependencies (vanishing gradients)

**Impact**: Training slow (days on large datasets); inference latency limits deployment

### Vanishing Gradients

Despite LSTM/GRU mitigations:
- Gradients still decay over long sequences
- RNNs struggle with long-range dependencies (50+ token distances)
- Fixed-size hidden state can lose information

---

## Transformer Architecture

### Encoder-Decoder Structure

**Encoder** (source: 08-transformer-and-syntax-in-nmt.pdf):
- Stack of identical layers (typically 6)
- Each layer:
  - **Multi-head self-attention**: Each token attends to all other tokens
  - **Feed-forward network**: Point-wise nonlinearity
  - **Layer normalization & residual connections**: Stabilize training
- **Input**: Source word embeddings + positional encodings
- **Output**: Contextual representations (one per source token)

**Decoder** (source: 08-transformer-and-syntax-in-nmt.pdf):
- Stack of identical layers (typically 6)
- Each layer:
  - **Masked self-attention**: Each token attends only to previous tokens (left-to-right)
  - **Cross-attention to encoder**: Each target token attends to all source tokens
  - **Feed-forward network**: Same as encoder
  - **Layer normalization & residual connections**
- **Input**: Target word embeddings + positional encodings (teacher forcing during training)
- **Output**: Probability distribution over vocabulary at each step

### Self-Attention Mechanism

See [[shared/attention-mechanisms]] for detailed description.

In self-attention, each token is projected into query, key, and value representations; attention weights come from query-key compatibility, and the output is a weighted sum of values (source: nlp/The Illustrated Transformer.md; source: nlp/Transformer Architecture_ Attention Is All You Need.md). Multi-head attention repeats this computation in several learned representation subspaces, then concatenates and projects the head outputs (source: nlp/The Illustrated Transformer.md).

**Key Difference from RNN Attention**:
- RNN attention: Decoder attends to encoder outputs (one decoder layer)
- Transformer: Every layer can attend to any other layer in source/target
- **Result**: More flexible information flow; better long-range dependencies

### Positional Encoding

**Problem**: Attention is **permutation-invariant**; doesn't inherently model word order

**Solution**: Add positional information to embeddings (source: 08-transformer-and-syntax-in-nmt.pdf; source: nlp/The Annotated Transformer.md):

$$\text{PE}(pos, 2i) = \sin\left(\frac{pos}{10000^{2i/d_{\text{model}}}}\right)$$

$$\text{PE}(pos, 2i+1) = \cos\left(\frac{pos}{10000^{2i/d_{\text{model}}}}\right)$$

where:
- $pos$ = token position (0, 1, 2, ...)
- $i$ = dimension index ($0, 1, \ldots, d_{\text{model}}/2 - 1$)
- $d_{\text{model}}$ = embedding dimension (512, 768, etc.)

**Properties**:
- **Learned Relative Position**: Each dimension oscillates at different frequency
- **No Absolute Position**: Model learns relative distances naturally
- **Extrapolation**: Can extend to longer sequences than training (unlike learned position embeddings)

### Advantages over RNNs

1. **Parallelizability** (source: 08-transformer-and-syntax-in-nmt.pdf):
   - All encoder positions computed simultaneously
   - All decoder positions (during training) computed in parallel
   - **Impact**: 10-100x speedup in training on GPUs

2. **Long-Range Dependencies**:
   - Direct connections between distant tokens ($\mathcal{O}(1)$ path length)
   - No vanishing gradients
   - **Impact**: Better handling of 50+ token distances

3. **Efficiency**: Constant depth (unlike RNNs which grow with sequence length)

4. **State-of-the-Art Performance**: Transformers outperform RNN-based NMT consistently

---

## Training Transformers

### Initialization and Layer Normalization

**Challenge**: Deep networks (12+ layers) prone to gradient explosion/vanishing

**Solutions** (source: 08-transformer-and-syntax-in-nmt.pdf):
- **Layer Normalization**: Normalize activations to zero mean, unit variance
  - Applied before each multi-head attention and feed-forward
  - Stabilizes training dramatically
- **Careful Weight Initialization**: Xavier/He initialization
- **Residual Connections**: Each layer outputs: LayerNorm(x + Attention(x))

### Optimization

**Optimizer**: Adam with learning rate scheduling

**Learning Rate Schedule** (source: 08-transformer-and-syntax-in-nmt.pdf):
- **Warm-up Phase**: Linearly increase learning rate (first 4000-10000 steps)
  - Avoids unstable early training
- **Decay Phase**: Decrease learning rate inverse-sqrt in step
  - Slowly reduce updates as training progresses

**Effect**: Transformers train much faster than RNNs despite more parameters

### Regularization

- **Dropout**: Randomly zero activations (attention weights, feed-forward hidden units)
  - Typical rate: 0.1
- **Label Smoothing**: Soften one-hot targets
  - Replace class probability 1.0 with 0.9, others 0.1/(vocab_size - 1)
  - Prevents over-confident predictions
- **Early Stopping**: Monitor validation BLEU; stop if plateaus

---

## Inference: Encoder-Decoder Generation

**At test time**:

1. **Encode source**: Run encoder once to get source representations (cached)
2. **Initialize decoder**: Start with <BEGIN> token
3. **Auto-Regressive Decoding**:
   - Compute cross-attention from target to source representations
   - Generate next token
   - Append to target sequence
   - Repeat until <END> token or max length

**With Beam Search** (source: 08-transformer-and-syntax-in-nmt.pdf):
- Keep k best partial hypotheses
- Expand each by one token
- Prune to k best overall
- **Complexity**: $\mathcal{O}(n \times k)$ where $n$ = target length, $k$ = beam width

---

## LLM-Based Translation

### Paradigm Shift

Traditional approach: Train translation model from scratch
```
Parallel corpus (millions of words)
   ↓
Train encoder-decoder transformer
   ↓
Fine-tune on specific language pair
   ↓
Deploy
```

**LLM Approach**: Start with pre-trained large model
```
Monolingual text (billions of words)
   ↓
Pre-train large language model
   ↓
Fine-tune on translation task (millions of words)
   ↓
Deploy
```

### Advantages

1. **Transfer Learning**: Pre-training on billions of words captures general language knowledge
   - Better language modeling prior
   - Few-shot capability (translate with minimal parallel data)
2. **Multilingual**: Single model handles many languages (if pre-trained multilingually)
3. **Better Quality**: Pre-training provides strong starting point

### Application to Czech Translation (source: 08-transformer-and-syntax-in-nmt.pdf)

- Fine-tune general-purpose LLM for Czech-English translation
- Fewer Czech-specific training steps needed
- Leverage knowledge from pre-training on diverse data

### Computational Trade-offs

**Advantages**:
- Faster training (few-shot learning)
- Better final quality
- Transfer across language pairs

**Disadvantages**:
- Large model size (1B+ parameters)
- Slower inference latency
- Higher memory requirements

---

## Syntax Learning in Transformers

### Implicit Syntax

Do transformers learn linguistic structure without explicit supervision?

**Evidence** (source: 08-transformer-and-syntax-in-nmt.pdf):
- Attention patterns often align with parse tree structure
- Some attention heads specialize in syntactic roles (subject, object, etc.)
- Model learns useful linguistic patterns even without supervised syntax signal

### Explicit Source Syntax: What the Paper Found

The source-syntax Transformer paper asks whether providing source-side dependency syntax helps a modern Transformer, given that earlier positive results were mostly from recurrent sequence-to-sequence models and smaller data settings (source: nlp/Promoting the knowledge of source syntax in Transformer NMT.pdf).

It tests two multi-task strategies:

1. **Simple alternating multi-tasking**: mix translation examples with auxiliary source-side parsing tasks, such as predicting dependency heads or labels (source: nlp/Promoting the knowledge of source syntax in Transformer NMT.pdf).
2. **Dependency interpretation of self-attention**: train one Transformer attention head to match a source-side dependency tree (source: nlp/Promoting the knowledge of source syntax in Transformer NMT.pdf).

The simple alternating setup did not outperform the single-task MT baseline after the same number of training steps. The paper argues that sharing encoder and decoder capacity with an auxiliary task can cost more than the syntactic supervision helps, though the same idea may still be useful in smaller-data settings (source: nlp/Promoting the knowledge of source syntax in Transformer NMT.pdf).

The attention-head supervision setup improved translation and produced reasonable parsing behavior, but the crucial control experiment used a trivial "diagonal parse" or linear tree and obtained very similar translation gains. This weakens the claim that the improvement comes from true linguistic syntax; it may instead be a regularizing effect imposed on self-attention matrices (source: nlp/Promoting the knowledge of source syntax in Transformer NMT.pdf).

**Takeaway**: In Transformer NMT, explicit source syntax is not automatically useful. A good syntax-aware experiment needs dummy baselines, because a gain from an auxiliary objective may come from regularization rather than linguistic knowledge (source: nlp/Promoting the knowledge of source syntax in Transformer NMT.pdf).

---

## Comparison: RNN vs. Transformer

| Aspect | RNN-Based | Transformer |
|--------|-----------|------------|
| **Training Speed** | Slow (sequential) | Fast (parallel) |
| **Memory per Token** | $\mathcal{O}(n)$ for cache | $\mathcal{O}(n^2)$ for attention |
| **Long-Range Deps** | Weak (gradient vanishing) | Strong (direct paths) |
| **Inference Latency** | Per-word sequential | Parallel (with beam) |
| **Parameter Count** | Moderate | Large |
| **Interpretability** | Medium (RNN hidden states) | Medium (attention patterns) |
| **Translation Quality** | Good (28-30 BLEU) | Excellent (30-35+ BLEU) |

---

## Modern Variants

### Efficient Transformers
- **Sparse Attention**: Attend to subset of tokens (linear complexity)
- **Linformer**: Use linear approximation to attention
- **Longformer**: Combine local + global attention

### Multilingual Transformers
- Single model for 100+ languages
- Shared vocabulary (multilingual subword units)
- Cross-lingual transfer

### Document-Level Translation
- Context beyond single sentence
- Discourse-aware generation
- Improved pronoun/coherence

---

## Related Pages

- [[shared/transformers]] (detailed architecture)
- [[shared/attention-mechanisms]] (multi-head attention)
- [[nlp/neural-machine-translation]] (RNN-based predecessor)
- [[nlp/syntax-based-smt]] (explicit syntax in SMT)
- [[nlp/course-overview]]
