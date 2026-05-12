# Contextual Embeddings: ELMo, BERT, and Transformers

**Summary**: Context-dependent word representations where each word's vector depends on its position in the sentence. Captures syntax, semantics, and long-range dependencies. Foundational for modern NLP.

**Course**: nlp, deep-learning, pattern-recognition

**Sources**: nlp/09-word-and-sent-reprs.pdf, deep-learning/08-neural-networks.pdf

**Last updated**: 2026-05-02

---

## Overview

Contextual embeddings assign a unique vector to each word occurrence based on surrounding context. Unlike static embeddings, the same word has different vectors in different sentences.

**Key advantage**: Polysemy is handled naturally. "Bank" in "river bank" and "savings bank" get different vectors.

---

## ELMo (Embeddings from Language Models)

**Architecture**: Bidirectional multi-layer LSTM trained as a language model.

### Forward and Backward LSTMs

Two independent LSTM networks:
- **Forward**: Predicts word $w_t$ given words $w_1, \ldots, w_{t-1}$
- **Backward**: Predicts word $w_t$ given words $w_{t+1}, \ldots, w_n$

Predictions are combined probabilistically (independent).

**Loss**: Negative log-likelihood of forward and backward language modeling

$$\mathcal{L} = -\sum_{t} \left[ \log P(w_t \mid w_1, \ldots, w_{t-1}) + \log P(w_t \mid w_{t+1}, \ldots, w_n) \right]$$

### Layer-wise Representations

Output **three** representation levels:
1. **Layer 0**: Character-based word embedding (via CNN over characters)
2. **Layer 1**: Hidden state of first LSTM layer
3. **Layer 2**: Hidden state of second LSTM layer

Each layer captures different linguistic phenomena:
- Layer 0: Surface morphology
- Layer 1: Syntax and local semantics
- Layer 2: High-level semantic roles

### Use of Task-Specific Weights

For downstream tasks, compute weighted combination:

$$\text{Representation} = \gamma \left( w_0 \cdot \text{Layer}_0 + w_1 \cdot \text{Layer}_1 + w_2 \cdot \text{Layer}_2 \right)$$

where $\gamma$ is a task scaling factor and $w_i$ are learned weights.

**Benefit**: Different tasks use different layers (NER emphasizes layer 1; SRL emphasizes layer 2).

### Advantages

- Bidirectional; sees full context
- Pre-trained on large corpus; transfer to downstream tasks
- Interpretable: layer structure reveals linguistic levels

### Limitations

