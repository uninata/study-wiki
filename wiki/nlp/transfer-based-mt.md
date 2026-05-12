# Transfer-Based Machine Translation (TectoMT)

**Summary**: Transfer-based MT uses an analysis → transfer → synthesis pipeline. TectoMT, developed at Charles University, transfers at the deep-syntactic (tectogrammatical) layer, abstracting away surface morphology and word order to enable better Czech-English translation.

**Course**: nlp

**Sources**: 07-syntax-in-smt.pdf, Deep Syntax.md, Dependency Trees - MT Talks.md

**Last updated**: 2026-05-03

---

## Overview

Transfer-based MT divides translation into three linguistically motivated stages:

```
Source text
    ↓  Analysis
Source representation (e.g. deep syntax tree)
    ↓  Transfer
Target representation (e.g. deep syntax tree)
    ↓  Synthesis
Target text
```

The critical design choice is **where** transfer happens. Surface-level transfer (word or phrase level) is essentially phrase-based MT. Deeper transfer operates on syntactic or semantic representations that abstract away surface differences between languages.

---

## The Tectogrammatical Layer (t-layer)

TectoMT transfers at the **tectogrammatical (deep-syntactic) layer**, developed in the Prague Dependency Treebank tradition (source: nlp/Deep Syntax.md).

### Properties of the t-layer

**Nodes**: Content words only — function words (prepositions, auxiliaries, conjunctions) are represented as **grammatemes** (abstract morphological/functional attributes), not as tree nodes.

**Edges**: Semantic dependency relations (**functors**): ACT (actor), PAT (patient), ADDR (addressee), LOC (location), etc.

**Attributes on each node**:
- **t-lemma**: Deep lexical form (essentially the lemma)
- **functor**: Semantic role (ACT, PAT, …)
- **grammatemes**: Abstract morphological features:
  - Number (singular/plural)
  - Tense (past/present/future)
  - Modality, aspect, etc.

### Example: Surface vs. Deep

**Surface** (Czech): *Jan dal Marii knihu* (Jan gave Mary a book)

**Surface dependency tree**:
```
      dal (gave)
     /    \       \
Jan(subj) Marii(obj) knihu(obj)
```

**Tectogrammatical tree**:
```
      dát.t (give.t)
     /    \          \
Jan.t   Marie.t    kniha.t
(ACT)   (ADDR)     (PAT)
```

- Function words are removed as nodes
- Grammatemes encode case, tense, etc. as attributes
- t-lemmas are abstract (language-independent forms)

---

## TectoMT Pipeline

**TectoMT** is a rule-based + statistical transfer MT system from Charles University (source: nlp/Deep Syntax.md, Lecture 7).

### Analysis Stage (Czech/English → t-tree)

**Steps**:
1. Tokenization and morphological analysis
2. Surface dependency parsing (a-layer)
3. Surface-to-deep conversion: remove function words, assign functors and grammatemes

**Tools**: Czech: MorphoDiTa + MaltParser + rule-based conversion; English: similar pipeline.

### Transfer Stage (source t-tree → target t-tree)

**Statistical tree-to-tree transfer** (source: nlp/Deep Syntax.md, Lecture 7):

The transfer model translates:
- **t-lemmas**: Lexical transfer (source t-lemma → target t-lemma) via translation dictionary
- **functors**: Often preserved (semantic roles are language-universal to a degree)
- **grammatemes**: Mapped between source and target morphological systems

**Statistical model**:

$$P(\text{target tree} | \text{source tree}) \approx \prod_{n \in \text{nodes}} P(t\text{-lemma}^t | t\text{-lemma}^s) \times P(\text{grammatemes}^t | t\text{-lemma}^s, \text{grammatemes}^s)$$

The model decomposes **per node** — each source node is translated independently, conditioned on its attributes.

### HMTM as the Tree Language Model

**HMTM (Hidden Markov Tree Model)** serves as the **"language model" in TectoMT** — but it operates on **trees** rather than word sequences.

**Role**: Ensures the generated target t-tree is linguistically coherent. Without HMTM, independently translated nodes may combine into unnatural tree structures.

**Mechanism**:
- Models the probability of a target node's attributes given its parent's attributes
- $P(\text{node}_i | \text{parent}(i))$ — Markov property along tree edges
- Equivalent to an $n$-gram LM for sequences, but generalized to trees

**Unit**: HMTM operates on **tree nodes** (t-lemma + functor + grammatemes tuples), not words.

**Analogy to LM**:

