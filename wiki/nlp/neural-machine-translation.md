# Neural Machine Translation

**Summary**: End-to-end neural translation using encoder-decoder RNN architectures, attention mechanisms, autoregressive decoding, and beam search.

**Course**: nlp

**Sources**: 03-nmt-seq2seq-attn.pdf, 02-smt-pbmt-nmt.pdf

**Last updated**: 2026-04-17

---

## Overview

Neural Machine Translation (NMT) is an end-to-end neural network approach that directly models P(target|source) without explicit alignment or phrase tables. NMT has been the dominant paradigm since ~2015.

---

## Encoder-Decoder Architecture

### Conceptual Framework

The encoder-decoder model decomposes translation into two stages:

1. **Encoder**: Read source sentence; produce continuous representation (context vector)
2. **Decoder**: Read context vector; generate target sentence word-by-word

### Encoder

**Role**: Process source sentence left-to-right; extract meaning

**Architecture** (source: 03-nmt-seq2seq-attn.pdf):
- RNN/LSTM/GRU cell processes words sequentially
- Input: Source word embeddings
- Output: Hidden states for each source position
- Final hidden state: Context vector summarizing entire source

**Example Flow**:
```
Source: "Das Haus ist groß"
        ↓
Embeddings: [embed(Das), embed(Haus), embed(ist), embed(groß)]
        ↓
RNN/LSTM cells (forward pass):
h₁ = RNN(embed(Das), h₀)
h₂ = RNN(embed(Haus), h₁)
h₃ = RNN(embed(ist), h₂)
h₄ = RNN(embed(groß), h₃)
        ↓
Context vector: h₄
```

**Bottleneck Issue**: Fixed-size vector must capture all source information; problematic for long sentences

### Decoder

**Role**: Generate target translation word-by-word conditioned on context and previous target words

**Architecture**:
- RNN/LSTM/GRU initialized with encoder final state
- Autoregressive: Each word conditioned on:
  - Context vector (from encoder)
  - All previously generated target words
  - Current hidden state
- Output: Probability distribution over target vocabulary at each step

**Generation Process**:
```
Initialization: s₀ = h_encoder

Step 1: P(y₁|context) via softmax(W·s₀)
        Sample/argmax → y₁ (e.g., "Das")
        
Step 2: s₁ = RNN(embed(y₁), s₀)
        P(y₂|y₁, context) via softmax(W·s₁)
        Sample/argmax → y₂ (e.g., "Haus")

... continue until END token
```

---

## Attention Mechanism

### Motivation

The fixed context vector is a **bottleneck**: Cannot store all source information for long sentences.

**Solution**: Allow decoder to **attend to** (selectively focus on) relevant parts of source during each decoding step.

### Mechanism (source: 03-nmt-seq2seq-attn.pdf)

**At each decoding step t**:

1. **Compute Attention Scores**:
   $$\text{score}(s_t, h_i) = \frac{s_t \cdot h_i}{\sqrt{d_k}}$$
   - $s_t$ = decoder hidden state at step t
   - $h_i$ = encoder hidden state at source position i
   - Scaling factor $\sqrt{d_k}$ prevents gradient saturation ($d_k$ = hidden dimension)

2. **Alignment Weights**:
   $$\alpha_{t,i} = \frac{\exp(\text{score}(s_t, h_i))}{\sum_j \exp(\text{score}(s_t, h_j))}$$
   - Softmax over all source positions
   - Interpretable as "attention" probability

3. **Context Vector**:
   $$c_t = \sum_i \alpha_{t,i} \cdot h_i$$
   - Weighted combination of encoder states
   - Focus on high-attention positions

4. **Decoder Update**:
   $$\tilde{s}_t = \tanh(W[\tilde{s}_{t-1}; c_t])$$
   - Concatenate previous state and context
   - Nonlinear transformation

