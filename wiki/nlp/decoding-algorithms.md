# Decoding Algorithms for Machine Translation

**Summary**: Algorithms for finding high-quality translations in statistical and neural MT, covering beam search, stack decoding, pruning strategies, future cost estimation, and alternative search methods.

**Course**: nlp

**Sources**: 05-pbmt-decoding-Koehn-2009.pdf, 05-pbmt-decoding-Haddow-2010.pdf

**Last updated**: 2026-04-17

---

## Overview

Decoding is the core inference problem in machine translation: **find the highest-scoring translation** given a source sentence. This is fundamentally **NP-hard** (source: 02-smt-pbmt-nmt.pdf), so practical systems use approximate algorithms balancing translation quality and computational cost.

---

## The Decoding Problem

### Objective

$$\hat{e} = \arg\max_e P(e|f) = \arg\max_e \prod_i \lambda_i f_i(e, f)$$

where:
- $f$ = source sentence
- $e$ = target sentence (translation)
- $f_i(e, f)$ = feature functions (phrase probability, LM, reordering, etc.)
- $\lambda_i$ = feature weights

### NP-Hardness

**Why NP-Hard?** (source: 02-smt-pbmt-nmt.pdf, 05-pbmt-decoding-Haddow-2010.pdf)

The decoding problem reduces to the traveling salesman problem:
1. Choose phrase pairs covering source (TSP node choice)
2. Reorder phrases in target (TSP ordering)
3. Maximize probability (TSP minimize distance)

