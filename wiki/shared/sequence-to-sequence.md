# Sequence-to-Sequence Models

**Summary**: Encoder-decoder neural architecture for generating variable-length output sequences from variable-length input sequences, with applications in translation, summarization, and dialogue.

**Course**: nlp, deep-learning

**Sources**: 03-nmt-seq2seq-attn.pdf

**Last updated**: 2026-04-17

---

## Overview

Sequence-to-sequence (seq2seq) models encode variable-length input sequences into fixed-size context vectors, then decode into variable-length output sequences. The architecture enabled neural approaches to machine translation and many other sequence generation tasks.

---

## Architecture

### Encoder

**Role**: Read input sequence; produce compact representation

**Process**:
1. Embed input tokens into continuous vectors
2. Process sequentially with RNN/LSTM/GRU
3. Each timestep: maintain hidden state
4. Final hidden state: context vector capturing entire sequence

**Implementation**:
```python
context = RNN(embed(x₁), RNN(embed(x₂), ... RNN(embed(xₙ), h₀)))
```

**Example: Machine Translation**
- Input: German sentence "Das Haus ist groß"
- Encoder: RNN processes word-by-word
- Context: Final hidden state represents "big house in German"

### Decoder

**Role**: Generate output sequence using context from encoder

**Process**:
1. Initialize with encoder final state (context)
2. Generate one output token at a time
3. Each step: condition on context and all previous target words
4. Feed generated token as input to next step

**Autoregressive Generation**:
```
Step 1: P(y₁ | context) → sample y₁ = "The"
Step 2: P(y₂ | y₁, context) → sample y₂ = "house"
Step 3: P(y₃ | y₁, y₂, context) → sample y₃ = "is"
...
Step n: P(yₙ | y₁...yₙ₋₁, context) → sample yₙ = "<END>"
```

**During Training** (teacher forcing):
- Use gold target words as input
- Enables parallel training over all timesteps
- Efficient on GPUs

**During Inference**:
- Use model-generated words
- Must generate sequentially
- Slower but necessary for actual prediction

### Context Vector Bottleneck

**Problem**: Fixed-size vector must store all input information
- Information loss for long sequences
- Limits translation quality
- Hard to capture distant dependencies

**Solution**: Attention mechanisms [[shared/attention-mechanisms]]
- Decoder attends to relevant parts of input
- Dynamic context from input

---

## Attention-Enhanced Seq2Seq

### Motivation

Fixed context vector insufficient for long sequences:
- Translation of 50-word sentence must fit in single vector
- Information degradation; attention loss

### Mechanism

At each decoding step t:

1. **Compute attention weights** over encoder states
   - Query: decoder hidden state $s_t$
   - Keys: encoder hidden states $h_1, ..., h_n$
   - Output: attention weights $\alpha_t$

2. **Form context vector** as weighted combination
   $$c_t = \sum_i \alpha_{t,i} h_i$$

3. **Update decoder state** with context
   $$\tilde{s}_t = f(s_{t-1}, c_t)$$

4. **Generate token** from updated state
   $$P(y_t | y_{1:t-1}, f) = \text{softmax}(W \tilde{s}_t)$$

### Alignment Discovery

Attention weights naturally learn word alignment without supervision:
```
Source (German):  Das  Haus  ist  groß
Target (English): The  house is   big

After training, attention shows:
- "The" attends mainly to "Das" (article alignment)
- "house" attends mainly to "Haus" (noun alignment)
- "is" attends mainly to "ist" (verb alignment)
- "big" attends mainly to "groß" (adjective alignment)
```

Diagonal pattern indicates monotone alignment (no reordering)

---

## Training

### Objective Function

**Cross-Entropy Loss**:
$$\mathcal{L} = -\sum_{t=1}^{T} \log P(y_t | y_{1:t-1}, \mathbf{f})$$

- Sum over all target positions
- Each term: negative log probability of gold token
- Optimize expected likelihood via SGD

### Optimization Details

- **Optimizer**: SGD with momentum or Adam
- **Batch Size**: Typically 32-64 (GPU memory bound)
- **Gradient Clipping**: Prevent explosion in RNNs
- **Learning Rate Scheduling**: Decay over time

### Regularization

- **Dropout**: Randomly disable units during training
- **L2 Regularization**: Penalize large weights
- **Early Stopping**: Monitor validation BLEU; stop if plateaus

---

## Decoding

### Inference Modes

**Greedy Decoding**:
- Always pick highest probability word
- Fast but suboptimal (earlier mistakes compound)

**Beam Search**:
- Maintain k best hypotheses
- Expand each by one token
- Prune to k best overall
- Better quality but slower

**Temperature Sampling**:
- Scale softmax by temperature $\tau$
- $\tau < 1$: peaks sharpened (less randomness)
- $\tau > 1$: distribution flattened (more randomness)
- Useful for diverse generation

### Length Penalties

**Problem**: Beam search biased toward short sequences
- Shorter sequences have fewer probability multiplications
- Score = product of probabilities; shorter = higher

**Solution**: Length normalization
$$\text{score} = \frac{\log P(y)}{|y|^\alpha}$$

- Divide by normalized length
- Typical: $\alpha = 0.6$ (balance quality and length)

---

## Variants and Extensions

### Bidirectional Encoder

**Improvement**: Use both left-to-right and right-to-left RNNs
- Concatenate forward and backward hidden states
- Encoder sees full context in both directions
- Better representations

### Multi-Layer Encoder/Decoder

Stack multiple RNN layers:
- Typically 2-4 layers
- Each layer: input from previous layer
- Deeper models can learn more complex transformations

### Residual Connections

**Addition to multi-layer**:
- Skip connections from one layer to next
- Stabilize training of deep networks
- Enable much deeper architectures

### Bidirectional Decoding

Generate left-to-right and right-to-left simultaneously:
- Ensemble predictions
- Sometimes better quality
- Slower (2x generation)

---

## Applications Beyond Translation

### Summarization
- Input: Long document
- Output: Short summary
- Context vector captures document essence

### Dialogue
- Input: User query
- Output: System response
- Seq2seq learns conversational patterns

### Image Captioning
- Input: Image (CNN encoder instead of RNN)
- Output: Text description
- Combine vision and language

### Code Generation
- Input: Function specification
- Output: Code
- Seq2seq learns programming patterns

### Paraphrase Generation
- Input: Sentence
- Output: Semantically equivalent sentence
- Learns to rephrase without changing meaning

---

## Limitations

1. **Fixed Bottleneck**: Context vector finite size; information loss
   - Attention mitigates but doesn't eliminate

2. **Sequential Inference**: Must generate one word at a time
   - Slow for real-time systems
   - Error accumulation (exposure bias)

3. **RNN Computational Cost**: Sequential processing hard to parallelize
   - Slow training compared to feedforward models
   - Transformers [[shared/transformers]] solve this

4. **Local Optima**: Non-convex optimization may converge to suboptimal solutions

---

## Evolution to Transformers

Seq2seq with RNNs gave way to Transformer seq2seq for several reasons:

1. **Parallelization**: Transformers process all positions simultaneously
2. **Long-Range Dependencies**: Self-attention eliminates RNN bottleneck
3. **Training Speed**: Transformers 10-100x faster on GPUs
4. **Translation Quality**: Better on long sequences

Modern translation systems are almost exclusively Transformer-based.

---

## Related Pages

- [[shared/attention-mechanisms]] (attention mechanism details)
- [[shared/transformers]] (evolved seq2seq architecture)
- [[nlp/neural-machine-translation]] (seq2seq for MT)
- [[nlp/course-overview]]
- [[deep-learning/course-overview]]
