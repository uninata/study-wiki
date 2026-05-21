# Morphology in Machine Translation

**Summary**: Handling morphologically complex languages in MT through lemmatization, subword segmentation (BPE, STE, WordPiece), and linguistic-aware approaches.

**Course**: nlp

**Sources**: 06-morphology.pdf, nlp/Rich Vocabulary - MT Talks.md, 01-eval.pdf

**Last updated**: 2026-05-21

---

## Overview

Morphologically rich languages (German, Czech, Turkish, Finnish) present challenges for translation systems:
- **Large Vocabulary**: Many word forms from single lemma (e.g., "go"→"goes", "going", "went")
- **Data Sparsity**: Rare word forms unseen in training data
- **Inflectional Complexity**: Number, case, gender, tense combinations explode vocabulary

**Solutions**: Lemmatization, subword segmentation, or factored models [[nlp/factored-translation-models]]

---

## Morphological Structure

### Inflectional Morphology

**Definition**: Regular grammatical modifications to word forms

**Characteristics**:
- Regular patterns (most words follow same rules)
- Productive (apply to new words naturally)
- Typically doesn't change part-of-speech

**Examples**:
- English: walk → walks (3rd person), walked (past), walking (gerund)
- German: Haus (house) → Häuser (houses), Hauses (genitive)
- Czech: kniha (book) → knihou (instrumental), knihách (locative plural)

### Derivational Morphology

**Definition**: Word formation creating new words, often changing meaning/POS

**Characteristics**:
- Irregular patterns (exceptions common)
- Less productive (cannot apply to arbitrary words)
- Often changes part-of-speech

**Examples**:
- English: happy → unhappy (adjective), happiness (noun), happily (adverb)
- German: fahren (drive) → Fahrer (driver), Fahrt (journey)

### Agglutinative Languages

**Definition**: Multiple morphemes concatenated linearly; high morpheme-to-word ratio

**Examples**:
- Turkish: "evlerinden" = ev-ler-in-den
  - ev (house) + -ler (plural) + -in (possessive) + -den (from/with)
  - Entire phrase in one word
- Finnish: "talossanikin" = talo-ssa-ni-kin
  - talo (house) + -ssa (inessive) + -ni (my) + -kin (also)

### Polysynthetic Languages

**Definition**: One "word" encodes entire clause; many morphemes per word

**Example**:
- Inuit: "Tusaassusuugunartuq"
  - Gloss: "He speaks English very well"
  - Multiple concepts in single morphological unit

---

## Lemmatization

### Motivation

Reduce surface forms to base form (lemma) for better generalization in translation.

**Example**:
- Input: "walking", "walked", "walks"
- Lemma: "walk"
- Result: Single translation prob P(e|walk) vs. separate probs for each form

### Process

1. **Morphological Analysis**: Identify stem and affixes
   - Input: "walked"
   - Decompose: stem="walk" + suffix="-ed"
2. **Lemmatization**: Output base form
   - Output: "walk"

### Benefits

- **Vocabulary Reduction**: Fewer unique word forms → less sparsity
- **Better Alignment**: Align base forms more reliably
- **Improved PBMT**: Phrase table covers more variations

### Limitations

- **Tool Dependency**: Requires morphological analyzer (not available for all languages)
- **Ambiguity**: Some forms have multiple lemmas (homonyms)
- **Information Loss**: Inflectional information discarded; may be important for target language

---

## Subword Segmentation: Byte-Pair Encoding (BPE)

### Motivation

Automatic, language-independent alternative to linguistic lemmatization.

### Algorithm (source: 06-morphology.pdf)

**Initialization**:
- Start with character-level vocabulary
- Each word split into characters + end-of-word marker: "play" → ["p","l","a","y","</w>"]

**Iteration**:
1. Count all adjacent pairs of symbols in corpus
2. Find most frequent pair
3. Merge all occurrences of that pair into single symbol
4. Add new symbol to vocabulary
5. Repeat until desired vocabulary size (typically 30K-60K)

**Example**:
```
Iteration 1: Merge most frequent pair (e.g., "e" + "s") → "es"
Iteration 2: Merge next most frequent pair (e.g., "p" + "lay") → "play"
Iteration 3: Continue...

Result vocabulary: 
- Characters: a, b, c, ..., z
- Digrams: "es", "ed", "ing", ...
- Longer: "play", "ing", ...
```

**Inference**:
- Apply merges in order learned during training
- Greedy segmentation: split word then apply merges greedily

### Effectiveness

**German→Czech Experiment** (source: 06-morphology.pdf):

| Configuration | BLEU Score |
|---------------|-----------|
| STE (word level) | 18.58 ± 0.06 |
| Linguistic Morphology | 18.58 ± 0.06 |
| BPE after every token | 13.88 ± 0.18 |
| BPE after non-final tokens | 18.24 ± 0.08 |

**Key Finding**: Simple "underscore trick" (append "_" to prevent cross-word merging) recovers 90% of linguistic method's quality!

### The Underscore Trick (source: 06-morphology.pdf)

**Problem**: BPE merges across word boundaries unnecessarily
```
"the big" → merge "e" + "space" → "e " → ...
           (wrong: merges across words)
```

**Solution**: Add end-of-word marker before learning merges
```
"the_big_" → natural segmentation respects word boundaries
           → "the" + "_bi" + "g_"
           → "the", "bi" (no cross-word merges)
```