5. **Output Distribution**:
   $$P(y_t | y_{1:t-1}, \mathbf{f}) = \text{softmax}(W' \tilde{s}_t)$$

### Visualization

Attention weights reveal alignment learned without explicit supervision:
```
English:    the   house  is   large
German:  Das Haus ist groß
         ↑   ↑    ↑   ↑
       (high attention weights reveal learned word order)
```

**Interpretation**: Diagonal pattern indicates reordering; clusters indicate phrases.

---

## Autoregressive Decoding

### Definition

The decoder generates translation **one word at a time**, conditioning on all previously generated words.

### Process

**At step t**:
- Input: Target words y₁, ..., y_{t-1} (gold words during training)
- Output: Probability P(y_t | y_1,...,y_{t-1}, context)
- Action: Sample or select argmax to get y_t

**During Training** (source: 03-nmt-seq2seq-attn.pdf):
- Use gold target words as input (teacher forcing)
- Efficient parallel training possible (no need to generate words sequentially)

**During Inference**:
- Use model-generated words as input
- Must generate sequentially (cannot parallelize)
- Slower but necessary for actual translation

### Exposure Bias Problem

**Mismatch** between training and inference:
- Training: Condition on gold words (always correct)
- Inference: Condition on model predictions (potentially wrong)
- Early errors compound; error accumulation over long sentences

**Mitigation Strategies**:
- Scheduled sampling: Mix gold and predicted words during training
- Beam search: Multiple hypotheses reduce impact of early errors
- Longer training: Model learns to recover from errors

---

## Beam Search Decoding

### Motivation

Greedy decoding (always pick highest probability word) is suboptimal:
```
P(y₁="the") = 0.9    (good)
P(y₂="big"|"the") = 0.5    (bad)
vs.
P(y₁="a") = 0.8     (good)
P(y₂="big"|"a") = 0.8     (better overall)

Greedy: pick "the" → 0.9 × 0.5 = 0.45
Optimal: pick "a" → 0.8 × 0.8 = 0.64
```

### Algorithm (source: 03-nmt-seq2seq-attn.pdf)

**Maintain k best hypotheses** (beam width, typically 3-5):

```
Initialize: H = {(empty, log_prob=0)}

For each step:
  For each hypothesis h in H:
    For each word w in vocabulary:
      Score new hypothesis h' = h + w
      Add h' to candidates
  
  Sort candidates by log probability
  H = top k hypotheses (by cumulative score)
  
  If all hypotheses end with END token: done
```

### Complexity

$$\text{Time} = O(n \times k \times |V|)$$

where:
- $n$ = target sentence length
- $k$ = beam width
- $|V|$ = vocabulary size

**Practical**: With vocabulary pruning and GPU parallelization, tractable.

### Quality Trade-off

| Beam Width | BLEU | Latency |
|------------|------|---------|
| 1 (greedy) | 28.5 | 100ms |
| 3 | 29.1 | 110ms |
| 5 | 29.3 | 120ms |
| 10 | 29.4 | 140ms |

- Returns diminish beyond width 5
- Typical production: 3-5

---

## Training

### Objective Function

**Cross-Entropy Loss** over target vocabulary:

$$\mathcal{L} = -\sum_t \log P(y_t | y_{1:t-1}, \mathbf{f})$$

- Sum over all target positions
- Optimize expected likelihood

### Optimization

- **Optimizer**: Adam, SGD with learning rate scheduling
- **Gradient Computation**: Backpropagation through entire sequence
- **Batch Size**: 32-128 (GPU memory limited)
- **Gradient Clipping**: Prevent gradient explosion in RNNs

### Regularization

- **Dropout**: Randomly zero activations during training (prevents co-adaptation)
- **Early Stopping**: Stop training when validation BLEU plateaus
- **Scheduled Sampling**: Mix gold and predicted inputs during training (address exposure bias)

---

## Advantages over PBMT

1. **Semantic Compositionality**: Learned dense representations capture meaning
2. **Implicit Alignment**: Attention learns alignment without supervision
3. **Long-Range Dependencies**: RNNs (LSTM/GRU) handle long-distance relationships
4. **Scalability**: Improves with more data; PBMT plateaus
5. **Unified Pipeline**: No separate phrase extraction, alignment, MERT tuning
6. **Soft Reordering**: Attention provides flexible reordering

## Limitations

1. **Slow Inference**: Sequential word generation much slower than PBMT
2. **Data Hunger**: Needs large parallel data (millions of words) for best results
3. **Exposure Bias**: Error accumulation during generation
4. **Hyperparameter Sensitivity**: Many design choices (RNN type, hidden dimension, etc.)

---

## Evolution to Transformers

While RNN-based NMT improved translation quality, it has drawbacks:
- **Sequential Processing**: Cannot parallelize over source/target length
- **Vanishing Gradients**: LSTM/GRU mitigate but don't eliminate
- **Latency**: Slow inference due to sequential generation

**Transformers** ([[shared/transformers]]) address these via attention-only architecture.

---

## Related Pages

- [[shared/sequence-to-sequence]] (seq2seq architecture)
- [[shared/attention-mechanisms]] (attention mechanism details)
- [[nlp/phrase-based-machine-translation]] (predecessor approach)
- [[nlp/machine-translation-evaluation]]
- [[nlp/transformers-nmt]] (modern variant)
- [[nlp/course-overview]]
