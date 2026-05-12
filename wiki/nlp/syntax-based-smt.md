# Syntax-Based Statistical Machine Translation

**Summary**: Hierarchical phrase-based models incorporating syntactic structure through non-terminals, recursive phrase composition, and CYK-based decoding.

**Course**: nlp

**Sources**: 07-syntax-in-smt.pdf, nlp/Constituency Trees - MT Talks.md, nlp/Dependency Trees - MT Talks.md

**Last updated**: 2026-05-03

---

## Overview

Syntax-Based SMT extends phrase-based MT by incorporating hierarchical structure through non-terminals. Rather than flat phrase sequences, translations are recursive tree structures respecting source syntax.

---

## Motivation

### Limitations of Flat Phrase-Based MT

**Problem**: Long-range reordering difficult to model
```
English (SVO):  The man saw the dog
                 ↓
German (SOV):   Der Mann den Hund sah
                (The man the dog saw)
```

**Issue**: Flat phrase model either
- Translates out-of-order (violates SVO→SOV pattern)
- Creates many overlapping phrases to capture reordering
- Data sparsity: rare reordering patterns unseen

### Solution: Hierarchical Structure

Use non-terminals to encode recursive phrase structure:
```
[S] → [NP] [VP]
[VP] → [NP] [V]
[NP] → DET N

German rule (SOV):
[S] → [NP] [V] [NP]  (different order)
```

Benefits:
- Compact representation of reordering patterns
- Scales to longer-range dependencies
- Compositional semantics

---

## Hierarchical Phrase-Based Models (HPBM)

### Basic Idea

Extend PBMT phrase pairs by adding **non-terminals** (placeholders for recursive phrases).

### Phrase Pair Structure

**Traditional PBMT** (flat):
```
(the man, l'homme)
(saw, a vu)
(the dog, le chien)
```

**Hierarchical PBMT** (with non-terminals):
```
(X → the X dog | le X chien)     [reordering captured!]
(X → man | homme)
(X → saw | a vu)
```

### Non-Terminal Alignment Constraints

**Key Constraint** (source: 07-syntax-in-smt.pdf):

Non-terminals in source and target phrase pairs must align **pairwise**:
```
Valid:
X₁ → [X₁ of the X₂] | [X₂ de X₁]
     (X₁ aligns to X₁, X₂ aligns to X₂)

Invalid:
X₁ → [X₁ of the X₂] | [X₁ X₂ de]
     (non-terminals cross in alignment)
```

**Effect**: Forces syntactically coherent translations; prevents scrambling.

### Phrase Extraction with Non-Terminals

**Process** (source: 07-syntax-in-smt.pdf):

1. Start with word-aligned parallel sentence
2. Extract all consistent phrase pairs (as in flat PBMT)
3. For each phrase pair, identify "gaps" (words with external alignments)
4. Replace gaps with non-terminals
5. Recursively extract sub-phrase pairs

**Example**:
```
Source: [the | man | saw | the | dog]
        [1  | 2   | 3   | 4   | 5]

Alignment: 1↔a, 2↔b, 3↔c, 4↔d, 5↔e

Phrase pair (1-5): "the man saw the dog" → "l'homme a vu le chien"
- Words 2,3,5 internal; words 1,4 external → mark as X
- Extracted rule: X → [the X saw the X | X a vu X]
```

### Scoring Hierarchical Rules

Same as flat PBMT:
- **Relative frequency**: P(target | source)
- **Lexical weighting**: Smooth via word-level probabilities
- **Phrase penalty**: Constant

---

## Decoding with CYK Algorithm

### The Decoding Problem

**Goal**: Find translation maximizing probability:
$$\hat{e} = \arg\max_e P(e) \times P(f | e) \times \text{ReorderingModel}$$

**Search Space**: All possible ways to
1. Recursively decompose source into phrases
2. Apply translation rules
3. Reorder target phrases

**Complexity**: Exponential if exhaustive

### CYK-Based Chart Parsing

**CYK Algorithm** (Cocke-Younger-Kasami): Dynamic programming for context-free grammars.

**Application to HPBM** (source: 07-syntax-in-smt.pdf):

**Bottom-Up Parsing**:
1. Build chart indexed by span [i,j]
2. For span length 1: Apply unary rules (single-word phrases)
3. For larger spans: Combine sub-spans via binary rules
4. Fill chart diagonal-by-diagonal (increasing span size)

**Chart Entry**: Best translation for each span
```
chart[i][j] = best translation of source words i..j
            = max over all applicable rules:
              score(rule) + chart[i₁][j₁] + chart[i₂][j₂]
```

**Time Complexity**:
$$O(n^3 \times |G| \times k)$$

where:
- $n$ = sentence length
- $|G|$ = grammar size (number of rules)
- $k$ = average number of ways to cover span (complexity factor)

---

## Complexity Reduction: Cube Pruning

### Problem

$O(n^3)$ spans × large grammar size × alternative decompositions = **exponential blowup**

### Solution: Cube Pruning

**Idea**: Limit expansion of partial hypotheses

**Algorithm** (source: 07-syntax-in-smt.pdf):
1. For each span [i,j], maintain k-best translations (not all)
2. When combining sub-spans: expand hypotheses lazily
3. Keep only top k combinations (by combined score)
4. Prune rest

**Result**: Reduced complexity at cost of optimality

**Trade-off**:
- Small k: Fast but suboptimal translations
- Large k: Better quality but slower
- Typical: k = 100-1000

---

## Constituency Trees in MT

(source: nlp/Constituency Trees - MT Talks.md)

### Context-Free Grammars

A **context-free grammar (CFG)** generates sentences via production rules of the form:

$$V \to w$$