### Advantages
- **Automatic**: No linguistic knowledge required
- **Language-Agnostic**: Same algorithm for any language
- **Effective**: Handles morphology surprisingly well
- **Fast**: Inference is linear in word length

---

## WordPiece Segmentation

### Variant

Similar to BPE but with different merge strategy and marking.

**Marking Convention**:
- Start tokens: normal form
- Continuation tokens: prefixed with "##"
- Example: "playing" → ["play", "##ing"]

### Usage

- Standard in BERT and other transformer models ([[shared/transformers]])
- Useful for interpretation (explicit morpheme boundaries)

---

## Linguistic Morphology Methods

### Supervised Approaches

**Morfessor**: Unsupervised morphological segmentation
- Learn morpheme boundaries from data
- Better than pure BPE for some languages

**DeriNet**: Derivational morphology for Czech
- Hand-crafted derivational relations
- More interpretable but labor-intensive

### Findings (source: 06-morphology.pdf)

- **Linguistic methods** (Morfessor, DeriNet) competitive with BPE
- **No single winner**: Best approach depends on language and data
- **Trade-off**: Data-driven (BPE) avoids annotation; linguistic methods leverage expert knowledge

### Room for Linguistics

**Key Question**: Can we combine data-driven efficiency with linguistic accuracy?

**Observation** (source: 06-morphology.pdf):
- Simple data-driven approach (BPE with underscore trick) matches complex linguistic methods
- Suggests that for translation, morphological form matters less than content
- Open: Can we do better by explicitly modeling morphological structure?

---

## Morphology in Different Paradigms

### PBMT Approach
- Lemmatize before phrase extraction
- Better phrase alignment for related forms
- Lose inflectional information (regenerate via target-side morphology)

### Factored Model Approach
- Decompose word into lemma + morphological features
- Learn separate models per factor
- Explicitly model morphological dependencies
- See [[nlp/factored-translation-models]]

### NMT Approach
- Subword segmentation (BPE) handles morphology implicitly
- No explicit morphological annotation needed
- Model learns patterns automatically

### Modern (Transformer) Approach
- BPE segmentation standard
- Learned dense representations capture morphological patterns
- Implicit handling through attention mechanisms

---

## Language-Specific Challenges

(source: nlp/Rich Vocabulary - MT Talks.md)

### German (Compounding)

German creates arbitrarily long **compound words** from multiple roots:

> *Rindfleischetikettierungsüberwachungsaufgabenübertragungsgesetz*
> (law for the delegation of monitoring tasks for beef labeling)

**MT impact**:
- Compound words are rare or unseen in training data
- Example: "Donaudampfschifffahrtsgesellschaftskapitänskajütentürschlüssel"
- **Solution** (source side): Compound splitting — decompose before translation
- **Solution** (target side): More complex; NMT handles compounds better than PBMT via BPE

### Finnish (Agglutination)

Agglutinative languages attach many affixes to words. Finnish nouns have **over 2000 possible inflections** per lemma (source: nlp/Rich Vocabulary - MT Talks.md).

**Case system**: 15 grammatical cases (inessive, elative, illative, adessive, ablative, allative, essive, translative, partitive, …)

**MT impact**: Most inflected forms will never appear in training data; vocabulary sparsity is astronomical. Even Zipf's law guarantees that most rare forms are unseen.

**Similar**: Turkish, Hungarian, Estonian.

### Czech (Fusional Inflection)

**Fusional** languages fuse multiple grammatical properties into a single affix. In Czech, one suffix encodes **case + gender + number simultaneously**.

**Example** (Czech adjective inflection):
```
mladý  (young, masculine nominative singular)
mladého (young, masculine genitive singular)
mladému (young, masculine dative singular)
mladou  (young, feminine accusative singular)
```

**Agreement constraints**: Adjectives must agree with their governing noun in case, gender, and number. This creates a combinatorial explosion of valid word forms.

**Ambiguity**: The same suffix can indicate multiple morphological cases in different contexts — fusional systems are inherently more ambiguous than agglutinative ones.

### Impact on MT Pipeline Stages

(source: nlp/Rich Vocabulary - MT Talks.md)

| Pipeline Stage | Impact of Rich Vocabulary |
|---------------|--------------------------|
| **Word Alignment** | Treats inflected forms as unrelated; sparse statistics |
| **Phrase Extraction** | Rare inflections may be missing even with large corpora; Zipf's law effect |
| **Decoding** | Extra word forms create more branching; pruning may eliminate correct forms |
| **Evaluation** | BLEU compares surface forms; small inflection error = same BLEU penalty as wrong word |

**Decoding note**: There is an inherent trade-off — increasing pruning limits to keep correct rare forms adds computational cost. The correct form may be available but pruned out before reaching a complete hypothesis.

### Solutions

| Problem | Source Side | Target Side |
|---------|-------------|-------------|
| Compounding | Compound splitting | Complex synthesis |
| Inflection | Lemmatization / stems | Morphological generator |
| Evaluation | Surface BLEU insufficient | Use lemma/deep-lemma signals, character-level metrics such as chrF, post-edited references, or manual evaluation (source: nlp/01-eval.pdf) |

---

## Related Pages

- [[nlp/factored-translation-models]] (linguistic feature factorization)
- [[nlp/phrase-based-machine-translation]] (lemmatization in PBMT pipeline)
- [[nlp/neural-machine-translation]] (subword segmentation in NMT)
- [[nlp/course-overview]]
