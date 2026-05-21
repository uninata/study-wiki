# Phrase-Based Machine Translation (PBMT)

**Summary**: Core PBMT pipeline covering phrase extraction from word-aligned data, phrase scoring, stack-based decoding, reordering models, MERT tuning, and the Moses framework.

**Course**: nlp

**Sources**: 05-pbmt.pdf, 05-pbmt-decoding-Koehn-2009.pdf, 05-pbmt-decoding-Haddow-2010.pdf, 00-requirements-and-topics.pdf, nlp/Phrase-based Model - MT Talks.md

**Last updated**: 2026-05-21

---

## Overview

Phrase-Based Machine Translation (PBMT) extends word-based SMT by translating contiguous sequences of words rather than individual words. In PBMT, a "phrase" is any contiguous sequence observed in the training data; it does not need to be a linguistic phrase such as a noun phrase or verb phrase (source: nlp/Phrase-based Model - MT Talks.md).

---

## Phrase Extraction Algorithm

### Key Idea
Rather than translating word-by-word, translate multi-word spans directly from a phrase translation table. The phrase table is a probabilistic dictionary extracted from word-aligned parallel data (source: nlp/Phrase-based Model - MT Talks.md).

### Extraction Procedure

**Input**: Word-aligned sentence pair

**Process** (source: 05-pbmt.pdf; source: nlp/Phrase-based Model - MT Talks.md):
1. Identify all contiguous phrase pairs consistent with word alignment
2. Phrase pair (f̄, ē) is **consistent** if:
   - No alignment links cross phrase boundaries
   - May include unaligned words at phrase edges (null alignments)
3. Extract all such pairs up to maximum length (typically 7 words)

### Example
```
Source alignment: market | economy | in | Germany
                   |-------|         |-----|
Target:          mercado | de economía | en Alemania

Extracted phrases:
- (market, mercado)
- (economy, economía)
- (market economy, mercado de economía)
- (in, en)
- (Germany, Alemania)
- (in Germany, en Alemania)
... and combinations
```

### Consistency Constraint
Ensures phrase pairs respect alignment structure: all alignment points from the selected source span must land inside the selected target span and vice versa (source: nlp/Phrase-based Model - MT Talks.md). If either side has an alignment link outside the candidate span, the phrase pair is rejected.

---

## Phrase Scoring

### Relative Frequency Estimation

**Translation Probability**:
$$P(\bar{e}|\bar{f}) = \frac{\text{count}(\bar{f}, \bar{e})}{\sum_{\bar{e}'} \text{count}(\bar{f}, \bar{e}')}$$

- Probability of target phrase given source phrase
- Estimated as empirical frequency from training data

**Inverse Translation Probability**:
$$P(\bar{f}|\bar{e}) = \frac{\text{count}(\bar{f}, \bar{e})}{\sum_{\bar{f}'} \text{count}(\bar{f}', \bar{e})}$$

The MT Talks phrase-model note frames this as maximum-likelihood estimation: count how often phrase $\bar{f}$ is translated as $\bar{e}$ and divide by the total count of $\bar{f}$ in the extracted phrase table (source: nlp/Phrase-based Model - MT Talks.md).

### Lexical Weighting

Smooths probability of phrase pairs by averaging word-level translation probabilities:

$$\text{lex}(\bar{e}|\bar{f}) = \prod_{i=1}^{|\bar{e}|} \frac{\sum_{j: a_{ij}} p(e_i | f_j)}{|j : a_{ij}|}$$

where $a_{ij}$ indicates alignment between word positions.

- Handles many-to-many alignments by normalizing
- Provides smoothing for rare phrases
- Helps with paraphrasing (similar phrases get similar scores)

### Phrase Penalty
- $\text{pp}(\bar{e}, \bar{f}) = \log(1) = 0$ for all phrases
- Constant feature; uniform cost across all phrases
- Simple but effective baseline

---

## Reordering Models

Before scoring a complete sentence, the decoder first looks up translation options for every source span; these options behave like pieces that must jointly cover the full source sentence (source: nlp/Phrase-based Model - MT Talks.md).

### Stack-Based Beam Search

PBMT decoding starts from an empty hypothesis with no source words covered. Each expansion chooses an uncovered source span, selects one of its translation options, appends the target phrase, and marks the source span as covered in a Boolean coverage vector. A hypothesis is complete when the coverage vector contains no uncovered positions (source: nlp/Phrase-based Model - MT Talks.md).

Because the search space grows rapidly with sentence length, practical PBMT decoders group partial hypotheses into stacks by the number of covered source words and prune each stack to keep only the best-scoring hypotheses. Comparing hypotheses with the same coverage size avoids unfairly ranking short partial translations above longer ones simply because they have accumulated fewer model decisions (source: nlp/Phrase-based Model - MT Talks.md).