- LSTM sequence bottleneck: $\mathcal{O}(n)$ runtime
- Two-layer limit (going deeper doesn't help much)
- Not pre-trained on cross-lingual data

---

## BERT (Bidirectional Encoder Representations from Transformers)

**Architecture**: Multi-layer bidirectional transformer encoder.

### Masked Language Modeling (MLM)

**Objective**: Predict masked tokens given context.

1. Randomly mask 15% of input tokens
2. Model predicts masked tokens using full bidirectional context
3. Loss: Cross-entropy on masked positions only

**Advantage over LM**:
- Fully bidirectional (not autoregressive)
- Can attend to all positions simultaneously
- Stronger contextual modeling

### Next Sentence Prediction (NSP)

**Objective**: Predict if sentence B follows sentence A (binary classification).

- 50% of time: B is true next sentence
- 50% of time: B is random sentence

**Benefit**: Captures sentence-level relationships (discourse structure).

### Architecture Details

- Multi-head self-attention: Each position attends to all others
- Layer normalization, residual connections
- Feed-forward sublayers ($2 \times$ hidden size)
- Positional encoding: Learned embeddings (not sinusoidal)

### Representations

- **Token-level**: Hidden state of any layer serves as token embedding
- **Sentence-level**: Special `[CLS]` token at start; its representation summarizes the sentence
- **Layer selection**: Different tasks benefit from different layers

### Training and Transfer

1. **Pre-training**: MLM + NSP on large corpus (Wikipedia, BookCorpus)
2. **Fine-tuning**: Replace final layer with task-specific classifier
3. **Few-shot**: Frozen BERT features + linear classifier

### Advantages

- Parallel computation (no sequential LSTM bottleneck)
- Stronger bidirectional modeling than ELMo
- Massive pre-trained models available (e.g., BERT-base: 12 layers, 110M parameters)
- Excellent transfer to diverse downstream tasks

### Variants

- **RoBERTa**: Improved pre-training (longer training, more data)
- **ALBERT**: Parameter sharing across layers; smaller model
- **DistilBERT**: 40% smaller, 60% faster, retains 97% performance
- **mBERT**: Multilingual (104 languages), enables zero-shot cross-lingual transfer

---

## Transformer-Based Contextual Embeddings

**General pattern**: Encoder-only transformers for context, encoder-decoder for generation.

### T5

**Architecture**: Encoder-decoder transformer.

**Pre-training**: Text-to-text framework. All tasks reformulated as text generation.
- Example: Sentiment = "sentiment: My movie was great" → "positive"
- Example: Translation = "translate English to French: Hello" → "Bonjour"

**Benefit**: Single unified architecture; transfer across diverse tasks.

### GPT / GPT-2 / GPT-3

**Architecture**: Decoder-only transformer (autoregressive).

**Pre-training**: Next-token prediction (language modeling).

**Key property**: Generates text autoregressively. Contextual embeddings emerge from latent representations in decoder.

**Scaling**: Larger models (GPT-3: 175 billion parameters) exhibit few-shot in-context learning.

### XLM-R

**Multilingual transformer**: Pre-trained on 100+ languages.

**Architecture**: RoBERTa applied to multilingual corpus.

**Use**: Cross-lingual transfer; zero-shot translation via multilingual embeddings.

---

## Comparison

| Method | Bidirectional | Training Speed | Transfer Quality | Parallelizable | Interpretability |
|--------|---------------|----------------|------------------|---|---|
| ELMo | Yes | Slow (LSTM) | Strong | No | Good (layers) |
| BERT | Yes | Moderate | Very strong | Yes | Good |
| T5 | Yes (enc), Auto (dec) | Moderate | Very strong | Yes | Moderate |
| GPT-2 | No (autoregressive) | Fast | Strong | Yes | Moderate |

---

## Applications in NLP

### Machine Translation

- **Encoder initialization**: Replace BiLSTM encoder with BERT/mBERT
- **Multilingual MT**: mBERT/XLM-R shared representation space enables zero-shot pairs
- **Pre-training**: Fine-tune pre-trained transformer encoder/decoder improves BLEU by 2–5%

### Named Entity Recognition

- Token-level task: Use token embeddings from BERT
- Fine-tune BERT + add CRF decoder
- State-of-the-art on most benchmarks

### Semantic Similarity

- Compute cosine similarity between `[CLS]` representations
- Captures semantic relatedness better than static embeddings

### Question Answering

- Encode question and passage together
- Fine-tune span prediction layer (start and end positions)

---

## Limitations

1. **Fixed length**: Most transformers have max sequence length (512 for BERT)
2. **Computational cost**: O(n²) attention over all positions
3. **Task-specific fine-tuning**: Pre-trained weights must be adjusted per task
4. **Language bias**: Multilingual models (mBERT) have reduced quality for underrepresented languages
5. **Knowledge cutoff**: Embeddings fixed at pre-training time; can't adapt to new facts or domains

**Partial solutions**:
- Longer attention windows (Longformer, BigBird)
- Efficient attention patterns (Linformer, Performer)
- Domain-specific pre-training (SciBERT for scientific text)
- Continued pre-training on target domain

---

## Related pages

- [[shared/word-embeddings]] — Static embeddings (Word2Vec, GloVe, FastText)
- [[shared/transformers]] — Transformer architecture details
- [[shared/attention-mechanisms]] — Self-attention mechanism
- [[nlp/word-and-sentence-representations]] — Application to sentence embeddings
- [[nlp/neural-machine-translation]] — Contextual embeddings in NMT
- [[deep-learning/neural-networks]] — Bidirectional architectures
- [[pattern-recognition/neural-networks]] — LSTM and backprop fundamentals
