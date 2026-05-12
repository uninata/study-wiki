# Word Alignment in Machine Translation

**Summary**: Techniques for determining word correspondences between source and target sentences, including cost function formulations, IBM Models 1-2, and the EM algorithm.

**Course**: nlp

**Sources**: 04-word-alignment-Koehn.pdf, 04-ibm12-notes-Collins.pdf, 04-church-gale-paper.pdf, 04-alignment.pdf

**Last updated**: 2026-04-17

---

## Overview

Word alignment is the process of determining which words in a source sentence correspond to words in a target sentence. This is a foundational step for phrase-based SMT and provides insights into model behavior.

---

## Alignment Problem Formulation

### Representation

An **alignment** is a bipartite graph connecting words in source and target:
- **Nodes**: Source words (left), target words (right)
- **Edges**: Aligned word pairs
- **Many-to-Many**: One source word can align to multiple target words
- **Null Alignments**: Account for insertions/deletions (no counterpart)

### Example
```
Source:  er    geht   ja    nicht   nach   hause
         |     |------|      |-------|      |
Target:  he    goes         does not       home
```

### Alignment Problem
- **Input**: Parallel sentence pair (f, e)
- **Output**: Alignment a specifying which positions are connected
- **Challenge**: Multiple valid alignments often exist

---

## Cost Function Formulation

### Distance Metrics for Alignment

Distance measures provide a principled way to compare and score alignment hypotheses (source: 04-church-gale-paper.pdf). The simplest approach counts positions where alignments differ; more sophisticated approaches assign different costs to different types of operations.

### Edit Distance

**Definition** (source: 04-church-gale-paper.pdf): A distance metric that allows comparing sequences by counting the cost of operations needed to transform one into another.

**Operations and Costs**:
  - **Substitutions**: Change alignment (cost $c_{\text{sub}}$)
  - **Deletions**: Remove alignment link (cost $c_{\text{del}}$)
  - **Insertions**: Add alignment link (cost $c_{\text{ins}}$)
  - **Contractions/Expansions**: Handle many-to-one/one-many alignments
  - **Merging**: Combine multiple elements

**Key insight**: Different weights reflect linguistic priors (e.g., prefer deletions over insertions in certain language pairs). The total cost is minimized using dynamic programming to find the optimal alignment.

### Fertility-Distortion Model

**IBM Models 1-2** use a probabilistic cost function based on (source: nlp/04-word-alignment-Koehn.pdf):

**Fertility** $\varphi(j)$:
- How many target words does source position $j$ produce?
- Example: "market" → ["mercado", "de"] has fertility 2
- Parameters: $P(\varphi(j)|f_j)$

**Distortion** $d(i|j, l_f, l_e)$:
- Word alignment position $i$ given source position $j$
- Depends on sentence lengths
- Example: SVO source → VSO target requires distortion

---

## IBM Models 1 and 2

### IBM Model 1

**Parameters**:
- $t(e|f)$ = translation probability of English word $e$ given foreign word $f$

**Probability Model**:
$$P(e, a | f) = \frac{\lambda^m}{\left(m+1\right)^l} \prod_{j=1}^m t(e_j | f_{a_j})$$

where:
- $m$ = target length; $l$ = source length
- $\lambda$ = normalization constant
- $a_j$ = source position aligned to target position $j$ (can be 0 for null)

**Key Assumption**: All alignments equally likely a priori; no word order consideration

**Learning Algorithm: EM**

*Initialization*: Uniform probabilities $t(e|f) = \frac{1}{|V_e|}$

*E-Step* (Expectation):
- Compute posterior probability of each alignment given current parameters
- $P(a | e, f) \propto \prod_j t(e_j | f_{a_j})$

*M-Step* (Maximization):
- Update translation probabilities as normalized expected counts
- $t(e|f) \propto \sum_{\text{sentences}} \sum_{a \text{ where } f \text{ aligned to } e} P(a|e,f)$

**Convergence**:
- EM guaranteed to increase likelihood
- Converges to local maximum (not global)
- Usually 5-10 iterations sufficient

### IBM Model 2

**Extension**: Add distortion parameters

**Additional Parameters**:
- $d(i|j, l_f, l_e)$ = probability of aligning source position $i$ to target position $j$
- Conditioned on sentence lengths

**Probability Model**:
$$P(e, a | f) = \prod_{j=1}^m t(e_j | f_{a_j}) \cdot d(a_j | j, l_f, l_e)$$

**Benefit**: Captures word-order preferences
- Example: English prepositions precede nouns; German postpositions follow
- Model learns these patterns from data

**Learning**: Same EM algorithm with additional distortion parameter updates

### Model 1 vs. Model 2

| Aspect            | Model 1      | Model 2                   |
| ----------------- | ------------ | ------------------------- |
| **Complexity**    | $O(m × l)$   | $O(m × l × m × l)$        |
| **Parameters**    | $~V_f × V_e$ | $+ m² × l²$ ( distortion) |
| **Quality**       | Baseline     | Better alignment          |
| **Training Time** | Fast         | Slower                    |

---

## Church-Gale Probabilistic Alignment

**Foundational Work** (source: 04-church-gale-paper.pdf):

The Church-Gale paper introduced probabilistic framework for aligning sentences before word-level alignment.

**Key Contributions**:
- Modeled sentence alignment as latent variable problem
- EM algorithm for unsupervised learning from sentence pairs
- Established foundation for IBM Models

**Pipeline**:
1. Sentence-level alignment (find corresponding sentences)
2. Word-level alignment (find corresponding words)

---

## Alignment Tools

### GIZA++ (source: 04-word-alignment-Koehn.pdf)
- **Standard Implementation**: IBM Models 1-5 plus HMM variant
- **Features**: 
  - Forward and reverse alignments (can be symmetrized)
  - Model training with EM
  - Viterbi alignment extraction
- **Integration**: Widely used in Moses ([[nlp/phrase-based-machine-translation]])

### hunalign (source: 00-requirements-and-topics.pdf)
- **Task**: Sentence-level alignment before word alignment
- **Method**: Length-based alignment with dictionary lookup
- **Output**: Sentence pairs ready for word alignment

### Training Pipeline

```
1. Raw Parallel Texts
   ↓
2. Sentence Alignment (hunalign)
   ↓
3. Tokenization & Normalization
   ↓
4. Word Alignment (GIZA++)
   ↓
5. Phrase Extraction (for PBMT)
```

---

## Alignment Quality and Downstream Impact

### Alignment Errors
- **False Positives**: Spurious links between unrelated words
  - Example: English "the" aligning to French "le" incorrectly (function word false friend)
- **False Negatives**: Missing links for correct correspondences
  - Example: Many-to-one alignments missed by single-to-single models

### Propagation to Phrase Table
- **Good Alignment** → Accurate phrase pairs → Better translation
- **Bad Alignment** → Noisy phrase pairs → Translation errors
- **Error Analysis**: Many translation failures trace to alignment errors

### Refinement Strategies
- **Symmetrization**: Combine forward (f→e) and reverse (e→f) alignments
  - Intersection: Keep only links found in both (precision-oriented)
  - Union: Keep all links found in either (recall-oriented)
  - Refined variants: Grow-diag-final (balanced)

---

## Related Pages

- [[nlp/smt-foundations]] (motivation for word alignment)
- [[nlp/phrase-based-machine-translation]] (phrase extraction uses alignment)
- [[nlp/machine-translation-evaluation]]
- [[nlp/course-overview]]