Decoders also use a future-cost estimate for the untranslated part of the sentence. Without this estimate, the search tends to translate easy spans first and can prune away hypotheses that are locally worse but globally better; the future cost can be precomputed efficiently with dynamic programming (source: nlp/Phrase-based Model - MT Talks.md).

### Distance-Based Reordering

**Linear Distance Penalty**:
$$\text{reorder}(i) = |(\text{start}_i - \text{end}_{i-1} - 1)|$$

where:
- $\text{start}_i$ = position where phrase $i$ begins
- $\text{end}_{i-1}$ = position where previous phrase ends
- Typical gap: 0-5 words

**Motivation**: Encourage translating source left-to-right; penalize jumping around

**Advantage**: Linear complexity; practical for real-time systems

**Limitation**: All reorderings of same distance equally penalized; ignores linguistic structure

### Lexicalized Reordering

Condition reordering probability on source and target phrase pairs:

$$P(\text{orient} | \bar{f}, \bar{e})$$

**Orientations**:
- **Monotone (M)**: Current phrase follows previous phrase in both source and target
- **Swap (S)**: Current phrase precedes previous phrase in source but follows in target
- **Discontinuous (D)**: Neither monotone nor swap

**Advantage**: More accurate; learns language-specific reordering patterns

**Disadvantage**: Higher computational cost; requires phrase-pair specific model

---

## MERT: Minimum Error Rate Training

### Motivation

Default feature weights (equal importance) are suboptimal. MERT tunes weights to maximize BLEU on development set.

### Algorithm (source: 05-pbmt.pdf)

**Log-Linear Combination**:
$$\hat{e} = \arg\max_e \sum_i \lambda_i f_i(e, f)$$

where $\lambda_i$ are feature weights to optimize.

**Procedure**:
1. Generate n-best lists for each development sentence (using current weights)
2. Line search: For each direction $\vec{d}$, find optimal step size $\alpha$ that maximizes BLEU
   - Iterate over all possible BLEU scores (formed by n-best candidates)
   - Choose $\alpha$ giving highest BLEU
3. Update weights: $\lambda^{(t+1)} = \lambda^{(t)} + \alpha^* \vec{d}$
4. Repeat steps 1-3 until convergence or fixed iterations

### Characteristics

**Advantages**:
- **Direct Metric Optimization**: Tunes to evaluation metric directly (not likelihood)
- **Effective**: Often 1-2 BLEU points improvement over uniform weights
- **Standard**: Widely adopted; expected baseline

**Disadvantages**:
- **Non-Convex**: May find local maxima; multiple starting points helpful
- **Instability**: Small weight changes can cause n-best lists to rerank differently
- **Computational Cost**: Requires n-best decoding multiple times

### Typical Configuration
- **Features**: Phrase probability, lexical weighting, language model, reordering, phrase penalty
- **Development Set**: 1000-2000 sentences for stability
- **Iterations**: 10-30 typically sufficient

---

## Moses Decoder

### Framework Overview (source: 00-requirements-and-topics.pdf)

Moses is the standard open-source PBMT implementation.

**Components**:
- **Phrase Table**: Phrase translation pairs with scores
- **Language Model**: n-gram model (typically 3-gram or 5-gram)
- **Reordering Model**: Distance-based or lexicalized
- **Feature Weights**: Tuned via MERT

**Pipeline**:
1. Parallel corpus → Sentence alignment (hunalign)
2. Word alignment (GIZA++)
3. Phrase extraction
4. Phrase table compilation
5. Language model training
6. MERT tuning
7. Decoding

### Typical System
- Language model: KenLM with 5-grams
- Reordering: Lexicalized, distance $\leq 6$
- Decoding: Beam search, width 200-1000
- Performance: 20-40 BLEU (language pair dependent)

---

## PBMT Strengths and Limitations

### Strengths
- **Interpretable**: Phrase table directly shows translation decisions
- **Efficient**: Practical decoding with reasonable resources
- **Data-Efficient**: Works with moderate parallel data (millions of words)
- **Modular**: Easy to add or remove components

### Limitations
- **Phrase Sparsity**: Rare phrases unseen in training; falls back to word translation
- **Long-Range Reordering**: Difficult to model complex reorderings
- **Compositional Semantics**: Hard to capture entire-sentence meaning
- **Plateauing Performance**: Improvements diminish with more data

### Transition to Neural MT
These limitations motivated neural approaches ([[nlp/neural-machine-translation]]) which:
- Learn dense representations for semantics
- Use attention mechanisms replacing explicit phrase pairs
- Scale better with large parallel data

---

## Related Pages

- [[nlp/word-alignment]] (prerequisite: phrase extraction requires alignment)
- [[nlp/decoding-algorithms]] (PBMT decoding strategies)
- [[nlp/morphology-machine-translation]] (handling morphology in PBMT)
- [[nlp/machine-translation-evaluation]] (MERT uses BLEU)
- [[nlp/neural-machine-translation]] (modern successor)
- [[nlp/smt-foundations]]
- [[nlp/course-overview]]
