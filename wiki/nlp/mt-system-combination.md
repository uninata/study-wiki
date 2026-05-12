# MT System Combination

**Summary**: System combination merges outputs from multiple MT systems to produce a translation better than any single system. Approaches include confusion network decoding, hypothesis selection, and MEMT (multi-engine MT post-editing).

**Course**: nlp

**Sources**: nlp/Deep Syntax.md (exam questions section)

**Last updated**: 2026-05-03

---

## Overview

Different MT systems have complementary strengths: phrase-based MT excels at fluency for short-range phrases; syntax-based MT handles reordering; neural MT generalizes better. System combination exploits these complementarities to produce translations better than any single system.

**Key insight**: Combining diverse systems — even individually weaker ones — often beats the single best system by reducing systematic errors.

---

## Types of System Combination

### 1. Hypothesis Selection

Select the best complete translation from a pool of candidates.

**Approach**:
1. Collect one translation hypothesis per system
2. Score each hypothesis using a quality estimator or agreement metric
3. Return the highest-scoring hypothesis

**BLEU-based reranking**: Compute pairwise BLEU between hypotheses; select the hypothesis most similar to all others (system combination via consensus).

**Advantage**: Simple to implement.
**Disadvantage**: Output is always one of the input hypotheses — no novel combinations.

### 2. Confusion Network Decoding

The most widely used system combination method (source: nlp/Deep Syntax.md).

**Algorithm**:
1. Designate one system's output as the **backbone** translation
2. Align all other systems' translations to the backbone (word-level alignment)
3. Build a **confusion network**: at each position, all possible words (from all systems) compete
4. Decode the confusion network using a language model to find the best word sequence

```
System A:  "The cat sat on the mat ."
System B:  "The cat was sitting on the mat ."
System C:  "A cat sat on the mat ."

Backbone (A): [The] [cat] [sat] [on] [the] [mat] [.]

Confusion network positions:
[The/A] [cat/A]  [sat/A,was.sitting/B] [on/A] [the/A,a/C] [mat/A] [./A]

Best path via LM: "The cat sat on the mat ." (or novel combination)
```

**Key component**: Language model scores word sequences across the confusion network path.

**Advantage**: Can produce word-level combinations not seen in any individual system.
**Disadvantage**: Alignment errors corrupt the confusion network; relies on backbone quality.

### 3. Multi-Engine MT Post-Editing (MEMT)

Use one MT system's output as a draft, then use another system's knowledge to post-edit.

**Approach**:
1. Primary system (e.g., PBMT) produces initial translation
2. Secondary system (e.g., neural MT) identifies likely errors
3. Blend: replace low-confidence phrases from primary with secondary alternatives

**Advantage**: Leverages the strengths of neural systems for fluency while preserving PBMT's phrase-level accuracy.

### 4. Combining External MT with PBMT

(source: nlp/Deep Syntax.md, exam question: "Describe one possible approach of combining an external MT system with a phrase-based MT system.")

**Approach**:
1. Obtain translations from an external MT system (e.g., Google Translate, TectoMT)
2. Extract phrase pairs from the external system's output aligned back to the source
3. Add these phrase pairs to the PBMT phrase table with a feature indicating source
4. Use MERT to weight the external system's phrases appropriately

**Pipeline**:
```
Source sentence
   ↓ External MT system
External translation
   ↓ Align external translation to source
New phrase pairs
   ↓ Merge with existing phrase table
Extended phrase table
   ↓ MERT tuning
Optimized PBMT system
```

**Benefits**:
- Fills gaps in the native phrase table (phrases unseen in training data)
- External systems may use different or larger parallel corpora
- Can exploit specialized domain MT systems (e.g., medical, legal)
- Low engineering cost — no system internals needed from external MT

**Limitations**:
- External translations may have systematic biases that propagate
- Phrase extraction from external MT is noisy (alignment errors)
- Intellectual property / API cost issues with commercial systems

---

## Quality Estimation for System Selection

When selecting between systems, **Quality Estimation (QE)** models predict translation quality without reference translations:

**Features used**:
- Source sentence features: length, complexity, OOV rate
- Target sentence features: fluency score, length ratio
- Cross-system features: agreement between systems

**Training**: Train on human-labeled quality scores (e.g., HTER — Human Translation Error Rate).

---

## When System Combination Helps

**Most effective when**:
- Systems are diverse (different training data, approaches, languages strengths)
- Each system makes different types of errors
- Language model is strong enough to find good paths through confusion networks

**Less effective when**:
- All systems are similar (e.g., multiple PBMT systems with same data)
- One system dominates all others — combination adds noise
- Language pairs where confusion network alignment is unreliable

---

## Typical BLEU Gains

System combination typically adds $0.5$–$2.0$ BLEU over the best single system on WMT shared tasks, depending on diversity of combined systems.

---

## Related Pages

- [[nlp/phrase-based-machine-translation]] — PBMT as one system in combination
- [[nlp/neural-machine-translation]] — NMT as one system in combination
- [[nlp/transfer-based-mt]] — TectoMT can serve as an external system
- [[nlp/machine-translation-evaluation]] — BLEU used to measure combination gains
- [[nlp/decoding-algorithms]] — Confusion network decoding is a form of decoding
- [[nlp/course-overview]]
