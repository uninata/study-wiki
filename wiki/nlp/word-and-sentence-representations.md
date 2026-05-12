# Word and Sentence Representations

**Summary**: Techniques for representing words and sentences as dense vectors, from static embeddings (Word2Vec, GloVe, FastText) to contextual embeddings (ELMo, BERT). Core to modern NLP and machine translation.

**Course**: nlp

**Sources**: 09-word-and-sent-reprs.pdf

**Last updated**: 2026-05-02

---

## Overview

Word and sentence representations are the foundation of neural machine translation. They convert discrete tokens into continuous vectors that capture semantic and syntactic meaning, enabling neural models to generalize across vocabularies and handle morphologically complex languages.

---

## Static Word Embeddings

Static embeddings assign a fixed vector to each word, independent of context (source: nlp/09-word-and-sent-reprs.pdf).

### Word2Vec

Two architectures:
- **Skip-gram**: Predicts context words given a target word. Minimizes negative log-likelihood of predicting surrounding words in a context window.
- **CBOW (Continuous Bag of Words)**: Predicts a target word given context. Faster, better for frequent words.

**Key insight**: Performs implicit dimensionality reduction; word vectors capture semantic relationships (e.g., $\mathbf{v}_{\text{king}} - \mathbf{v}_{\text{man}} + \mathbf{v}_{\text{woman}} \approx \mathbf{v}_{\text{queen}}$).

**Use in MT**: Initializes word embeddings in encoder/decoder; captures monolingual semantic structure. Cross-lingual word2vec trained on parallel corpora enables alignment.

### GloVe (Global Vectors)

Combines count-based (co-occurrence matrix) and prediction-based (neural) approaches.

**Objective**: Minimize difference between dot product of word vectors and log of co-occurrence counts.

**Advantage**: More globally aware than Skip-gram; better for frequent words.

### FastText

Represents words as sums of character n-grams (typically n=3 to n=6).

**Key advantage**: Handles out-of-vocabulary (OOV) words and morphologically rich languages by composing subword units. Critical for low-resource languages with complex morphology (e.g., Turkish, Finnish).

**Use in MT**: Mitigates vocabulary sparsity in morphologically rich source/target languages.

---

## Contextual Embeddings

Context-dependent representations where a word's vector changes based on the surrounding sentence (source: nlp/09-word-and-sent-reprs.pdf).

### ELMo (Embeddings from Language Models)

**Architecture**: Bidirectional LSTM trained as a language model. Outputs representations at each layer; higher layers capture syntax/semantics, lower layers capture surface form.

**Use in MT**: Fine-tuned encoders achieve better representations than frozen embeddings; bidirectional context improves alignment and disambiguation.

### BERT and Transformers

**Architecture**: Multi-layer bidirectional transformer. Each layer performs self-attention over all positions.

**Key property**: Each word's representation is informed by all other words in the sentence via attention. Captures long-range dependencies.

**Advantages over ELMo**:
- Parallel computation (no LSTM sequential bottleneck)
- Stronger contextual modeling (self-attention vs. LSTM)
- Pre-trained on large corpora (better initialization)

**Use in MT**: Encoder for contextual token representations; decoder can use BERT embeddings or transformer architecture throughout.

---

## Sentence Representations

Methods to derive fixed-size sentence vectors from word embeddings (source: nlp/09-word-and-sent-reprs.pdf).

### Simple Pooling

- **Averaging**: Average all word vectors in the sentence. Cheap, works surprisingly well.
- **Max pooling**: Take maximum value per dimension across all words. Captures salient features.
- **Weighted averaging**: Weight by word frequency, TF-IDF, or attention scores.

**Limitation**: Loses word order and long-range structure.

### Transformer Pooling

- **`[CLS]` token**: Special token at sentence start; its final representation serves as sentence embedding (BERT convention).
- **Last hidden state**: Use the final transformer layer's output for all positions, then aggregate.
- **Mean pooling of contextualized embeddings**: Average contextual vectors from a specific layer.

**Advantage**: Captures syntax, semantics, and discourse structure through self-attention.

---

## Application to Machine Translation

### Word Alignment

Static embeddings enable unsupervised word alignment:
1. Train monolingual embeddings on source and target corpora.
2. Align embedding spaces using Procrustes or self-learning (e.g., MUSE).
3. Find nearest neighbors in aligned space for alignment candidates.

**Use**: Initialize translation tables, identify cognates, bridge low-resource pairs.

### Encoder Representations

- **Static**: Initialize encoder with Word2Vec/GloVe; fine-tune during training.
- **Contextual**: Freeze or fine-tune ELMo/BERT; better generalization, especially for morphologically complex languages.

### Decoder Initialization

Output embeddings (for softmax layer) often tied to encoder embeddings; reduces parameters and improves generalization.

---

## Monolingual vs. Multilingual Embeddings

### Monolingual

- Trained on large single-language corpus.
- Optimize for in-language semantic/syntactic relationships.
- Best for high-resource languages; require separate embeddings per language.

### Multilingual

- Trained on mixed-language or parallel corpora.
- Shared vocabulary and embedding space reduce language-specific bottlenecks.
- Enable cross-lingual transfer and zero-shot translation (see [[nlp/multilingual-machine-translation]]).

**Trade-off**: Slightly lower per-language quality, but massive gains for low-resource pairs.

---

## Related pages

- [[nlp/neural-machine-translation]] — Application of embeddings in seq2seq and transformer NMT
- [[nlp/multilingual-machine-translation]] — Multilingual embeddings enable cross-lingual transfer
- [[nlp/morphology-machine-translation]] — FastText and subword segmentation for morphologically rich languages
- [[shared/attention-mechanisms]] — Contextual embeddings use self-attention
- [[shared/transformers]] — Transformer-based contextual embeddings
- [[shared/contextual-embeddings]] — Cross-course canonical page on ELMo, BERT, transformers
- [[shared/word-embeddings]] — Cross-course canonical page on Word2Vec, GloVe, FastText
