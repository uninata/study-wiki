# Transformers

**Summary**: Attention-only neural architecture with self-attention replacing recurrence, enabling parallelization and long-range dependencies. Foundation of modern NLP and LLMs.

**Course**: nlp, ml-methods, deep-learning

**Sources**: 08-transformer-and-syntax-in-nmt.pdf, nlp/The Annotated Transformer.md, nlp/The Illustrated Transformer (1).md, nlp/Transformer Architecture_ Attention Is All You Need.md

**Last updated**: 2026-05-03

---

## Overview

The Transformer architecture ("Attention is All You Need", 2017) revolutionized deep learning by replacing recurrent mechanisms with pure attention. Transformers are now foundational for NLP, achieving state-of-the-art on nearly all language tasks and forming the basis of large language models (GPT, BERT, T5, etc.).

---

## Motivation: RNN Limitations

### Sequential Processing Bottleneck

**RNNs process sequentially**: Cannot parallelize over sequence length
- Encoder must process word 1, then 2, ..., then n sequentially
- No way to compute all encoder states in parallel
- Training slow on GPUs (30-100s of seconds per batch)

**Transformers process in parallel**: All positions simultaneously
- All encoder positions computed in one forward pass
- Enables massive parallelization on GPUs/TPUs
- 10-100x speedup in training

### Vanishing Gradients

**RNNs struggle with long-range dependencies**:
- Gradients multiply over many timesteps: $\frac{\partial L}{\partial h_1} = \frac{\partial L}{\partial h_n} \times \prod_{i=1}^{n-1} \frac{\partial h_{i+1}}{\partial h_i}$
- Product of many small terms (<1) → vanishing gradients
- LSTM/GRU mitigate but don't eliminate

**Transformers have direct connections**: Every position connected to every other in one layer
- Gradient path of length $\mathcal{O}(1)$
- No vanishing gradient problem
- Better long-range dependencies

---

## Architecture

### High-Level Structure

**Encoder**:
- Stack of N identical layers (typically 6-12)
- Each layer: Multi-head attention + Feed-forward
- Input: Source tokens + positional encodings
- Output: Contextual source representations

**Decoder**:
- Stack of N identical layers (typically 6-12)
- Each layer: Masked self-attention + Cross-attention + Feed-forward
- Input: Target tokens + positional encodings (teacher forcing)
- Output: Probability distribution over vocabulary

### Encoder Layer

Each encoder layer contains:

1. **Multi-Head Self-Attention** (see [[shared/attention-mechanisms]]):
   - Each position attends to all other positions
   - Learns what to focus on
   - Parallelizable

2. **Feed-Forward Network**:
   - Two linear layers with ReLU
   - Point-wise: applied identically to each position
   - Inner dimension typically $4 \times$ outer dimension

3. **Layer Normalization & Residual Connections**:
   - Normalize activations: stabilizes training
   - Residual: Output = LayerNorm(x + Attention(x))
   - Enables deep networks (6-12+ layers)

### Decoder Layer

Each decoder layer contains:

1. **Masked Self-Attention**:
   - Each position attends only to previous positions
   - Prevents looking ahead during training/generation
   - Maintains autoregressive property

2. **Cross-Attention to Encoder**:
   - Target tokens query; source representations as key/value
   - Learns to align target to source
   - Soft alignment learned during training

3. **Feed-Forward Network**:
   - Same as encoder layer

4. **Layer Normalization & Residual Connections**

### Positional Encoding

**Problem**: Attention is permutation-invariant; doesn't inherently model word order

**Solution**: Add positional information to embeddings

$$PE(pos, 2i) = \sin\left(\frac{pos}{10000^{2i/d_{model}}}\right)$$
$$PE(pos, 2i+1) = \cos\left(\frac{pos}{10000^{2i/d_{model}}}\right)$$

**Properties**:
- Sinusoidal functions at different frequencies
- Can represent any position uniquely
- Can extrapolate to longer sequences (unlike learned embeddings)

---

## Key Advantages

### Parallelization

All positions processed simultaneously (per layer):
- **RNN**: O(n) sequential steps
- **Transformer**: O(1) steps with parallel computation across sequence

**Impact**: 10-100x training speedup on GPUs/TPUs

### Long-Range Dependencies

Direct attention between distant positions:
- **RNN**: O(n) gradient path length
- **Transformer**: O(1) gradient path length

**Impact**: Better handling of 50+ token distances

### Universal Applicability

Same architecture for:
- NLP (translation, summarization, QA, ...)
- Vision (image classification, detection, ...)
- Multimodal (image captioning, video understanding, ...)
- Speech (recognition, synthesis)

### Scaling Properties

Performance improves predictably with:
- Model size (more parameters)
- Training data (more examples)
- Compute (more training time)

---

## Training

### Initialization and Normalization

**Challenge**: Deep networks (6-12+ layers) prone to gradient explosion/vanishing

**Solutions**:

1. **Layer Normalization**: Normalize before each sublayer
   - Normalizes activations to zero mean, unit variance
   - Different from batch normalization (per example, not per batch)
   - Stabilizes very deep networks

2. **Careful Weight Initialization**: Xavier/He initialization

3. **Residual Connections**: Each layer learns residual
   $$\text{Output} = x + f(x)$$
   - Gradient can flow directly through residual connection
   - Enables 6-12+ layer stacks

### Optimization

**Optimizer**: Adam with $\beta_1 = 0.9$, $\beta_2 = 0.98$, $\epsilon = 10^{-9}$ (source: nlp/The Annotated Transformer.md).

**Noam Learning Rate Schedule**:

$$lrate = d_{\text{model}}^{-0.5} \cdot \min(step^{-0.5},\ step \cdot warmup\_steps^{-1.5})$$

