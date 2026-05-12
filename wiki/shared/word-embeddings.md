# Word Embeddings: Word2Vec, GloVe, FastText

**Summary**: Techniques for representing words as fixed-size dense vectors. Core technique in NLP, ML, and programming; enables downstream models to leverage semantic similarity and supports cross-lingual alignment.

**Course**: nlp, pattern-recognition, programming-for-engineers

**Sources**: nlp/09-word-and-sent-reprs.pdf, programming-for-engineers/l10_visualization.qmd

**Last updated**: 2026-05-02

---

## Overview

Word embeddings map discrete tokens to continuous vectors in a learned space where semantic and syntactic relationships are preserved as geometric relationships (e.g., distance, direction).

**Key property**: Simple vector arithmetic captures analogies:

$$\mathbf{v}_{\text{king}} - \mathbf{v}_{\text{man}} + \mathbf{v}_{\text{woman}} \approx \mathbf{v}_{\text{queen}}$$

---

## Word2Vec

Efficient embedding learning via two shallow neural architectures.

### Skip-gram

**Objective**: Predict context words given target word.

**Input**: Target word $w$
**Output**: Probability distribution over context words within window $[w-c, w+c]$

**Loss**: Negative log-likelihood

$$\mathcal{L} = -\sum_{t} \sum_{\substack{-c \leq j \leq c \\ j \neq 0}} \log P(w_{t+j} \mid w_t)$$

**Use softmax**: 

$$P(w_{\text{out}} \mid w_{\text{in}}) = \frac{\exp(\mathbf{v}_{\text{out}} \cdot \mathbf{v}_{\text{in}})}{\sum_{w} \exp(\mathbf{v}_{w} \cdot \mathbf{v}_{\text{in}})}$$

**Complexity**: Output layer $\mathcal{O}(V)$ where $V$ = vocabulary size. **Negative sampling** reduces to $\mathcal{O}(k)$ by sampling $k$ negative examples per example.

### CBOW (Continuous Bag of Words)

**Objective**: Predict target word given context.

**Input**: Context words $[w_{t-c}, \ldots, w_{t-1}, w_{t+1}, \ldots, w_{t+c}]$
**Output**: Target word $w_t$

**Advantage**: Faster training (fewer predictions per example). Slightly lower quality on rare words.

### Negative Sampling

Core trick enabling scalability. Instead of computing full softmax:

1. Sample k negative examples (random words)
2. Objective: maximize similarity of true pair, minimize for negative pairs

**Noise contrastive estimation**:

$$\mathcal{L} = \log \sigma(\mathbf{v}_{\text{target}} \cdot \mathbf{v}_{\text{context}}) + \sum_{i=1}^{k} \mathbb{E}_{w_i \sim P_n} \left[ \log \sigma(-\mathbf{v}_{w_i} \cdot \mathbf{v}_{\text{context}}) \right]$$

**Effect**: Stochastic approximation of full softmax; 10-100x speedup.

---

## GloVe (Global Vectors for Word Representation)

Combines count-based and prediction-based approaches.

### Motivation

- **Count-based** (LSA, PMI): Leverage global statistics efficiently
- **Prediction-based** (Word2Vec): Learn semantic nuances, generalize better
- **Goal**: Best of both

### Factorization of Co-occurrence

Let $\mathbf{X}$ = word-word co-occurrence matrix; $X_{ij}$ = number of times word $i$ appears in context of word $j$.

**Objective**: Learn vectors $\mathbf{v}_i$ and $\mathbf{v}_j$ such that $\mathbf{v}_i \cdot \mathbf{v}_j \approx \log(X_{ij})$

**Loss** (least squares):

$$\mathcal{L} = \sum_{i,j} f(X_{ij}) (\mathbf{v}_i \cdot \mathbf{v}_j - \log X_{ij})^2$$

where $f(X_{ij}) = \left(\frac{X_{ij}}{X_{\max}}\right)^{\alpha}$ downweights rare co-occurrences.

**Advantage**: Directly optimizes for reproducing co-occurrence patterns. Faster convergence than Word2Vec on fixed corpus.

---

## FastText

Incorporates subword information via character n-grams.

### Motivation

Standard embeddings (Word2Vec, GloVe) treat each word as atomic unit. Problems:
- OOV words have no representation
- Morphologically related words (e.g., "play", "plays", "played") not connected
- Spelling errors break lookup

### Architecture

Represent each word as sum of character $n$-grams (default $n=3$ to $n=6$).

**Example**: "hello"
- Substrings: $\langle \text{he}, \text{hel}, \text{ell}, \text{llo}, \text{lo} \rangle$
- $$\mathbf{v}_{\text{hello}} = \frac{1}{|\mathcal{N}|} \sum_{n \in \mathcal{N}} \mathbf{v}_n$$

where $\mathcal{N}$ is the set of character $n$-grams.

**Advantage**: OOV word "helo" can be represented as sum of overlapping $n$-grams shared with "hello".

**Impact**: 
- Handles typos and morphology naturally
- Critical for morphologically rich languages (Turkish, Finnish, Arabic)
- Enables translation of unseen word forms in low-resource pairs

### Use in Translation

FastText embeddings initialized in encoder vocabularies enable partial generalization to inflected forms not seen during training.

---

## Applications

### Machine Translation

- **Initialization**: Word embeddings initialize encoder/decoder
- **Alignment**: Cross-lingual embeddings (via Procrustes alignment) enable unsupervised word alignment
- **Lexicon induction**: Nearest neighbors in aligned space yield translation pairs

### Similarity and Search

- Compute cosine similarity between word vectors
- Retrieve semantically related words
- Example: "restaurant" → ["cafe", "diner", "bistro"]

### Analogy Solving

Given $a : b :: c : d$, solve for $\mathbf{d}$ as:

$$\mathbf{d} \approx \mathbf{c} + (\mathbf{b} - \mathbf{a})$$

**Example**: "king" is to "queen" as "man" is to "woman"

---

## Comparison and Trade-offs

| Method | Training | OOV | Rare words | Morphology |
|--------|----------|-----|-----------|-----------|
| Word2Vec | Fast | None | Weak | No |
| GloVe | Moderate | None | Strong | No |
| FastText | Fast | Yes | Moderate | Yes |

**Use cases**:
- **Word2Vec**: Quick baseline, standard benchmark
- **GloVe**: When global statistics matter (synonym detection)
- **FastText**: Low-resource, morphologically complex languages

---

## Limitations

1. **Context-insensitive**: Same vector for polysemes ("bank" as riverbank vs. financial institution)
2. **Frozen in time**: Embeddings fixed after training; can't adapt to new words or usages
3. **Limited to word-level**: Don't capture phrase or sentence-level semantics
4. **Dimensionality**: Trade-off between expressiveness (high-D) and efficiency (low-D)

**Solutions**: Contextual embeddings ([[shared/contextual-embeddings]]) address these by generating position-dependent vectors.

---

## Related pages

- [[shared/contextual-embeddings]] — Context-dependent alternatives (ELMo, BERT)
- [[nlp/word-and-sentence-representations]] — Sentence representations built from word embeddings
- [[nlp/neural-machine-translation]] — Embeddings in encoder initialization
- [[nlp/multilingual-machine-translation]] — Cross-lingual embeddings for transfer
- [[pattern-recognition/non-parametric-methods]] — KNN in embedding space
- [[programming-for-engineers/numpy-fundamentals]] — Vector operations and similarity computation
