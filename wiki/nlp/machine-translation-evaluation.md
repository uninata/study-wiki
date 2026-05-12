# Machine Translation Evaluation

**Summary**: Automated and manual evaluation metrics for assessing translation quality, including BLEU, METEOR, TER, and human evaluation methodologies.

**Course**: nlp

**Sources**: 01-eval.pdf

**Last updated**: 2026-04-17

---

## Overview

Machine translation evaluation requires balancing automated efficiency with human judgment accuracy. No single metric perfectly correlates with human assessment, so practitioners typically combine multiple metrics.

### Metric Classification

- **Reference-Based**: Compare hypothesis against one or more reference translations (BLEU, METEOR, TER)
- **Reference-Free**: Assess translation without references (rare; challenging)
- **Automated**: Fast, repeatable, language-independent (BLEU, METEOR, TER)
- **Manual**: Ground truth but expensive and time-consuming (human evaluation)

---

## BLEU (Bilingual Evaluation Understudy)

**Definition**: Corpus-level n-gram precision metric with brevity penalty (source: 01-eval.pdf).

### Computation

$$\text{BLEU} = \text{BP} \cdot \exp\left(\sum_{n=1}^{N} w_n \log p_n\right)$$

where:
- $p_n$ = precision of n-grams of length $n$ (typically $N=4$)
- $w_n$ = weight for n-gram order (uniform: $w_n = 1/N$)
- $\text{BP}$ = brevity penalty if output shorter than reference

### Brevity Penalty

$$\text{BP} = \begin{cases} 1 & \text{if } c > r \\ e^{1-r/c} & \text{if } c \leq r \end{cases}$$

- $c$ = total hypothesis length across corpus
- $r$ = reference length
- Prevents favoring overly short translations

### Strengths
- **Fast**: Computes in milliseconds; suitable for development iteration
- **Language-Independent**: Works for any language pair
- **Widely Adopted**: De facto standard; allows comparison across systems
- **Corpus-Level**: Aggregates judgments across all test sentences

### Weaknesses
- **Reference Dependence**: Penalizes correct paraphrases; requires multiple references for robustness
- **Poor Sentence-Level Correlation**: BLEU unreliable on short segments (< 10 words)
- **Insensitive to Grammaticality**: Focuses on n-gram overlap; missing grammar errors
- **Imperfect Correlation**: Doesn't always track human judgment (especially for distant language pairs)

### Practical Range
- Typical system scores: 20-40 BLEU points (language pair dependent)
- Difference of 1-2 points often not significant
- Relative comparison more reliable than absolute scores

---

## METEOR (Metric for Evaluation of Translation with Explicit ORdering)

**Definition**: Sentence-level metric incorporating stemming, synonymy, and word order (source: 01-eval.pdf).

### Components

1. **Exact Matches**: Same word form in hypothesis and reference
2. **Stemming Matches**: Morphological variants (e.g., "walk" ↔ "walking")
3. **Synonym Matches**: WordNet or other synonym resources
4. **Paraphrase Matches**: Acceptable alternative phrasings
5. **Word Order**: Penalty for reordering between reference and hypothesis

### Aggregation

- **Sentence-Level**: Precision, recall, and order penalty computed per sentence
- **Corpus-Level**: Weighted average across all sentences

### Advantages over BLEU
- **Better Human Correlation**: Empirically correlates more strongly with human judgment
- **Morphological Robustness**: Recognizes word variants; benefits morphologically rich languages
- **Semantic Awareness**: Synonymy and paraphrase capture meaning beyond n-grams
- **Interpretability**: Components provide insight into translation errors

### Limitations
- **Resource Dependence**: Requires synonym/paraphrase databases; coverage varies by language
- **Computational Cost**: Slower than BLEU due to stemming and synonym lookups
- **Language Coverage**: Designed for language pairs with available NLP tools

---

## TER (Translation Edit Rate)

