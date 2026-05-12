# NPFL087: Statistical Machine Translation

**Summary**: Overview of the Statistical Machine Translation course structure, learning objectives, grading, and related prerequisite courses.

**Course**: nlp

**Sources**: 00-requirements-and-topics.pdf

**Last updated**: 2026-05-03

---

## Course Structure

NPFL087 is a comprehensive course covering the evolution from rule-based machine translation through statistical and neural approaches. The course spans 12 lectures + project presentations.

### Course Outline

1. **Metrics of MT Quality** — Evaluation methodologies (see [[nlp/machine-translation-evaluation]])
2. **Approaches to MT** — SMT, PBMT, NMT, and the NP-hardness of decoding (see [[nlp/smt-foundations]])
3. **NMT Fundamentals** — Seq2seq, attention mechanisms, transformers (see [[nlp/neural-machine-translation]])
4. **Parallel Texts & Alignment** — Sentence and word alignment with hunalign, GIZA++ (see [[nlp/word-alignment]])
5. **PBMT Pipeline** — Phrase extraction, decoding, MERT tuning, Moses (see [[nlp/phrase-based-machine-translation]])
6. **Morphology in MT** — Linguistic vs. data-driven approaches; factors and segmentation (see [[nlp/morphology-machine-translation]])
7. **Syntax in SMT** — Constituency, dependency, and deep structures (see [[nlp/syntax-based-smt]])
8. **Modern NMT** — Transformers, syntax in neural models, LLM-based translation (see [[nlp/transformers-nmt]])
9. **Representations** — Word and sentence embeddings for translation (see [[nlp/word-and-sentence-representations]])
10. **Multi-Lingual MT** — Transfer learning and multilingual models (see [[nlp/multilingual-machine-translation]])
11. **Multi-Modal Translation** — Vision and language integration (see [[nlp/multimodal-machine-translation]])
12. **Project Presentations** — Student research projects

## Prerequisites

### Informal Prerequisites
- **NPFL125** Introduction to Language Technologies
- **NPFL070** Language Data Resources

### Recommended
- **NPFL114** Deep Learning (for neural approaches)
- **NPFL140** Large Language Models (for LLM-based translation)

## Grading

| Component | Weight |
|-----------|--------|
| Participation & Homework | 10% |
| Written Exam | 30% |
| Project Report (~4 pages) | 50% |
| Project Presentation (~30 min) | 10% |

**Final Grade Scale**: ≥50% (good), ≥70% (very good), ≥90% (excellent)

### Project Requirements
- Work alone or in groups of 2-3
- Present results (~30-minute talk)
- Write scientific paper-style report (~4 pages)
- Project suggestions: https://tinyurl.com/npfl087-2026

## Key Resources

### Course Materials
- **Slides & Lectures**: https://ufal.mff.cuni.cz/courses/npfl087
- **Video Lectures & SMT Wiki**: http://mttalks.ufal.ms.mff.cuni.cz/

### Textbooks
- Philipp Koehn. *Statistical Machine Translation*. Cambridge University Press, 2009.
  - Slides: http://statmt.org/book/
- Kocmi et al. (2021). *The Reality of Multi-Lingual Machine Translation*. ISBN 978-80-88132-11-0.
  - Full text: https://ufal.mff.cuni.cz/books/2021-kocmi

### External Resources
- **mt-class.org** (UEDIN course, updated to NMT)
- **CMU MT & Seq2Seq Course**: http://phontron.com/class/mtandseq2seq2017/
- **NMT Book**: http://mt-class.org/jhu/assets/nmt-book.pdf; https://arxiv.org/abs/1709.07809
- **Deep Learning Textbook**: http://www.deeplearningbook.org/

## Speech Translation: ELITR Project

The course references the ELITR (Simultaneous Translation for Interpreters) project demonstrating real-world machine translation applications:
- **ELITR Project**: https://elitr.eu/
- **Demo Videos**: 
  - Czech example: http://ufallab.ms.mff.cuni.cz/~bojar/elitr/720p.mp4
  - Open Doors Day demo: https://elitr.eu/a-tireless-interpreter/

---

## Related Pages

- [[nlp/machine-translation-evaluation]]
- [[nlp/smt-foundations]]
- [[nlp/word-alignment]]
- [[nlp/phrase-based-machine-translation]]
- [[nlp/neural-machine-translation]]
- [[nlp/morphology-machine-translation]]
- [[nlp/factored-translation-models]]
- [[nlp/syntax-based-smt]]
- [[nlp/transfer-based-mt]]
- [[nlp/mt-system-combination]]
- [[nlp/transformers-nmt]]
- [[nlp/decoding-algorithms]]
- [[nlp/word-and-sentence-representations]]
- [[nlp/multilingual-machine-translation]]
- [[nlp/multimodal-machine-translation]]
- [[nlp/exam-questions]]
- [[nlp/exam-clusters]]
- [[shared/attention-mechanisms]]
- [[shared/sequence-to-sequence]]
- [[shared/transformers]]