With reordering, exponential search space: (# permutations) × (# phrase pair choices) = astronomically large

**Implication**: No polynomial algorithm guaranteed optimal solution

---

## Beam Search (Greedy-Partial-Solution)

### Algorithm

Beam search maintains k best **partial hypotheses** at each step.

**Initialization**:
- Start with empty hypothesis (empty translation, zero words covered)

**Iteration** (source: 05-pbmt-decoding-Koehn-2009.pdf):
1. For each of k active hypotheses:
   - Try all applicable phrase pairs (matching uncovered source words)
   - Create new hypothesis: previous translation + new phrase
   - Compute score: previous score + phrase probabilities + LM cost
2. Collect all candidate hypotheses
3. Rank by score; keep top k
4. Continue until all hypotheses cover entire source

**Termination**:
- Stop when all k hypotheses are complete (100% source coverage)
- Return best hypothesis

### Complexity

$$\text{Time} = O(n \times k \times |\text{phrase options}|)$$

where:
- $n$ = source sentence length
- $k$ = beam width
- $|\text{phrase options}|$ = # phrases matching source spans (typically 1000s)

**Typical**: n=30, k=200, options=1000 → manageable computation

### Quality Trade-off

| Beam Width | Speed | Quality |
|------------|-------|---------|
| 1 (greedy) | ⭐⭐⭐ | ⭐⭐ |
| 5 | ⭐⭐ | ⭐⭐⭐ |
| 100 | ⭐ | ⭐⭐⭐ |
| 1000 | Too slow | Marginal gain |

Typical production: width 3-10

---

## Stack Decoding

### Motivation

Beam search has no structure; difficult to implement efficiently.

**Stack decoding** organizes hypotheses by **coverage** (# source words covered).

### Stack Structure (source: 05-pbmt-decoding-Haddow-2010.pdf)

Create "stacks" where **stack i** contains all hypotheses covering exactly i source words:

```
Stack 0: (empty, score=0)
         No source words covered

Stack 1: (phrase1, score=s1)
         (phrase2, score=s2)
         ... (hypotheses covering 1 word)

Stack 2: (phrase1+phrase2, score=...)
         (phrase3, score=...)
         ... (hypotheses covering 2 words)

...

Stack n: (complete translations)
         All source words covered
```

### Hypothesis Expansion

**From stack i to stack i+k**:
1. Take hypothesis from stack i covering source words [start, end]
2. Find all uncovered phrases starting after end (or before start if reordering allowed)
3. Apply phrase; create new hypothesis
4. Score = previous score + phrase probabilities + reordering penalty + LM cost
5. Add to appropriate stack (i + # new words covered)

### Recombination (source: 05-pbmt-decoding-Haddow-2010.pdf)

**Key Optimization**: Two hypotheses in same stack may be **indistinguishable** for future decoding

**Conditions for Recombination**:
- Same number of source words covered (same stack i)
- Same last n-1 target words (same LM history for trigram model)
- Same last source word covered (same reordering anchor)

**Action**: Keep hypothesis with better score; **discard worse hypothesis**

**Benefit**: Reduces hypotheses per stack dramatically; often 10-100x reduction

### Complexity with Recombination

$$\text{Time} = O(\text{stack\_size} \times \text{sentence\_length}^2)$$

- Quadratic in sentence length (# possible phrase boundaries)
- Linear in stack size (maintained via pruning)

---

## Pruning Strategies

### Histogram Pruning (source: 05-pbmt-decoding-Haddow-2010.pdf)

**Strategy**: Keep at most k hypotheses per stack

```
Stack i hypotheses: [h1(score=100), h2(score=95), h3(score=90), h4(score=50), ...]

After histogram pruning with k=2:
                   [h1(score=100), h2(score=95)]
                   (discard h3, h4, ... regardless of score)
```

**Implementation**: Sort by score; truncate to k

**Advantage**: Simple; practical default

**Disadvantage**: May discard promising hypotheses if many overlap in same stack

### Stack Pruning (source: 05-pbmt-decoding-Haddow-2010.pdf)

**Strategy**: Threshold-based pruning

Keep hypothesis if:
$$\text{score} \geq \alpha \times \text{best\_score\_in\_stack}$$

where $\alpha < 1$ (typical: 0.001)

**Example**:
- Best score in stack: 100
- Threshold (α=0.001): 0.1
- Keep only hypotheses with score ≥ 99.9
- Discard scores < 99.9

**Advantage**: More conservative; keeps promising hypotheses

**Disadvantage**: Less predictable stack sizes; adaptive (varies per stack)

### Combined Pruning

Many systems use **both**:
1. Histogram pruning: Hard limit on stack size (e.g., max 200 hypotheses)
2. Stack pruning: Threshold pruning within that limit (e.g., α=0.001)

---

## Future Cost Estimation

### Motivation

When should we prune a hypothesis early (before complete translation)?

**Idea**: Estimate cost to finish the translation; prune if total cost too high.

### Estimation Strategy (source: 05-pbmt-decoding-Haddow-2010.pdf)

For each uncovered source span:
- Find **cheapest translation option**
- Estimate cost ignoring context

**Example**:
```
Hypothesis covers "the man", cost so far: -50
Uncovered: "saw the dog"

Future cost estimate:
- "saw" → best translation, cost: -10
- "the" → best translation, cost: -2
- "dog" → best translation, cost: -8
- Total future estimate: -20

Total cost: -50 + (-20) = -70
```

### Admissibility

The estimate must **never overestimate** actual cost:
$$\text{estimate} \leq \text{actual\_cost}$$

**Purpose**: Guarantees optimality if estimate is admissible (A* search)

**In practice**: Use pessimistic (conservative) estimate; may discard some good hypotheses

### Language Model Integration

**Challenge**: Language model depends on context (surrounding words)

**Solution**: Estimate without context
- Known: Output words from hypothesis
- Unknown: Words to follow
- Estimate: Average LM cost per word × remaining words

Not perfect but reasonable heuristic.

---

## Alternative Decoding Algorithms

### A* Search (source: 05-pbmt-decoding-Haddow-2010.pdf)

**Algorithm**:
1. Maintain priority queue of hypotheses
2. Priority = score + future cost estimate
3. Pop hypothesis with lowest priority (most promising)
4. Expand (add one phrase)
5. Push new hypotheses to queue
6. Done when top hypothesis is complete

**Advantage**: **Optimal** if future cost estimate is admissible

**Disadvantage**: Queue operations expensive; often slower than beam search in practice

**Use Case**: When optimality matters; offline translation

### Greedy Hill-Climbing (source: 05-pbmt-decoding-Haddow-2010.pdf)

**Algorithm**:
1. Generate one complete translation via depth-first search
2. At each step: greedily pick phrase with highest immediate score
3. Recursively translate remainder
4. Return first complete translation found

**Complexity**: Linear (single path)

**Quality**: Often suboptimal (greedy myopia)

**Use Case**: Fast baseline; real-time systems with strict latency

### Finite-State Transducer (FST) Based Decoding

**Approach**: Represent translation model as weighted FST

**Advantage**: Leverage efficient FST algorithms (standard toolkit: OpenFST)

**Limitation**: Complex reordering models difficult to encode; rarely used

---

## Reordering Limits

### Concept

Restrict how far phrases can be reordered (in source words).

**Maximum Distance** $d$ = farthest source position from previous phrase position

### Effect

**Without Reordering Limit** (d = ∞):
- Exponential phrase order choices
- O(n!) permutations possible

**With Limit** (d = 5-8):
- Linear number of valid phrase orderings
- Only phrases within distance d of previous can be chosen

### Complexity Reduction

**With Reordering Limit**:
$$\text{Time} = O(\text{stack\_size} \times \text{sentence\_length})$$

Linear in sentence length (vs. quadratic without limit)

### Typical Settings (source: 05-pbmt-decoding-Haddow-2010.pdf)

- German-English: d = 6
- Language-Pair Dependent
- **Trade-off**: Larger d improves coverage/quality; increases latency

---

## Practical Decoder: Moses

Standard open-source PBMT decoder implementing:
- Stack-based organization
- Beam/histogram/stack pruning
- Reordering limits (default 6)
- CYK for hierarchical models [[nlp/syntax-based-smt]]
- Cube pruning for efficiency

**Typical Configuration**:
```
Stack size: 200-500
Histogram pruning: k = 200
Stack pruning: α = 0.001
Reordering limit: 6
Beam width: ~200 effective
```

---

## Summary of Algorithms

| Algorithm | Speed | Quality | Complexity | Use Case |
|-----------|-------|---------|-----------|----------|
| **Greedy** | ⭐⭐⭐ | ⭐⭐ | O(n) | Real-time |
| **Beam Search** | ⭐⭐ | ⭐⭐⭐ | O(n·k·opts) | Production |
| **Stack Decoding** | ⭐⭐ | ⭐⭐⭐ | O(n·k) w/ pruning | Standard |
| **A* Search** | ⭐ | ⭐⭐⭐ | O(n log(queue)) | Optimal |
| **Hill-Climbing** | ⭐⭐⭐ | ⭐ | O(n) | Baseline |

---

## Related Pages

- [[nlp/phrase-based-machine-translation]] (PBMT uses beam/stack decoding)
- [[nlp/neural-machine-translation]] (beam search in NMT)
- [[nlp/syntax-based-smt]] (CYK decoding for hierarchical models)
- [[nlp/course-overview]]