where $V$ is a non-terminal (phrase category: NP, VP, S, …) and $w$ is a string of terminals (words) and non-terminals.

**Example grammar**:
```
S -> NP VP
NP -> Det Adj N
NP -> dogs
VP -> sleep
Det -> the
Adj -> black
N -> cat
```

**Limitation for MT**: CFGs cannot fully describe natural languages (especially agreement and non-local dependencies), but serve as a useful approximation.

### Tree-to-String, String-to-Tree, Tree-to-Tree

When constituency parse trees are used in MT:

- **Tree-to-String** (source side): Parse source sentence; rules map source subtrees to target strings
- **String-to-Tree** (target side): Rules generate target parse trees from source strings
- **Tree-to-Tree** (both sides): Synchronous CFG; source and target parse trees are built simultaneously

### Synchronous CFG

In tree-to-tree MT, rules have **two right-hand sides**:

```
S/S -> NP_1 VP_2 / NP_1 VP_2        (English SVO to English SVO)
S/S -> NP_1 VP_2 / NP_1 X_2 V      (English SVO to German SOV)
```

Subscripts indicate how non-terminals align between source and target sides. Parsing the source sentence automatically constructs the target parse tree.

**Advantage**: Captures complex reordering (SVO ↔ SOV) in a single rule.

### Why Real Parse Trees Make MT Harder

- **Coverage**: If the parser uses wrong category labels or rare rules, phrase extraction fails
- **Sparseness**: Syntactic rules are more specific than flat phrases → fewer training instances
- **Parser errors**: Errors in the source parse propagate to all rule applications

---

## Dependency Trees in MT

(source: nlp/Dependency Trees - MT Talks.md)

### Dependency Structure

A **dependency tree** is a rooted directed tree where:
- **Nodes** = words in the sentence
- **Edges** = directed dependency relations (head → dependent)

In most linguistic theories, verbs control their arguments. Example (Prague-style parse of *Jim gave his brother a new toy yesterday*):

```
gave (root)
├── Jim (subject)
├── brother (indirect object)
│   └── his (modifier)
├── toy (direct object)
│   ├── a (determiner)
│   └── new (modifier)
└── yesterday (adverbial)
```

Contrast with constituency trees: dependency nodes are **all words** (no non-terminal phrase nodes).

### Non-Projectivity

A dependency tree is **non-projective** if a dependency arc "crosses" another arc when words are projected to a line.

**Significance for Czech**: Approximately $25\%$ of Czech sentences contain a non-projective edge (source: nlp/Dependency Trees - MT Talks.md). For English, the figure is about $8\%$.

**Relevance to MT**: Non-projective structures cause problems for:
- Phrase extraction (contiguous phrases cannot capture the full non-projective sub-tree)
- CYK-based chart parsing (assumes projective structure)
- Linear reordering models (crossing arcs require discontinuous reordering)

**Mitigation**: Mildly context-sensitive grammars (e.g., **Tree-Adjoining Grammar, TAG**) can handle non-projective structures. Most non-projective sentences have at most one "gap", making TAG practical.

---

## Syntax Integration Levels

### Trade-offs

| Syntax Type | Accuracy | Coverage | Data Need | Non-projectivity |
|-------------|----------|----------|-----------|-----------------|
| Constituency | Good | Good | Moderate | Not handled |
| Dependency (surface) | Good | Good | Moderate | Problematic |
| Deep/Tectogrammatical | Very Good | Poor | High | Resolved |

See [[nlp/transfer-based-mt]] for deep-syntactic (tectogrammatical) transfer.

---

## Strengths and Limitations

### Strengths

1. **Long-Range Reordering**: Non-terminals capture reordering patterns
2. **Compositional**: Recursive structure naturally handles composition
3. **Interpretable**: Phrase tree alignments reveal translation decisions
4. **Data Efficiency**: Hierarchical rules more general than flat phrases

### Limitations

1. **Parsing Errors**: Syntax parser errors propagate to decoding
   - If parser wrong, rule application fails
   - Cascading errors
2. **Data Sparsity**: Fewer hierarchical rule instances than flat phrases
   - Rare syntactic structures unseen in training
3. **Complexity**: More complex pipeline (syntax parsing + HPBM decoding)
4. **Mixed Results**: Empirical improvements over flat PBMT modest (often 0-1 BLEU)

---

## Empirical Results

**Typical Observations** (source: 07-syntax-in-smt.pdf):

- Constituency-based HPBM: +0 to +1 BLEU over flat PBMT
- Performance gains language-pair dependent
  - German-English (SVO→SVO): modest improvement
  - Chinese-English (SVO→SVO): modest improvement
  - Language pairs with very different word order: potentially larger gains

**Reason for Modest Gains**:
- Flat PBMT already captures many reordering patterns via long phrases
- Hierarchical structure provides marginal additional benefit
- Data sparsity of hierarchical rules offsets benefits

---

## Evolution to Modern Approaches

### Syntax-Based NMT

Transformers [[shared/transformers]] learn syntax implicitly:
- Attention patterns align with parse trees
- No explicit syntax needed
- Often competitive with explicit syntactic models

### Why Implicit > Explicit?

- **Flexibility**: Learn what syntactic structure is useful for translation
- **Robustness**: No parser errors
- **Efficiency**: Simpler models (no separate parsing stage)

---

## Related Pages

- [[nlp/phrase-based-machine-translation]] — flat predecessor; phrase extraction pipeline
- [[nlp/transfer-based-mt]] — deep-syntactic transfer (TectoMT, HMTM)
- [[nlp/decoding-algorithms]] — CYK-based decoding
- [[nlp/neural-machine-translation]] — implicit syntax learning
- [[nlp/transformers-nmt]] — transformer attention patterns
- [[nlp/course-overview]]