| Sequence LM (PBMT) | Tree LM (HMTM, TectoMT) |
|--------------------|-----------------------|
| $P(w_i \mid w_{i-1})$ | $P(\text{node}_i \mid \text{parent}(i))$ |
| Ensures fluent word sequences | Ensures coherent tree structures |
| Words = units | Tree nodes = units |

### Synthesis Stage (target t-tree → text)

**Steps** (reverse of analysis):
1. Add function words (prepositions, auxiliaries) from grammatemes
2. Generate surface word forms from t-lemmas + morphological attributes
3. Order words linearly (surface syntax realization)

**Challenge**: Surface word order is language-specific and may not follow directly from t-tree topology.

---

## Benefits of Deep-Syntactic Transfer

(source: nlp/Deep Syntax.md, exam questions)

### 1. Abstraction Across Morphological Systems

Czech has 7 cases, rich agreement; English has little morphology. At the t-layer:
- Czech *Marii* (dative) and English *to Mary* both become `Marie.t (ADDR)` with grammateme `direction`
- Transfer maps between conceptual roles, not surface word forms

### 2. Long-Range Reordering

Czech and English have radically different word order (Czech: relatively free; English: SVO rigid). The t-tree topology captures predicate-argument structure — word ordering is left to synthesis.

### 3. Handling Non-Projectivity

Czech has many non-projective dependency structures (approx. 25% of sentences contain a non-projective edge; source: nlp/Dependency Trees - MT Talks.md). At the t-layer, non-projectivity is resolved by abstracting to logical relations.

### 4. Shared Semantic Representation

Related languages (e.g., Slavic–Slavic) share t-layer representations, making transfer more direct.

---

## Problems of Transfer-Based MT

(source: nlp/Deep Syntax.md, exam questions)

### 1. Pipeline Error Propagation

Each stage introduces errors. If analysis fails (wrong parse), transfer and synthesis have no recovery mechanism.

```
Error rates compound:
Analysis accuracy: 90%
Transfer accuracy: 90%
Synthesis accuracy: 90%
→ Overall: 0.9³ ≈ 73%
```

### 2. Parser Dependency

Requires high-quality parsers for both source and target language. For low-resource languages, good treebanks may not exist.

### 3. Non-Compositional Expressions

Idioms, multi-word expressions, and idiomatic phrases that don't compose semantically are hard to handle at the t-layer. Example: "kick the bucket" should not be transferred as `kick.t (PAT) bucket.t`.

### 4. Grammar Coverage

Transfer rules must cover all linguistic phenomena. Gaps in grammar coverage cause failed or incomplete translations.

### 5. Complexity

The pipeline requires:
- Morphological analyzers
- Dependency parsers (surface level)
- Surface-to-deep conversion rules
- Tree-to-tree transfer models
- Synthesis grammars

Total system complexity is far higher than PBMT.

### 6. Cascade in Synthesis

Morphological synthesis (generating inflected forms from t-lemmas + grammatemes) requires a complete morphological generator for the target language, which may be unavailable or imperfect.

---

## Comparison: Transfer-Based vs. Phrase-Based MT

| Aspect | Phrase-Based (Moses) | Deep-Syntactic (TectoMT) |
|--------|---------------------|--------------------------|
| Transfer unit | Phrase (surface words) | t-node (deep syntax node) |
| Reordering | Explicit reordering model | Tree structure handles it |
| Morphology | Surface word forms | Grammatemes + synthesis |
| Parser dependency | None | High |
| Long-range dependencies | Limited by phrase length | Handled by tree structure |
| Complexity | Moderate | High |
| Typical BLEU (en–cs) | Moderate | Comparable or lower |

---

## Surface Syntactic Transfer (STSG)

Between phrase-based and deep-syntactic, **Surface Tree Substitution Grammar (STSG)** transfer operates at the surface dependency tree level:
- Tree nodes = surface words (with morphology)
- Benefits: captures syntactic structure
- Problems: non-projectivity causes issues; morphological mismatch still unresolved

STSG is discussed in Lecture 7 as the precursor to deep-syntactic transfer (source: nlp/Deep Syntax.md).

---

## Related Pages

- [[nlp/syntax-based-smt]] — Hierarchical/constituency syntax in SMT
- [[nlp/phrase-based-machine-translation]] — Contrast with flat phrase-based approach
- [[nlp/morphology-machine-translation]] — Deep-syntactic representation handles morphology
- [[nlp/word-alignment]] — Alignment underpins transfer statistics
- [[nlp/course-overview]] — Lecture 7 covers TectoMT