- **Warmup phase** (steps $< warmup\_steps$): LR increases linearly
- **Decay phase** (steps $\geq warmup\_steps$): LR decays as $1/\sqrt{step}$
- **Typical**: $warmup\_steps = 4000$; base model trained for 100,000 steps (~12 hours on 8 P100 GPUs)

**Rationale for warmup**: In early training, gradient estimates are noisy. Starting with a small LR that grows linearly prevents instability. After warmup, the inverse-square-root decay provides a smooth schedule that adapts to training dynamics.

### Regularization

- **Dropout**: $P_{drop} = 0.1$ applied to output of each sublayer before residual add + layer norm; also to embedding sums (source: nlp/The Annotated Transformer.md)
- **Label Smoothing**: $\epsilon_{ls} = 0.1$ — instead of one-hot targets, distribute $\epsilon_{ls}$ uniformly across all vocab positions; hurts perplexity but improves BLEU and accuracy
- **Early Stopping**: Monitor validation; stop if plateaus

### Original Paper Hyperparameters ("Attention Is All You Need")

| Hyperparameter | Base Model | Big Model |
|----------------|-----------|-----------|
| Encoder/Decoder layers $N$ | 6 | 6 |
| Model dimension $d_{\text{model}}$ | 512 | 1024 |
| Feed-forward dimension $d_{ff}$ | 2048 | 4096 |
| Attention heads $h$ | 8 | 16 |
| Head dimension $d_k = d_v$ | 64 | 64 |
| Dropout $P_{drop}$ | 0.1 | 0.3 |
| Warmup steps | 4000 | 4000 |

Training data: WMT 2014 EN-DE (~4.5M sentence pairs), EN-FR (36M pairs). Shared BPE vocabulary of ~37,000 tokens.

**Results**: EN-DE: 28.4 BLEU (big model, new state-of-the-art at the time); EN-FR: 41.0 BLEU.

---

## Inference

### Encoder-Decoder Generation

**At Test Time**:

1. **Encode source once** (cached): Encoder processes source fully
2. **Initialize decoder** with `<BEGIN>` token
3. **Autoregressive generation**:
   - Compute attention to encoded source
   - Generate next token
   - Append and repeat

**Difference from training**: No teacher forcing (generate sequentially)

### Beam Search Decoding

Maintain k best partial translations:
```
Iteration 1: Start with <BEGIN>, generate first word
             Expand to all vocabulary: |V| options
             Keep top k

Iteration 2: For each of k hypotheses, append one word
             Compute score: previous score + log P(new word)
             Keep top k

...

Stop when all k hypotheses reach </END> or max length
```

**Complexity**: O(n × k) where n = target length, k = beam width

---

## Variants and Extensions

### Encoder-Only (BERT)

- Only encoder stack
- Masked language modeling during training
- Output: Contextual word representations
- Applications: Classification, NER, QA

### Decoder-Only (GPT)

- Only decoder stack (with masked self-attention)
- Causal language modeling during training
- Output: Next token prediction
- Applications: Generation, few-shot learning, in-context learning

### Efficient Variants

- **Sparse Attention**: Attend to subset of positions → O(n log n) or O(n)
- **Linear Attention**: Approximate softmax → O(n) complexity
- **Hierarchical**: Multi-level attention structure

### Multimodal Transformers

- Vision Transformer (ViT): Process images as patches
- CLIP: Joint image-text training
- Flamingo: Interleaved vision-language generation

---

## Transformer Scaling

### Model Size

Performance scales predictably with parameters:
- Increasing model size (wider/deeper) → better BLEU
- Each doubling of parameters: ~0.3-0.5 BLEU improvement
- Diminishing returns but consistent trend

### Data Size

Performance scales with training data:
- 2x more data → ~0.2-0.3 BLEU improvement
- No plateau observed (scales to billions of examples)
- Fundamental assumption of modern LLMs

### Compute

Training time and parameter efficiency:
- Compute ∝ tokens processed
- More compute → larger models
- Better models via scaling laws

---

## Comparison to Alternatives

| Aspect | RNN | CNN | Transformer |
|--------|-----|-----|-------------|
| **Speed (parallel)** | Slow | Fast | Fast |
| **Long-range deps** | Weak | Weak | Strong |
| **Sequential needed** | Yes | No | No |
| **Parameter count** | Small | Medium | Large |
| **Expressiveness** | Good | Medium | Excellent |
| **Interpretability** | Medium | Low | Medium (attention) |

---

## Applications

### Machine Translation
[[nlp/neural-machine-translation]] via [[shared/sequence-to-sequence]]

### Language Modeling
Foundation for GPT, Claude, etc.
- Predict next token
- Scale to 100B+ parameters
- Excellent few-shot capabilities

### Text Classification
- Remove decoder
- Pool encoder output or use [CLS] token representation
- Fine-tune for downstream task

### Question Answering
- Encode question + passage
- Predict answer span positions

### Summarization
- Encoder-decoder like translation
- Compress document to summary

### Dialogue
- Encode context/history
- Generate response

---

## Limitations and Critiques

1. **Quadratic Complexity**: O(n²) attention cost for long documents
2. **Large Model Size**: Billions of parameters; expensive to train/deploy
3. **Data Hungry**: Need massive corpora for good performance
4. **Black Box**: Attention is interpretable to some degree but full model behavior opaque
5. **Positional Encoding**: Choices matter; no principled way to set

---

## Related Pages

- [[shared/attention-mechanisms]] (multi-head attention mechanism)
- [[shared/sequence-to-sequence]] (encoder-decoder seq2seq)
- [[nlp/transformers-nmt]] (transformers specifically for MT)
- [[nlp/neural-machine-translation]]
- [[nlp/course-overview]]
- [[ml-methods/course-overview]]
- [[deep-learning/course-overview]]
