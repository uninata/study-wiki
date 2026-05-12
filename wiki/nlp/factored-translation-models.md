# Factored Translation Models

**Summary**: Decomposing words into linguistic factors (lemma, POS, morphological tags) for improved translation of morphologically rich languages.

**Course**: nlp

**Sources**: 06-factored-Koehn.pdf

**Last updated**: 2026-04-17

---

## Overview

Factored translation models extend PBMT by decomposing each word into multiple linguistic factors rather than treating words as atomic units. This approach bridges data-driven statistical methods and linguistic knowledge.

---

## Motivation

### Problem with Surface Forms

Surface-form-based translation suffers in morphologically rich languages:

**Example: German→English**
- Source: "Häuser" (houses, plural)
- Source: "Hauses" (house's, possessive)
- Both should translate to English "house" (+ grammatical marker for target)
- But surface-form alignment treats them as different words
- Data sparsity: each form unseen separately, but lemma "Haus" appears frequently

### Solution: Factorization

Decompose each word into factors:
- **Lemma**: Base word form (invariant across inflections)
- **Part-of-Speech**: Grammatical category (noun, verb, adjective, ...)
- **Morphological Features**: Gender, number, case, tense, mood, person, ...

**Example**:
```
German: "Häuser"
  Lemma: Haus
  POS: NN (noun)
  Gender: Neuter
  Number: Plural
  Case: Nominative

English: "houses"
  Lemma: house
  POS: NN
  Number: Plural
```

---

## Architecture

### Word Representation

Each word represented as **vector of factors**:

$$w = (f_1, f_2, ..., f_k)$$

where each factor is a linguistic attribute.

**Typical Factors** (source: 06-factored-Koehn.pdf):
- Position 0: Lemma (base word form)
- Position 1: POS tag (part-of-speech)
- Position 2-N: Morphological features (gender, number, case, tense, ...)

**Example**:
```
Input word: "schöne" (German: "beautiful")
Factorization: (schön | ADJ | Fem | Sg)
              (lemma | POS | Gender | Number)

Output word: "beautiful"
Factorization: (beautiful | ADJ | - | -)
              (lemma | POS | Gender | Number)
```

### Parallel Decoding

**Concept**: Decode each factor stream independently, then combine.

**Process**:
1. **Lemma Translation**: P(English lemma | German lemma)
   - "Haus" → "house"
2. **POS Propagation**: P(English POS | German POS, English lemma)
   - NN → NN given "house"
3. **Morphology Generation**: P(English morpho | German morpho, English lemma + POS)
   - Sg→Pl given "house" + NN

**Advantage**: Share statistical strength across morphological variants
- Single lemma-to-lemma probability P(English lemma | German lemma)
- Multiple surface forms map to same lemma
- Reduced sparsity

### Target-Surface Generation

Two strategies:

**Deterministic Rule-Based**:
- Apply morphological generation rules
- "house" + Plural → "houses"
- Requires morphological generator for target language

**Learned Model**:
- Train separate model: (lemma, POS, morpho) → surface form
- More flexible; handles exceptions
- Requires training data with morphological annotations

---

## Combination Function

**Linear Combination** (source: 06-factored-Koehn.pdf):

$$P(\bar{e} | \bar{f}) = \prod_{j} P(\text{factor}_1^j | \text{factor}_1^f) \times P(\text{factor}_2^j | \text{factor}_1^j, \text{factor}_1^f) \times ...$$

**Example Decomposition**:
$$P(\text{English lemma + POS + morph} | \text{German lemma + POS + morph}) =$$
$$P(\text{eng-lem} | \text{ger-lem}) \times P(\text{eng-pos} | \text{eng-lem}, \text{ger-pos}) \times ...$$

**Advantages**:
- Flexible factor combinations
- Can add or remove factors without retraining
- Linguistic knowledge encoded in factor structure

---

## Handling Missing Factors

Some words may lack complete morphological annotation:

**Strategy**: Unknown factor marked with special token (e.g., "<unk>")

**Robustness**:
- If POS tagger fails: use "<unk>" POS
- Model learns to handle unknown factors
- Graceful degradation

---

## Example: German-English PBMT

### Setup
- Source word: German word with lemma, POS, morphological features
- Target word: English word with lemma, POS, morphological features
- Phrase pairs: Aligned at lemma level + factor alignments

### Pipeline

**Preprocessing**:
1. Tokenize parallel corpus
2. POS tag both sides
3. Lemmatize both sides
4. Extract morphological features

**Phrase Extraction** (source: 06-factored-Koehn.pdf):
1. Word-align on lemmas
2. Extract phrase pairs respecting lemma-level alignment
3. Annotate with factors

**Decoding**:
1. Look up source word's factors
2. Find phrase pairs matching source lemma+POS
3. For each candidate target phrase:
   - Lemma-to-lemma probability
   - POS agreement/transformation probability
   - Morphology generation (if applicable)
4. Select best according to overall model

### Results

Expected improvements over surface-form PBMT:
- Better handling of morphological variants
- Reduced phrase table sparsity
- More stable probabilities (shared across forms)

**Language Pairs Tested** (source: 06-factored-Koehn.pdf):
- German-English
- Czech-English

---

## Advantages

1. **Vocabulary Reduction**: Factor languages reduce number of unique translation units
   - Multiple surface forms → single lemma translation
   - Sparsity problem mitigated

2. **Linguistic Structure**: Leverages linguistic knowledge
   - POS constraints meaningful alignments
   - Morphological features necessary for target generation

3. **Generalization**: Better translation for unseen morphological forms
   - New form "walked" translates via lemma "walk" (seen in training)
   - Without factoring, novel form untranslatable

4. **Flexibility**: Easy to add/remove factors without full retraining

---

## Limitations

1. **Tool Dependency**: Require morphological analyzers (POS taggers, lemmatizers, feature extractors)
   - Not available for all languages
   - Quality varies (errors propagate)

2. **Annotation Complexity**: Multi-factor word pairs more complex to work with
   - Larger phrase tables
   - More parameters to tune

3. **Diminishing Returns**: In NMT era, subword segmentation often simpler and equally effective
   - BPE handles morphology implicitly
   - No linguistic annotation needed

4. **Data Requirements**: Need morphological annotations
   - Adds preprocessing burden
   - May require training additional tools

---

## Comparison: Factored vs. Subword vs. Lemmatization

| Approach | Vocabulary | Interpretability | Tools Required | NMT Friendly |
|----------|-----------|-----------------|-----------------|-------------|
| **Surface Forms** | Large | High | None | No |
| **Lemmatization** | Medium | Medium | Lemmatizer | Moderate |
| **Subword (BPE)** | Medium | Low | None | Yes |
| **Factored** | Medium | Very High | Morphological tools | Moderate |

---

## Modern Context

### In PBMT
- Effective for morphologically rich pairs (German-Czech, Turkish-English)
- Adds linguistic sophistication to phrase-based approach
- Rarely used now (PBMT largely obsolete)

### In NMT
- Subword segmentation (BPE) handles morphology implicitly
- No explicit factor annotation needed
- Transformers learn morphological patterns via attention

### Future Direction
- Could potentially improve modern systems (transformers + factors?)
- Trade-off: interpretability vs. simplicity
- Current trend: simpler models (BPE) over complex linguistic models (factored)

---

## Related Pages

- [[nlp/morphology-machine-translation]] (morphological foundations)
- [[nlp/phrase-based-machine-translation]] (PBMT pipeline)
- [[nlp/neural-machine-translation]] (subword alternative)
- [[nlp/course-overview]]
