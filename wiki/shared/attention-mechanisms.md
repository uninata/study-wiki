# Attention Mechanisms

**Summary**: Core attention mechanism allowing neural networks to focus on relevant parts of input, including scaled dot-product attention, multi-head attention, and applications across NLP and vision.

**Course**: nlp, ml-methods, deep-learning

**Sources**: 03-nmt-seq2seq-attn.pdf, 08-transformer-and-syntax-in-nmt.pdf

**Last updated**: 2026-04-17

---

## Overview

Attention mechanisms enable neural networks to selectively focus on relevant parts of input rather than processing uniformly. Originally developed for sequence-to-sequence models in NLP, attention is now fundamental across deep learning.

---

## Scaled Dot-Product Attention

### Core Mechanism

Given **queries** (Q), **keys** (K), and **values** (V):

$$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right) V$$

### Components

**Queries** ($Q = X W^Q$):
- What information are we looking for?
- Typically: decoder hidden state (what does decoder need?)

**Keys** ($K = X W^K$):
- What information is available?
- Typically: encoder hidden states (what can encoder provide?)

**Values** ($V = X W^V$):
- What information to retrieve?
- Typically: encoder representations (actual information content)

### Computation Steps

1. **Alignment Scores**:
   $$\text{score}(Q, K) = Q \cdot K^T$$
   - Dot product between queries and keys
   - High score = good match between query and key

2. **Scaling**:
   $$\frac{\text{score}(Q, K)}{\sqrt{d_k}}$$
   - Prevent dot product from growing too large (gradient saturation)
   - $d_k$ = dimensionality of keys (typically 64)

3. **Normalization** (softmax):
   $$\alpha = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)$$
   - Compute attention weights (probabilities)
   - Row-wise softmax: sum to 1 across all keys

4. **Output**:
   $$\text{Output} = \alpha V$$
   - Weighted combination of values
   - Focus on high-attention values

### Properties

- **Differentiable**: Can backpropagate through all operations
- **Parallel**: All attention weights computed simultaneously (O(n²) but parallelizable)
- **Interpretable**: Attention weights show what model focuses on

---

## Multi-Head Attention

### Motivation

Single attention head may not capture diverse relationships:
- Syntactic roles (subject, verb, object)
- Semantic relationships (synonymy, hypernymy)
- Positional relationships (nearby vs. distant)

### Architecture (source: 08-transformer-and-syntax-in-nmt.pdf)

**Multiple Parallel Attention Heads**:

$$\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, ..., \text{head}_h) W^O$$

where each head:
$$\text{head}_i = \text{Attention}(Q W_i^Q, K W_i^K, V W_i^V)$$

### Process

1. Project Q, K, V into h subspaces (via learned matrices $W_i^{Q,K,V}$)
2. Apply attention in each subspace independently
3. Concatenate outputs: $[\text{head}_1; \text{head}_2; ...; \text{head}_h]$
4. Project via $W^O$ to original dimension

### Example with h=8 heads

```
Input: X (batch_size, seq_len, d_model=512)

For each of 8 heads:
  Project to d_k = 64 dimension
  Apply scaled dot-product attention
  Output: (batch_size, seq_len, 64)

Concatenate: (batch_size, seq_len, 512)
Project via W^O: (batch_size, seq_len, d_model)
```

### Benefits

- **Specialized Heads**: Different heads learn different relationships
- **Robustness**: Multiple representations more stable
- **Expressiveness**: Can model multiple simultaneous relationships
- **Parallelizable**: All heads computed in parallel

### Empirical Findings

Different heads attend to different phenomena:
- Some heads focus on nearby tokens (local context)
- Some heads focus on distant tokens (discourse)
- Some heads track syntactic structure
- Some heads track semantic relationships

---

## Variations of Attention

### Additive Attention (Bahdanau)

$$\text{Attention}(Q, K, V) = \text{softmax}\left(v^T \tanh(W[Q; K])\right) V$$

**Difference from scaled dot-product**:
- Uses learned weight matrices instead of direct dot product
- Often works similarly; slightly higher computational cost

### Multi-Query Attention

Multiple queries attend to single key-value pair (efficiency variant)

### Cross-Attention vs. Self-Attention

**Cross-Attention**: Q from one sequence, K and V from another
- Example: Decoder queries (target), encoder keys/values (source)
- Enables sequence alignment

**Self-Attention**: Q, K, V from same sequence
- Example: Each word attends to all words in sentence
- Captures intra-sequence relationships

---

## Applications

### Sequence-to-Sequence (source: 03-nmt-seq2seq-attn.pdf)

Decoder attends to encoder outputs:
- Q from decoder hidden state
- K, V from encoder representations
- Aligns target to source at each generation step

### Transformers (source: 08-transformer-and-syntax-in-nmt.pdf)

Both encoder and decoder use self-attention:
- Encoder self-attention: Model source word relationships
- Decoder self-attention: Model target word relationships (masked)
- Cross-attention: Align target to source

### Vision Attention

Image transformer (ViT) applies attention to image patches:
- Treats image as sequence of patches
- Attention captures long-range spatial relationships
- Competitive with convolutional architectures

---

## Attention Visualization and Interpretation

### Alignment Patterns

Attention weights reveal learned alignments:
```
Target: the   dog   barks
Source: der   Hund  bellt

Attention heatmap:
        der  Hund  bellt
    the [0.8  0.1   0.1]
    dog [0.1  0.8   0.1]
barks   [0.1  0.1   0.8]
```

Diagonal pattern indicates left-to-right alignment (good for similar word order)

### Reordering Patterns

Attention reveals reordering:
```
Target: the   cat   on   table
Source: die   Katze auf   tisch

German word order (die Katze auf dem Tisch) requires reordering
Attention patterns show off-diagonal structure capturing this
```

### Head Specialization

Different heads focus on different phenomena:
- Some heads: attend to nearby tokens (local)
- Some heads: attend to distant tokens (discourse)
- Some heads: attend to specific syntactic roles (dependency structure)

---

## Computational Complexity

### Scaled Dot-Product Attention

$$\text{Complexity} = O(n^2 \cdot d_k)$$

where:
- $n$ = sequence length
- $d_k$ = key dimension

**Bottleneck**: Quadratic in sequence length
- For n=512: 512² = 262K comparisons
- For n=2048: 2048² = 4M comparisons
- Very long sequences (100K+) problematic

### Multi-Head Attention

$$\text{Complexity} = O(n^2 \cdot d_k \cdot h)$$

- h heads computed in parallel
- Overall quadratic per head

---

## Efficient Attention Variants

### Sparse Attention

Only attend to subset of positions:
- Local attention: window of nearby tokens
- Strided attention: every k-th token
- Result: O(n) complexity

### Linear Attention

Approximate softmax with kernel functions:
- Linformer: O(n) attention
- Linear Transformers: O(n) complexity
- Trade-off: Slightly lower quality for efficiency gain

### Hierarchical Attention

Multi-level attention structure:
- Local attention within blocks
- Long-range attention between blocks
- Combines local and global context

---

## Related Pages

- [[nlp/neural-machine-translation]] (RNN-based attention)
- [[shared/transformers]] (multi-head attention in transformers)
- [[shared/sequence-to-sequence]] (attention in seq2seq models)
- [[nlp/course-overview]]
- [[ml-methods/course-overview]]
- [[deep-learning/course-overview]]
