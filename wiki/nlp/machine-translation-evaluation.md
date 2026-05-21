# Machine Translation Evaluation

**Summary**: Evaluation methods for MT, emphasizing manual evaluation design, BLEU, confidence intervals, LLM judges, and why metric choice can change research conclusions.

**Course**: nlp

**Sources**: 01-eval.pdf, nlp/Promoting the knowledge of source syntax in Transformer NMT.pdf

**Last updated**: 2026-05-21

---

## Overview

Machine translation evaluation is part of the research loop: systems are trained, outputs are generated, and evaluation results guide the next model or data change (source: nlp/01-eval.pdf). The course frames MT quality pragmatically: many systems aim for output that is worth reading or worth editing, while publication without editing remains risky in many settings (source: nlp/01-eval.pdf).

The central warning is that metrics drive research. BLEU helped shape the phrase-based MT era, and other metrics or human protocols can push different system choices (source: nlp/01-eval.pdf).

---

## Manual Evaluation

Manual evaluation can be reference-based, source-based, or both. It can also be sentence-level, document-level, or document-aware; the protocol matters because sentence-level scoring can hide document-level phenomena such as consistency and discourse coherence (source: nlp/01-eval.pdf).

### Common Scoring Techniques

- **Adequacy and fluency**: Annotators judge whether the output preserves meaning and whether it reads naturally in the target language (source: nlp/01-eval.pdf).
- **Direct Assessment (DA)**: Annotators use a continuous scale, often asking how well the MT output expresses the meaning of the reference or source (source: nlp/01-eval.pdf).
- **Relative ranking**: Annotators compare outputs from multiple systems instead of assigning absolute scores (source: nlp/01-eval.pdf).
- **Comprehension or task-based tests**: Evaluation asks whether users can perform a task using the translation, such as answering questions from a translated text (source: nlp/01-eval.pdf).
- **Error annotation**: Gray-box protocols such as MQM or error span annotation identify specific error categories and spans instead of producing only a scalar score (source: nlp/01-eval.pdf).

Manual evaluation is expensive, subjective, hard to reproduce, and highly sensitive to experimental design. Still, it is necessary when automatic scores do not reflect the real user objective (source: nlp/01-eval.pdf).

---

## BLEU

BLEU is a reference-based automatic metric built from the geometric mean of modified $n$-gram precisions, usually for $1$-grams through $4$-grams, multiplied by a brevity penalty (source: nlp/01-eval.pdf):

$$
\text{BLEU} = \text{BP} \cdot \exp\left(\sum_{n=1}^{N} w_n \log p_n\right)
$$

where $p_n$ is modified precision for $n$-grams and the weights are often uniform, $w_n = \frac{1}{N}$ (source: nlp/01-eval.pdf).

The brevity penalty discourages outputs that are too short:

$$
\text{BP} =
\begin{cases}
1 & \text{if } c > r \\
e^{1-r/c} & \text{if } c \leq r
\end{cases}
$$

where $c$ is the candidate length and $r$ is the effective reference length over the test set (source: nlp/01-eval.pdf).

### BLEU Caveats

BLEU scores are not reliably comparable across languages, test sets, numbers of references, tokenization schemes, or evaluation implementations. The lecture explicitly recommends fixed implementations such as sacreBLEU when BLEU must be used (source: nlp/01-eval.pdf).

BLEU is also weak at sentence level, sensitive to surface word forms, and can miss valid paraphrases. The lecture shows that many correct output tokens may be unconfirmed by a single reference, leaving space for systems to differ in quality without BLEU seeing it (source: nlp/01-eval.pdf).

---

## Other Automatic Signals

The evaluation lecture discusses several directions for fixing BLEU's surface-form bias: lemma or deep-lemma evaluation, character-sequence metrics such as chrF, gappy sequence metrics such as BEER, and post-edited references such as HTER (source: nlp/01-eval.pdf).

The source-syntax Transformer paper evaluates translation with multiple automatic metrics: BLEU, CharacTER, BEER, and chrF3; it uses paired bootstrap resampling through MT-ComparEval to assess significance (source: nlp/Promoting the knowledge of source syntax in Transformer NMT.pdf). This is a useful example of the course principle that one scalar metric is rarely enough for trustworthy MT conclusions.

Reference-free automatic evaluation is called quality estimation in the lecture; it aims to judge translation quality without a reference translation (source: nlp/01-eval.pdf).

---

## Empirical Confidence Intervals

For deterministic MT systems, the lecture recommends bootstrap resampling to estimate confidence intervals without assuming a parametric distribution (source: nlp/01-eval.pdf):

1. Sample many test sets by drawing sentences with replacement while preserving test-set size.
2. Score each sampled test set.
3. Sort the scores and drop the top and bottom $2.5\%$.
4. Use the remaining range as an empirical $95\%$ confidence interval.

This matters because small score differences may be noise from the chosen test sentences rather than genuine system improvements (source: nlp/01-eval.pdf).

---

## LLMs as Judges

The lecture treats LLM-based evaluation as promising but risky. In the shown error-span annotation experiment, prompt details affected false positives and recall, chunking mattered strongly, and the measured precision/recall against human annotations was poor in the reported setup (source: nlp/01-eval.pdf).

The practical lesson is to be careful with LLM judges: larger or reasoning-capable models may produce richer outputs, but adversarial and nonsensical-answer tests show that models can still assign overly generous scores when the evaluation setup is flawed (source: nlp/01-eval.pdf).

---

## End-to-End vs Component Evaluation

Component scores do not always correlate with whole-system translation quality. The lecture's alignment example shows that a preprocessing/alignment setup with better alignment error rate need not produce better BLEU, so component-level improvements should be checked against the final MT objective (source: nlp/01-eval.pdf).

This is especially relevant for [[nlp/word-alignment]], [[nlp/phrase-based-machine-translation]], and [[nlp/transformers-nmt]]: a change can make an internal module look better while the generated translation gets worse.

---

## Related Pages

- [[nlp/phrase-based-machine-translation]]
- [[nlp/neural-machine-translation]]
- [[nlp/word-alignment]]
- [[nlp/transformers-nmt]]
- [[nlp/course-overview]]
