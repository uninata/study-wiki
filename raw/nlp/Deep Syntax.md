---
title: "Statistical Machine Translation"
source: "https://ufal.mff.cuni.cz/courses/npfl087#lectures"
author:
published:
created: 2026-05-03
description:
tags:
  - "clippings"
---
## NPFL087 — Statistical Machine Translation

The course covers the area of machine translation (MT) in its current breadth, delving deep enough in each approach to let you know how to confuse every existing MT system. We put a balanced emphasis on several imporant types of state-of-the-art systems: phrase-based MT, surface-syntactic MT and (a typically Praguian) deep-syntactic MT. We do not forget common pre-requisities and surrounding fields: extracting translation equivalents from parallel texts (including word alignment techniques), MT evaluation or methods of system combination.

We aim to provide a unifying view of machine translation as statistical search in a large search space, well supported with practical experience during your project work in a team or alone. Finally, we also attempt to give a gist of emerging approaches in MT, such as neural networks.

### About

SIS code: [NPFL087](https://is.cuni.cz/studium/eng/predmety/index.php?do=predmet&kod=NPFL087)  
Semester: summer  
E-credits: 5  
Examination: 2/2 C+Ex  
Instructor: [Ondřej Bojar](https://ufal.mff.cuni.cz/ondrej-bojar)

### Since 2021: Inverted Classroom / Flipped Class

Since 2021, this class very successfully runs in the 'inverted' mode for most lectures. In other words, you are expected:

- to watch the Main Lecture Video for the week beforehand
- to write down any questions, corrections, possible extensions
	- be excessive, your goal is to 'grill the teacher' for as long as possible
		- you may write these to the Shared Doc for the week
- to attend the lecture call
	- if you do not grill your teacher, the teacher will grill you;-)
		- collectively take notes into the Shared Doc
		- should we have to go remote, please hard to **have your camera on** (use virtual background if needed)

Recordings:

- The lectures are pre-recorded.
- The debates will **not** be recorded.
- If you are absent, recoved by:
	- browsing last week's document
		- asking for details at the next week session

### Timespace Coordinates for 2026

- **lecture**: Fridays 10.40-12.10 in room S9
- **lab/project progress**: right before the lecture, i.e. Fri 9.00-10.40 in room SU2
- **STARTING DATE**: Fri Feb 20, 2026

---

### Additional Sources

- [MTtalks](http://mttalks.ufal.ms.mff.cuni.cz/) - a series of short videolectures illustrating the basic concepts, accompanied with a wiki to explain the details.
- [MT-Class.org](http://mt-class.org/) - four other university MT classes.
- Book: [Statistical Machine Translation](http://statmt.org/book/) (Philipp Koehn). In the library or at Amazon ([UK](http://www.amazon.co.uk/gp/product/0521874157?ie=UTF8&tag=statismachint-21&linkCode=as2&camp=1634&creative=6738&creativeASIN=0521874157), [US](http://www.amazon.com/gp/product/0521874157?ie=UTF8&tag=statismachint-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0521874157)).
- Book: [Čeština a strojový překlad](https://ufal.mff.cuni.cz/books/2012-bojar) (Ondřej Bojar). In the library or at [Knihkupectví Karolinum](http://cupress.cuni.cz/ink2_ext/index.jsp?include=podrobnosti&id=224545).
- Book: [The Reality of Multilingual Machine Translation](https://ufal.mff.cuni.cz/books/2021-kocmi) (Tom Kocmi, Dominik Macháček, Ondřej Bojar). Full PDF available [here](http://ufal.mff.cuni.cz/books/preview/2021-kocmi_full.pdf).

### Requirements

Key requirements:

- Work on a project (alone or in a group of two to three).
- Present project results (~30-minute talk).
- Write a report (~4-page scientific paper).

Contributions to the grade:

- 10% homework and activity,
- 30% written exam,
- 50% project report,
- 10% project presentation.

Final Grade: ≥50% good, ≥70% very good, ≥90% excellent.

### License

Unless otherwise stated, teaching materials for this course are available under CC BY-SA 4.0.

Legend:

The dates below indicate **when we talk about it**. Remember to watch the Full Lecture Video much earlier.

If you see an older year than 2026, the entry has not yet been updated.

### 0\. Class Overview and Project Suggestions

Feb 20, 2026 [Intro Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/2526/00-requirements-and-topics.pdf) [Project Topics](https://docs.google.com/document/d/1JuPu3EBy1u9rX_d2leEzAapaM9uwZ9FtL6Uq0dmH_cg)

(We only very briefly skimmed the list of topics on Feb 20.)

- Course overview.
- Grading.
- Project topics.

### 1\. Metrics of MT Quality

Feb 20, 2026 [Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/2526/01-eval.pdf) [An Old Lecture Video](https://slideslive.com/38924201/1-metrics-of-mt-quality) [Shared Doc](https://docs.google.com/document/d/1W78kX3aeTYtQxSqY-ORsD_vh26IGyBzhy3xcOzZucJs)

[MT Talks: Evaluation in General](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=MT_Evaluation_in_General) [MT Talks: Automatic Evaluation (PER and BLEU)](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Automatic_MT_Evaluation)

- The task of machine translation.
- Methods of manual evaluation.
- Methods of automatic evaluation.
	- LLMs as Judges
- Empirical confidence bounds, bootstrapping.
- End-to-end vs. component evaluation.
- Updates after LLM: (not discussed in the class)
	- Gemba paper: [https://aclanthology.org/2023.eamt-1.19/](https://aclanthology.org/2023.eamt-1.19/)
		- Score magnitudes and accuracies: [https://arxiv.org/abs/2401.06760](https://arxiv.org/abs/2401.06760)
		- Optimal Reference Translations: [paper in Czech](https://asjournals.lib.cas.cz/slovoaslovesnost/article/uuid:58270194-d276-4b97-b3c5-d04951bcdbc8), [slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/other/2023-ort-slides.pdf)

### 2\. Overview of Approaches to MT: SMT, PBMT, NMT

Feb 27, 2026 [Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/02-smt-pbmt-nmt.pdf) [Full Lecture Video](https://slideslive.com/38924790/2-approaches-to-mt) [Shared Doc](https://docs.google.com/document/d/1AUuAw_K_MD0q2JPoEUSn6feASJO1_rZAGJm4JLKEbVs)

[MT Talks: Overview of MT (except NMT)](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Intro) [MT Talks: MT that Deceives (Errors in MT)](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=MT_that_Deceives)

(Ondřej Bojar is not present this week. Miroslav Hrabal will present a tutorial on Metacentrum, LLM finetuning and DSPy.) (Watch the pre-recorded lecture and record your questions in the shared doc.)

- Approaches to MT.
- What makes MT *statistical*
	- Probability of a sentence, Bayes' law.
		- Log-linear model.
- Phrase-Based MT.
	- Features used.
		- Training Pipeline.
		- Unjustified independence assumptions.
- Neural MT.
	- Deep learning summary.
		- Representing text.
		- Encoder-decoder architecture overview.

### 3\. Introduction to Neural Machine Translation (NMT)

Mar 6, 2026 [Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/03-nmt-seq2seq-attn.pdf) [Full Lecture Video](https://slideslive.com/38925194/3-basic-nmt) [Shared Doc](https://docs.google.com/document/d/1Ko1t1crwURN7YyiWVX9ZIPgSAtlHMbRmRzyYz880ytA)

(On March 6, we will discuss whatever questions you may have in Shared Docs for 1. Metrics, 2. Approaches, as well as 3. Intro to NMT.)

- Basic NN building blocks for NMT.
- Representing text in NNs.
- Neural LMs.
- Vanilla Sequence-to-Sequence Model (Encoder-Decoder Framework).
- Attention.

### 4\. Alignment

Mar 22, 2024

[Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/04-alignment.pdf) [Koehn's Slides with Formulas](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1819/04-word-alignment-Koehn.pdf) [Full Lecture Video](https://slideslive.com/38925232/4-alignment) [Shared Doc](https://docs.google.com/document/d/1t0GtDM5_l1t4qkZCcio9uX6m7sFlUWT-z5j3cO_2fn8)

[MT Talks: Data Acquisition](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Data_Acquisition) [MT Talks: Sentence Alignment (Gale&Church)](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Sentence_Alignment) [MT Talks: Word Alignment (IBM1)](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Word_Alignment) [Optional: Church&Gale 1993](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1819/04-church-gale-paper.pdf) [Optional: Collins' Notes on IBM1 and IBM2](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1819/04-ibm12-notes-Collins.pdf) [Homework: IBM Model 1](https://ufal.mff.cuni.cz/courses/npfl087#assignments)

- Parallel Data Acquisition.
- Document Alignment.
- Sentence Alignment.
- Word Alignment, IBM1 in Detail.
- Linguistic Adequacy of Word Alignment

### No Lecture before Easter

Mar 29, 2024

- No lecture today, please work on your projects.

### 5\. Phrase-Based Machine Translation

Apr 5, 2024

[Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/05-pbmt.pdf) [Haddow's Slides for Recombination, Pruning, Future Cost](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1819/05-pbmt-decoding-Haddow-2010.pdf) [Koehn's Slides for Future Cost](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1819/05-pbmt-decoding-Koehn-2009.pdf) [Full Lecture Video](https://slideslive.com/38925281/5-phrasebased-mt) [Shared Doc](https://docs.google.com/document/d/1i_KppexIu7YeZ7SyccSTxU4-OxIQAnxCmhdQB1FinXk) [MT Talks: Phrase-Based MT](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Phrase-based_Model)

- PBMT Overview.
	- Phrase Extraction.
		- Reminder: Log-linear model.
- PBMT Model.
	- Features Used.
		- Traditional PBMT "Training Pipeline"
- Translating with PBMT (Decoding)
	- Translation Options and Stack-Based Beam Search
		- Interlude: MT is NP-Hard (in general, not just PBMT).
		- Pruning, Future Cost Estimation.
		- Local and Non-Local Features.
- Minimum Error-Rate Training.

### 6\. Morphology in MT

Apr 12, 2024

[Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/06-morphology.pdf) [Koehn's Slides for Factored Models](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1819/06-factored-Koehn.pdf) [Full Lecture Video](https://slideslive.com/38925379/6-morphology-in-mt) [Shared Doc](https://docs.google.com/document/d/15OY4PqaaMLkymedylU41tVilRRENM0ggCk30pGuoAiU) [MT Talks: Rich Morphology Makes Everything Harder](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Rich_Vocabulary)

- Problems caused by rich morphology.
	- Morphological richness of Czech.
		- Margin for improvement in BLEU.
- Combinatorial explosion of Czech word forms.
- Morphology in PBMT:
	- Factored PBMT.
		- Reverse self-training.
- Morphology in NMT.
	- Subword Units, BPE.

### 7\. Syntax in SMT

Apr 19, 2024

[Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/07-syntax-in-smt.pdf) [Full Lecture Video](https://slideslive.com/38925411/7-syntax-in-preneural-statistical-mt) [Shared Doc](https://docs.google.com/document/d/1yDOXeQIl_4HvcwcGuRwQSTKlOLqbk6I7fU3K30PDq8Y)

[MT Talks: Constituency Trees in MT](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Constituency_Trees) [MT Talks: Dependency Trees in MT](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Dependency_Trees) [MT Talks: Deep Syntax in MT](http://mttalks.ufal.ms.mff.cuni.cz/index.php?title=Deep_Syntax)

- Motivation for grammar in MT.
- Hierarchical Model.
- Proper syntax: Constituency vs. dependency trees.

Constituency Syntax:

- Context Free Grammars.
- MT as parsing.
	- Synchronous CFG, LM integration.
- Why real source/target parse trees make it harder.

Dependency Syntax:

- Surface syntax (STSG), problems.
- Deep syntax (t-layer); TectoMT, HMTM.

### 8\. Transformer; Syntax in NMT

Apr 20, 2023

[Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/08-transformer-and-syntax-in-nmt.pdf) [Full Lecture Video](https://slideslive.com/38926788/8-transformer-and-syntax-in-nmt) [Shared Doc](https://docs.google.com/document/d/1FFEb1u823USRfxgYQJxG9c3FkqQ9AGAcOcTKpoYnScQ) [Transformer Illustrated](http://jalammar.github.io/illustrated-transformer/) [Transformer in Pytorch](http://nlp.seas.harvard.edu/2018/04/03/attention.html) [Transformer at Medium](https://medium.com/@adityathiruvengadam/transformer-architecture-attention-is-all-you-need-aeccd9f50d09) [Replacing Linguists with Dummies](https://ufal.mff.cuni.cz/pbml/113/art-kondratyuk-cardenas-bojar.pdf) [Promoting the Knowledge of Source Syntax in Transformer NMT Is Not Needed](https://www.cys.cic.ipn.mx/ojs/index.php/CyS/article/view/3265)

- Transformer Architecture (Attention is All You Need)
- Syntax in NMT:
	- Source Syntax in Network Structure
		- Source Syntax Attached to Tokens
		- Target Syntax through Interleave or Multi-Task.

### 9\. Does MT Understand? Word and Sentence Representations

Apr 27, 2023

[Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/09-word-and-sent-reprs.pdf) [Lecture Video](https://slideslive.com/38926925/9-does-mt-understand-word-and-sentence-representations) [Shared Doc](https://docs.google.com/document/d/1x330UoyuHtCvzI2e1mh81bpucTtQ7V54SgIFt6URfYE)

- Introducing Semiotics.
- Do Current MT Systems Understand?
- Continuous Representations.
	- What are Good Representations?
		- Continuous Word Representations.
		- Continuous Sentence Representations.
- Aspects of Meaning.
- Evaluating Sentence Representations
	- How Meaningful is Seq2Seq Representation?

### 10\. Multi-Lingual MT

May 4, 2023

[Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/10-multilingual-mt.pdf) [Lecture Video](https://slideslive.com/38926977/10-multilingual-machine-translation) [Shared Doc](https://docs.google.com/document/d/1JDqtju0N6Vw08xfPkXc3irsl-evdEWOMyDlAYGAxa6s)

- Motivation for using more than 2 languages.
- Transfer Learning.
	- Catastrophic Forgetting.
		- Trivial Transfer Learning.
- Multi-Lingual NMT.
- Massively Multi-Lingual NMT.

### 11\. Multi-Modal Translation

May 11, 2023

[Lecture Slides](https://ufal.mff.cuni.cz/~bojar/courses/npfl087/1920/11-multimodal-mt.pdf) [Video](https://slideslive.com/38927092/11-multimodal-translation-speech-and-vision) [Shared Doc](https://docs.google.com/document/d/1HL4BcIbl5SeLogaGdM3DTCoDT-qoAJXYiHiPzYzSUpE)

- Overview of Multi-Modal Translation.
- Spoken Language Translation = ASR + MT.
	- Problems at ASR-MT boundary.
		- End-to-end SLT approaches.
- Visual information for MT.
	- Motivation
		- Are pictures really helpful?

### Project Presentations

May 18, 2023 [Shared Doc with Schedule](https://docs.google.com/document/d/1ygXsvbj5np5vyBHGhr1TcoqEdfuJWV-oSqgAF_0kk5o)

- Be prepared to present your project.
	- Up to 30 minutes per talk.
		- Present work in progress, no need to have final results.

### Written Exam (and remaining presentations)

May 25, 2023 (TENTATIVE)

- Approximately hour-long written exam.
- Seven open questions
	- For a full answer, you usually need to write half a page or a page, including illustrations
		- [Examples of typical questions](#exam_questions).
		- The questions cover everything we discussed in the lectures.

For older versions of the lectures, you can browse the course history in SVN:

- [https://svn.ms.mff.cuni.cz/projects/NPFL087](https://svn.ms.mff.cuni.cz/projects/NPFL087)  
	For read-only access use username: **student** and password: **student**

### Doc-Level Manual Evaluation and/or Manual Post-Editing

- Czech speakers: Evaluate the provided translations:
	- Indicate: Adequacy, Fluency, Overall Quality and Number of Context Errors
- Others: Provide your independent translation, and MT post-editing of a short document

### IBM Model 1 Implementation

Deadline: 20th April 2020

- Implement IBM Model 1 in your favourite language.
- Detailed instructions: [http://ufallab.ms.mff.cuni.cz/~bojar/2021-ibm1-alignment/](http://ufallab.ms.mff.cuni.cz/~bojar/2021-ibm1-alignment/)

The exam is written and consists of 7 questions, each equally important. In general, the exam questions will cover the **full range of topics discussed in the lectures**.

Here are the exam questions used in the past, for illustration:

### Training Data for MT

- What types of data are critical for training MT systems and what are the stages of their preparation.

### Word Alignment

- Describe IBM Model 1 for word alignment, highlighting the EM structure of the algorithm. You may or may not use formulas.
- Suggest limitations of IBM Model 1. Provide examples of sentences and their translations where the model is inadequate, suggest a solution for at least one of them.
- Illustrate the problems of word alignment task as such.
- Come up with as many problems as you can for automatic word alignment when used in phrase-based MT.

### Phrase-Based MT

- Use a graph and/or the notation of deductive logic to illustrate the *full* space of partial (incl. complete) derivations translating "Marii miluje Jan" into English given the following translation dictionary:
	- Jan = John, miluje = loves, Marii = Mary and a model that:
		- translates each input word exactly once
				- allows any permutations of words,
				- ignores translation probabilities.
- Make up an example sentence and phrase table snippets. Illustrate the process of phrase-based translation. Remember to cover both the preparation of translation options as well as the hypothesis expansion.
- Make up an example input sentence, phrase table snippets and the process of hypothesis expansion and pruning to illustrate why is future cost estimation needed in phrase-based MT. Ignore the cost of reordering.
- In the first step of phrase-based translation, all relevant phrase translations are considered for an input sentence. How the phrase translations were obtained? What scores are associated with phrase translations? Roughly suggest how the scores can be estimated.
- What is the relation between noisy channel model and log-linear model for MT? Try to use formulas. Remember to explain your notation.
- Describe in detail the process of hypothesis expansion in phrase-based MT. Provide examples for local and non-local features for scoring the hypotheses. How can non-local features be turned into local ones?

### Hierarchical MT, Treelet MT

- Illustrate the extraction of "gappy phrases" for the hierarchical model from a word-aligned sentence pair (e.g. 4x5 words). List (some of) the extracted phrases in the order of extraction.
- Illustrate chart parsing as used in both hierarchical and (surface-) syntactic translation model. You will need to provide a sample: input sentence, some rules, some rule applications.
- What is the difference between the hierarchical and (surface-) syntactic translation model? What new complications does syntax bring and how they can be solved?

### Syntax in MT

- Make up a sample sentence containing non-projectivity.
- Why is non-projectivity important in MT? Provide an example.
- For (a) phrase-based model (think Moses) and (b) deep-syntactic translation (think TectoMT) provide examples of as many problems as you can (e.g. syntactic constructions where you can prove the model will fail, situations with a high risk of mismatch between training and test data).
- Compare (a) phrase-based model (think Moses) and (b) constituency-based syntactic model (Joshua). Provide sample syntactic constructions for a language pair that includes English where (1) one of them is bound to fail and (2) both of them are bound to fail. Describe what new problems does the syntactic model bring and how to tackle them (hint: coverage and sparseness).

### Factored MT, Language Models in MT

- When factors are used for target-side morphology, what they are meant to solve? Provide a (not very frequent) counterexample when the part added to the setup hurts instead of helping.
- Compare language models based on word forms and language models based on POS tags (`N`, `V`, `A`,... or more detailed like `Nsg`, `Npl` at your option) by making up cases where the increased generality of the POS LM helps and where it hurts in distinguisting good vs. bad sentences. You may need to say which patterns are frequent in your training data prior to saying how this misleads the model given some test data. Use monolingual or bilingual examples as you wish.
- Sketch the idea of the reverse self-training approach. What benefits it brings?

### Search

- Why is MT NP-complete? Try providing a (polynomial) reduction of an NP-complete problem onto a task in MT.
- What are "local" vs. "non-local" features in search? Provide examples for phrase-based MT and also for an arbitrary syntactic model you come up with. You will probably need to sketch a small sample of the search space of each of the models with partial hypotheses.
- What are the complications of introducing a language model to the hierarchical model (model based on chart parsing)? Illustrate state splitting.

### MT Evaluation

- Describe BLEU. Explain its core properties and limitations, sketch the formula and provide its explanation.
- How does BLEU defeat (score low) hypotheses like "The the the the the." and (separately) "The."?
- Why does BLEU perform poorly when evaluating Czech? There are at least two reasons. Provide examples.
- What are the problems of (a) (automatic) word alignment and (b) phrase extraction as used in the "Moses pipeline" in general or when used in phrase-based translation.
- Suggest 3 different *manual* MT evaluation techniques and highlight their respective positive and negative aspects.

### Model Optimization

- Describe the loop of weight optimization for the log-linear model as used in phrase-based MT.

### Transfer-Based MT

- Describe what a "transfer-based" MT architecture means, illustrate the design of the deep-syntactic layer used for Czech-English translation. What are the potential benefits of transferring at this deep-syntactic layer?
- What are the problems of transfer-based MT?
- Describe the statistical model that is used in TectoMT tree-to-tree transfer. What component of the model serves as a "language model"? What unit does this language model operate with?

### MT System Combination

- Describe one possible approach of combining an external MT system with a phrase-based MT system. What benefits can this approach have?

### Neural MT

- Sketch the structure of an encoder-decoder architecture of neural MT, remember to describe the components in the picture.
- What problem does attention in neural MT address? Provide the key idea of the method.

### Archive

All lecture materials for the years 2008—2017 are available in the course SVN:

[https://svn.ms.mff.cuni.cz/projects/NPFL087](https://svn.ms.mff.cuni.cz/projects/NPFL087)  
For read-only access use username: **student** and password: **student**