**Definition**: Minimum number of edits (insertions, deletions, substitutions, shifts) to transform hypothesis into reference (source: 01-eval.pdf).

### Edit Operations

- **Insertion**: Add word not in hypothesis
- **Deletion**: Remove word from hypothesis
- **Substitution**: Replace word with different word
- **Shift**: Move sequence of words to different position (captures reordering)

### Computation

$$\text{TER} = \frac{\text{# edits}}{\text{# reference words}}$$

- **Output Range**: 0 (perfect) to infinity (very bad)
- **Lower is Better**: Unlike BLEU (where higher is better)

### Shift Operations
- Explicit handling of reordering; reduces edit distance compared to substitution-only
- Example: German SVO → English VSO reordering = 1 shift vs. multiple substitutions

### Strengths
- **Interpretable**: Edit distance directly represents correction effort
- **Reordering Explicit**: Shifts penalize wrong word order separately
- **Meaningful**: Number of edits correlates with human effort to post-edit

### Weaknesses
- **Computational Cost**: Computing minimum edit distance with shifts is NP-hard; approximations used
- **Shift Ambiguity**: Multiple valid shift sequences; score varies with algorithm
- **Reference Dependence**: Single reference may miss valid paraphrases

---

## Human Evaluation

**Definition**: Native or expert speakers assess translation quality via standardized protocols (source: 01-eval.pdf).

### Evaluation Dimensions

#### Adequacy
- **Question**: Does the translation convey the meaning of the source?
- **Scale**: Typically 1-5 (1=not at all, 5=perfectly)
- **Interpretation**: Semantic equivalence; ignores fluency

#### Fluency
- **Question**: Is the translation natural and grammatical in the target language?
- **Scale**: Typically 1-5
- **Interpretation**: Target-language quality; ignores meaning fidelity

#### Ranking
- **Method**: Direct comparison; "which translation is better?"
- **Advantage**: Avoids absolute scale bias; more reliable than individual scores
- **Output**: Ordinal ranking (A > B > C or pairwise comparisons)

### Inter-Annotator Agreement
- **Metric**: Kappa (categorical) or Spearman correlation (ordinal)
- **Typical**: 0.6-0.8 for well-trained annotators
- **Variation**: Depends on task clarity and annotator expertise

### Advantages
- **Ground Truth**: Reflects actual translation quality
- **Fine-Grained**: Captures nuances (style, register, cultural appropriateness)
- **Flexible**: Can assess any aspect of translation quality

### Disadvantages
- **Expensive**: Labor-intensive; typically $100-500 per 1000 words
- **Slow**: Evaluation turnaround measured in days/weeks
- **Inconsistent**: Annotator disagreement requires adjudication
- **Non-Reproducible**: Different annotators produce different scores

### Best Practices
- **Multiple Annotators**: ≥2 annotators per segment for consensus
- **Training**: Detailed guidelines and practice annotation
- **Quality Checks**: Inter-annotator agreement monitoring
- **Statistical Significance**: Report confidence intervals or significance tests

---

## Metric Selection Strategy

### Development Phase
- **Primary**: BLEU (fast, standard)
- **Secondary**: METEOR (human correlation check)
- **Frequency**: Evaluate after each model change

### Final Evaluation
- **Automatic**: BLEU + METEOR + TER for comprehensive picture
- **Human**: Validation on held-out test set (200-500 segments typical)
- **Analysis**: Error analysis to identify systematic failures

### Trade-offs

| Aspect | BLEU | METEOR | TER | Human |
|--------|------|--------|-----|-------|
| Speed | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐ |
| Human Correlation | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Language Independence | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Interpretability | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Cost | ⭐ (seconds) | ⭐ (seconds) | ⭐ (minutes) | ⭐⭐⭐⭐ (hours) |

---

## Related Pages

- [[nlp/phrase-based-machine-translation]] (MERT tuning uses BLEU)
- [[nlp/neural-machine-translation]]
- [[nlp/course-overview]]
