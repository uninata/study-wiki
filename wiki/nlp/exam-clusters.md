# NLP Exam Clusters

**Summary**: Study guide organizing NPFL087 exam topics into three tiers by importance. Tracks confidence levels and practice needs across all major topics covered in the course.

**Course**: nlp

**Sources**: exam-questions.md, course lectures 1-12

**Last updated**: 2026-05-03

---

## Tier 1 — Must know

| Cluster                                                   | Status | Confidence | Need to practise                                                                                 |
| --------------------------------------------------------- | ------ | ---------- | ------------------------------------------------------------------------------------------------ |
| [[nlp/smt-foundations\|MT data + SMT foundations]]        | ⬜      | 🔴         | [[nlp/course-overview\|data pipeline]], [[nlp/smt-foundations\|noisy channel]], log-linear model |
| [[nlp/word-alignment\|Word alignment + IBM Model 1]]      | ⬜      | 🔴         | [[pattern-recognition/em-algorithm\|EM algorithm]], limitations, worked examples                 |
| [[nlp/phrase-based-machine-translation\|Phrase-based MT]] | ⬜      | 🔴         | phrase table construction, [[nlp/decoding-algorithms\|hypothesis expansion]], pruning            |
| [[nlp/decoding-algorithms\|Search + decoding]]            | ⬜      | 🔴         | NP-hardness, beam search, [[nlp/smt-foundations\|local/non-local features]]                      |
| [[nlp/machine-translation-evaluation\|BLEU + evaluation]] | ⬜      | 🔴         | BLEU formula, brevity penalty, Czech morphology issues                                           |

## Tier 2 — Important

| Cluster | Status | Confidence | Need to practise |
|---|---|---|---|
| [[nlp/syntax-based-smt\|Hierarchical / syntactic MT]] | ⬜ | 🔴 | gappy phrases, chart parsing, syntax problems |
| [[nlp/factored-translation-models\|Factored MT + language models]] | ⬜ | 🔴 | morphological factors, POS LM helps/hurts |
| [[nlp/phrase-based-machine-translation\|Optimization / MERT]] | ⬜ | 🔴 | weight optimization loop, MERT algorithm (see MERT section) |

## Tier 3 — Compact coverage

| Cluster | Status | Confidence | Need to practise |
|---|---|---|---|
| [[nlp/transfer-based-mt\|Transfer-based MT / TectoMT]] | ⬜ | 🔴 | deep syntactic layer, tree-to-tree transfer, HMTM |
| [[nlp/mt-system-combination\|System combination]] | ⬜ | 🔴 | external MT + phrase-based MT integration, confusion networks |
| [[nlp/neural-machine-translation\|Neural MT + attention]] | ⬜ | 🔴 | [[shared/transformers\|encoder-decoder]], [[shared/attention-mechanisms\|attention mechanism]] |

## Study strategy

**For Tier 1 clusters**: Work through all exam questions multiple times. These topics appear across many questions (2–12) and form the foundation.

**For Tier 2 clusters**: Study questions that specifically target these topics. Useful for deepening understanding but not tested as heavily in isolation.

**For Tier 3 clusters**: Brief study sufficient — these are bonus/specialized knowledge. Understand core concepts but don't over-invest time.

## How to use this page

1. **Before studying**: Use "Status" column to mark progress as you work through each cluster (⬜ → 🟦 → ✅)
2. **Track confidence**: Update the emoji (🔴 = low, 🟡 = medium, 🟢 = high) as you study
3. **Focus practice**: Use the "Need to practise" column to guide hands-on work
4. **Cross-reference**: Click wikilinks to jump to detailed lecture notes and concept pages
5. **Test prep**: Re-read Tier 1 clusters the day before the exam

## Related pages

- [[nlp/exam-questions]] (full exam question bank with all 12 questions and sub-questions)
- [[nlp/course-overview]] (lecture organization and syllabus)
- [[nlp/smt-foundations]] (foundational SMT concepts and noisy channel)
- [[nlp/word-alignment]] (IBM Models and EM algorithm)
- [[nlp/phrase-based-machine-translation]] (phrase extraction and MERT)
- [[nlp/neural-machine-translation]] (modern NMT approaches)
- [[shared/transformers]] (transformer architecture for NMT)
- [[shared/attention-mechanisms]] (attention fundamentals)
