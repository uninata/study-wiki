# NLP Exam Questions — NPFL087 Statistical Machine Translation

**Summary**: Comprehensive exam question bank for NPFL087 (30% of course grade). 7 equally-weighted questions covering the full range of topics from lectures, with detailed sub-questions and expected knowledge areas.

**Course**: nlp

**Sources**: Statistical Machine Translation _ ÚFAL_exam_questions.pdf

**Last updated**: 2026-05-02

---

## Exam Structure

The written exam consists of **7 questions**, each equally important. Exam questions cover the **full range of topics discussed in lectures**. Students are expected to:
- Provide detailed explanations with examples
- Use formulas and notation when appropriate
- Discuss limitations and trade-offs
- Connect concepts across multiple lectures

---

## Question 1: Training Data for MT

**Topic**: Data preparation and preprocessing

**Relevant Lectures**: [[nlp/course-overview]] (Lecture 1), [[nlp/smt-foundations]]

**Question**: What types of data are critical for training MT systems and what are the stages of their preparation?

**Expected Knowledge**:
- Parallel corpora (bilingual aligned data)
- Monolingual corpora for language models
- Data cleaning and normalization
- Tokenization and sentence alignment
- Data filtering and quality assessment
- Preprocessing pipelines (e.g., lowercasing, punctuation handling)

**Related Topics**: [[nlp/morphology-machine-translation]], [[nlp/factored-translation-models]]

---

## Question 2: Word Alignment

**Topics**: Alignment problem, IBM Models, EM algorithm

**Relevant Lectures**: [[nlp/word-alignment]] (Lecture 4), [[nlp/neural-machine-translation]]

**Sub-questions**:

### 2a: IBM Model 1 Description
Describe IBM Model 1 for word alignment, highlighting the EM structure of the algorithm. You may or may not use formulas.

**Expected Knowledge**:
- IBM Model 1 objective function: $p(\mathbf{e}|\mathbf{f}) = \prod_j \sum_i p(e_j | f_i)$
- Alignment variables $a_j$ mapping target positions to source positions
- EM algorithm: E-step computes expected alignments, M-step updates parameters
- Null alignment handling
- Iterative refinement and convergence

**Resources**: [[shared/word-embeddings]], [[nlp/smt-foundations]]

### 2b: IBM Model 1 Limitations
Suggest limitations of IBM Model 1. Provide examples of sentences and translations where the model is inadequate; suggest a solution for at least one.

**Expected Knowledge**:
- One-to-many alignment not captured (fertility)
- Distortion (word reordering) not modeled
- Null-alignment ambiguity
- Fertility: multiple source words aligned to one target word
- Relative ordering of words ignored
- Example: English "not" and French "ne...pas" (many-to-many)
- Solutions: IBM Models 2, 3, 4 (fertility, distortion parameters)

### 2c: Word Alignment Problems
Illustrate the problems of the word alignment task as such.

**Expected Knowledge**:
- Ambiguity: multiple valid alignments for same sentence pair
- Many-to-many alignments (e.g., idioms, colloquialisms)
- Null alignments (unaligned words)
- Long-range dependencies
- Language-pair-specific challenges (morphology, word order)
- Computational complexity

### 2d: Phrase-Based MT Alignment Issues
Come up with as many problems as you can for automatic word alignment when used in phrase-based MT.

**Expected Knowledge**:
- Cascading errors from alignment to phrase extraction
- Phrase extraction depends on high-quality alignment
- Rare words and phrases misaligned
- Asymmetric alignments create invalid phrases
- Long phrases extracted from noisy alignments
- GIZA++ tool limitations and heuristics (grow-diag-final)

**Resources**: [[nlp/phrase-based-machine-translation]]

---

## Question 3: Phrase-Based MT

**Topics**: Phrase extraction, decoding, scoring, hypothesis expansion

**Relevant Lectures**: [[nlp/phrase-based-machine-translation]] (Lecture 5), [[nlp/decoding-algorithms]]

**Sub-questions**:

### 3a: Full Space of Parallel (Complete) Derivations
Use a graph and/or the notation of deductive logic to illustrate the **full space** of **parallel (complete) derivations** translating "Marii miluje Jan" into English given the following translation dictionary:
- Jan = John
- miluje = loves
- Marii = Mary
- Model: translates each input word exactly once, allows any permutations of words, ignores translation probabilities

**Expected Knowledge**:
- Phrase-based model generates hypotheses by covering input sequentially
- Parallel derivations: multiple ways to cover the same input span
- Complete derivations: cover entire input
- Chart representation showing hypothesis space
- All permutations of output words considered
- Example output: "John loves Mary", "Mary loves John", "loves John Mary", etc.
- Graph structure with nodes for input spans and edges for phrases

### 3b: Phrase Table Snippets and Translation Process
Make up an example sentence and phrase table snippets. Illustrate the process of phrase-based translation. Remember to cover both preparation of translation options and hypothesis expansion.

**Expected Knowledge**:
- Phrase extraction from word-aligned parallel text
- Phrase scoring: relative frequency estimates $p(e|f), p(f|e), \text{lex}(e|f), \text{lex}(f|e)$
- Multiple translation options for each phrase
- Hypothesis generation: cover input left-to-right
- Reordering model: distance-based or phrase-based distortion
- Beam search decoding
- Language model integration (n-gram probabilities)
- Example: Input "Das Haus ist groß" with phrase pairs and translation options

### 3c: Hypothesis Expansion and Future Cost Estimation
Make up an example input sentence, phrase table snippets and the process of hypothesis expansion and pruning to illustrate why is future cost estimation needed in phrase-based MT. Ignore the cost of reordering.

**Expected Knowledge**:
- Hypothesis expansion: incrementally add phrases to cover input
- Incomplete hypotheses: don't cover entire input yet
- Pruning: select top-K hypotheses per input span
- Future cost: estimate cost of remaining uncovered input
- Need for future cost: can't afford to keep all hypotheses
- Beam pruning: compare partial scores + future cost
- Heuristic future cost: minimum phrase cost for remaining words
- Example showing how pruning without future cost loses correct translation

### 3d: Noisy Channel vs Log-Linear Models
What is the relation between noisy channel model and log-linear model for MT? Try to use formulas. Remember to explain your notation.

**Expected Knowledge**:
- Noisy channel: $\arg\max_e P(e) P(f|e)$ (language model × translation model)
- Log-linear: $\arg\max_e \exp(\sum_i \lambda_i h_i(e, f))$ (weighted feature sum)
- Features $h_i$: phrase probabilities, language model, word penalty, reordering cost, etc.
- Parameters $\lambda_i$: learned weights (MERT optimization)
- Equivalence: can encode noisy channel as log-linear with specific features
- Flexibility: log-linear allows arbitrary features beyond probability models
- MERT: direct optimization of log-linear weights on development set

**Resources**: [[nlp/smt-foundations]], [[nlp/machine-translation-evaluation]]

### 3e: Hypothesis Expansion with Local and Non-Local Features
Describe in detail the process of hypothesis expansion in phrase-based MT. Provide examples for local and non-local features for scoring the hypotheses. How can non-local features be turned into local ones?

**Expected Knowledge**:
- Local features: depend only on current phrase (phrase pair, distortion distance)
- Non-local features: depend on hypothesis history (language model, word count)
- Language model: non-local (scores entire translated span)
- Phrase features: local (score individual phrase pair)
- Reordering distance: local
- Handling non-local features: maintain state in hypothesis
- Language model state: last N-1 words for N-gram LM
- Combining features: weighted sum in log-linear model

---

## Question 4: Hierarchical MT, Treelet MT

**Topics**: Hierarchical phrase models, syntactic translation, chart parsing

**Relevant Lectures**: [[nlp/syntax-based-smt]] (Lecture 7), [[nlp/phrase-based-machine-translation]]

**Sub-questions**:

### 4a: Gappy Phrases Extraction
Illustrate the extraction of "gappy phrases" for the hierarchical model from a word-aligned sentence pair (e.g. 4×5 words). List (some of) the extracted phrases in the order of extraction.

**Expected Knowledge**:
- Gappy phrases: phrases with non-contiguous word coverage
- Extraction rules: if phrase pair alignment forms rectangular region (with internal structure), extract as rule
- Rules contain non-terminals: $X \to \langle \text{source}, \text{target} \rangle$
- Example: source span 1-3 containing target span 1-2 and 5-6 creates rule with two $X$ non-terminals
- Order of extraction: by input span size (smallest first)
- Heuristics: max phrase size, max non-terminals per rule, max gaps

### 4b: Chart Parsing in Hierarchical and Syntactic Models
Illustrate chart parsing as used in both hierarchical and (surface-)syntactic translation models. Provide a sample: input sentence, some rules, some rule applications.

**Expected Knowledge**:
- Chart: dynamic programming table indexed by input span
- Cells: store possible derivations spanning that input
- CYK parsing: bottom-up, spans of increasing size
- Rules: binary (two children) and unary (one child)
- Parsing with hierarchical rules: find rule matching span
- Syntactic parsing: integrate constituent structure
- Example: span [0,2] can combine children [0,1] and [1,2] via binary rule
- Output: parse forest or k-best parses

### 4c: Hierarchical vs Surface-Syntactic Models
What is the difference between the hierarchical and (surface-)syntactic translation model? What new complications does syntax bring and how can they be solved?

**Expected Knowledge**:
- Hierarchical: use learned (gappy) phrase rules, no linguistic structure
- Surface-syntactic: parse source sentence, rules constrained to follow syntax tree
- Complications: parse errors, coverage (syntax tree might not match good translations), sparsity
- Linguistic motivation: syntactic structure should aid reordering and long-distance dependencies
- Solutions: forest-based translation (multiple parses), soft constraints, hybrid models
- Deep-syntactic: use tree structure for source, dependency-based or abstract syntactic trees
- Example: PP-attachment ambiguity affects which rules can apply

**Resources**: [[shared/transformers]], [[nlp/transformers-nmt]]

---

## Question 5: Syntax in MT

**Topics**: Non-projectivity, syntactic phenomena, phrase-based vs syntax-based models

**Relevant Lectures**: [[nlp/syntax-based-smt]] (Lecture 7)

**Sub-questions**:

### 5a: Non-Projectivity Example
Make up a sample sentence containing non-projectivity.

**Expected Knowledge**:
- Non-projectivity: crossing dependencies in parse tree
- Example (Dutch): "Een lager dosis is niet nodig" (A lower dose is not necessary)
- Phrase structure: dependencies cross when drawn as arcs
- Challenge: standard phrase-based models assume contiguous phrases, non-projective structures violate this

### 5b: Why Non-Projectivity Matters in MT
Why is non-projectivity important in MT? Provide an example.

**Expected Knowledge**:
- Non-projectivity requires non-contiguous phrases (gappy phrases)
- Phrase-based models struggle with non-projective reordering
- Affects word order changes between languages
- Example: German SOV order vs English SVO (long-distance dependencies)
- Syntactic models can handle non-projectivity via rules with gaps
- Language pairs with large word order differences (e.g., German-English) benefit

### 5c: Phrase-Based vs Deep-Syntactic Models
For (a) phrase-based model (think Moses) and (b) deep-syntactic model (think TectoMT) provide examples of as many problems as you can (e.g., syntactic constructions where you can prove the model will fail, situations with high risk of mismatch between training and test data).

**Expected Knowledge**:
- Phrase-based failures:
  - Long-distance reordering (relative clauses, PP-attachment)
  - Non-projective structures
  - Morphologically complex structures
  - Ambiguous phrase segmentation
- Deep-syntactic failures:
  - Parse errors propagate to translation
  - Abstract structures not always well-defined
  - Coverage issues (test structure not in training)
  - Training-test mismatch (different syntactic representations)
- Examples: garden-path sentences, ambiguous attachment, rare constructions

### 5d: Constituency-Based vs Dependency-Based Syntactic Models
Compare (a) phrase-based model (think Moses) and (b) constituency-based syntactic model (Joshua) and (c) dependency-based syntactic model. Provide sample syntactic constructions for a language pair that includes English where (1) one of them is bound to fail and (2) both of them are bound to fail. Describe what new problems does the syntactic model bring and how to tackle them (hint: coverage and sparseness).

**Expected Knowledge**:
- Phrase-based: no syntactic structure, handles any reordering
- Constituency-based: rules follow phrase structure tree
- Dependency-based: rules follow dependency structure
- Failures:
  - (1) Phrase-based fails: long non-contiguous reordering
  - (1) Syntactic model fails: wrong parse, rare construction
  - (2) Both fail: out-of-domain phenomena, ambiguous structures
- Coverage: training data limited, test has unseen constructions
- Sparseness: syntactic rules very specific, less generalization than phrases
- Solutions: backoff to phrase-based, rule generalization, lattice-based decoding

**Resources**: [[shared/attention-mechanisms]], [[nlp/transformers-nmt]]

---

## Question 6: Factored MT, Language Models in MT

**Topics**: Morphological factors, language model integration, word forms vs POS tags

**Relevant Lectures**: [[nlp/factored-translation-models]] (Lecture 6), [[nlp/morphology-machine-translation]]

**Sub-questions**:

### 6a: Target-Side Morphology with Factors
When factors are used for target-side morphology, what are they meant to solve? Provide a (not very frequent) counterexample when the part added to the setup hurts instead of helping.

**Expected Knowledge**:
- Factors solve: morphological complexity, data sparsity due to inflected forms
- Decompose words: surface form, lemma, POS tag, morphological features
- Generate translation at lemma level, then inflect based on target morphology
- Counterexample: language-pair where morphological decomposition creates ambiguity or loses distinctions
- Example: Czech verb aspect (perfective/imperfective) marked on lemma but needed for translation of specific tense forms

### 6b: Language Models Based on Word Forms vs POS Tags
Compare language models based on word forms and language models based on POS tags (N, V, A, ... or more detailed like Nsg, Npl at your option) by making up cases where the increased generality of the POS LM helps and where it hurts in distinguishing good vs. bad sentences. You may need to say which patterns are frequent in your training data prior to saying how this misleads the model given some test data. Use monolingual or bilingual examples as you wish.

**Expected Knowledge**:
- Word form LM: high specificity, captures exact sequences
- POS tag LM: high generality, captures syntactic patterns
- Word form helps: rare patterns, specific collocations
- Word form hurts: data sparsity for rare words, doesn't generalize
- POS tag helps: generalize patterns, handle OOV words
- POS tag hurts: loses lexical information, wrong lemmas with same POS
- Example: "good dog" vs "bad dog" (word form captures) vs "ADJ NOUN" (generalizes but loses semantics)
- Training bias: frequent patterns in training mislead model for test data

### 6c: Reverse Self-Training
Sketch the idea of the reverse self-training approach. What benefits it brings?

**Expected Knowledge**:
- Self-training: use system's own translations to improve
- Reverse self-training: translate target-side monolingual data back to source
- Create synthetic parallel data: source-generated target, target-back-translated source
- Benefits: leverage monolingual data, domain adaptation, data augmentation
- Can help with morphology, out-of-domain phenomena
- Trade-off: synthetic data quality depends on initial system

**Resources**: [[nlp/neural-machine-translation]], [[shared/transformers]]

---

## Question 7: Search

**Topics**: NP-completeness, decoding complexity, search features

**Relevant Lectures**: [[nlp/decoding-algorithms]] (Lectures 5 and beyond), [[nlp/smt-foundations]]

**Sub-questions**:

### 7a: MT NP-Completeness
Why is MT NP-complete? Try providing a (polynomial) reduction of an NP-complete problem onto a task in MT.

**Expected Knowledge**:
- Traveling Salesman Problem (TSP) or similar NP-complete problem
- Reduction: map TSP to phrase-based MT decoding
- TSP: visit all cities minimizing distance
- MT: cover all source positions minimizing cost (translation + reordering)
- Decision problem: can we achieve cost ≤ C? (NP-complete)
- Reordering combinatorics: $n!$ permutations of source phrases
- Optimal decoding is NP-hard; heuristic search required

**Resources**: [[nlp/phrase-based-machine-translation]]

### 7b: Local vs Non-Local Features in Search
What are "local" vs "non-local" features in search? Provide examples for phrase-based MT and also for an arbitrary syntactic model you come up with. You will probably need to sketch a small sample of the search space of each of the models with partial hypotheses.

**Expected Knowledge**:
- Local features: depend on current span or phrase only
- Non-local features: depend on hypothesis history or entire sentence
- Phrase-based:
  - Local: phrase pair score, reordering cost
  - Non-local: language model (depends on previous words)
- Syntactic:
  - Local: rule features
  - Non-local: language model, parent rule features
- Hypothesis representation: partial translation with state for non-local features
- Example: LM state tracks last N-1 words

### 7c: Language Model Complications in Hierarchical Models
What are the complications of introducing a language model to the hierarchical model (model based on chart parsing)? Illustrate state splitting.

**Expected Knowledge**:
- Language model: non-local feature, needs state tracking
- Hierarchical decoding: bottom-up via chart
- Non-terminals create discontinuities in output
- State: LM context (last N-1 words) for combining with other rules
- Complication: multiple ways to fill non-terminals yield different LM contexts
- State splitting: different hypotheses for same span have different LM states
- Complexity: exponential in number of non-terminals
- Solution: cube pruning, limit number of states per cell
- Example: rule $X \to \langle X_1 \text{ loves } X_2, X_1 \text{ miluje } X_2 \rangle$ creates LM context dependencies between children

---

## Question 8: MT Evaluation

**Topics**: BLEU metric, limitations, manual evaluation

**Relevant Lectures**: [[nlp/machine-translation-evaluation]] (Lecture 1)

**Sub-questions**:

### 8a: BLEU Description
Describe BLEU. Explain its core properties and limitations, sketch the formula and provide its explanation.

**Expected Knowledge**:
- BLEU: geometric mean of n-gram precisions with brevity penalty
- Formula: $\text{BLEU} = \text{BP} \cdot \exp\left(\sum_n w_n \log p_n\right)$
- $\text{BP} = \min(1, e^{1 - r/c})$ (brevity penalty: penalizes short translations)
- $p_n$: modified precision for n-grams (clipped by reference count)
- Weights $w_n$: typically uniform or higher for lower n
- Corpus-level metric: aggregate over sentence scores
- Properties: fast, automatic, language-independent
- Limitations: no semantic understanding, penalizes paraphrases, sensitive to length

### 8b: BLEU Defects
How does BLEU defeat (score low) hypotheses like "The the the the the." and (separately) "The."?

**Expected Knowledge**:
- "The the the the the.": low precision (repeated word, not in reference), but high n-gram overlap if reference contains "the"
- Actually: BLEU would give some credit (modified precision clips at reference count)
- But: missing content words would lower precision at higher n-grams
- "The.": very short, triggers brevity penalty (brief relative to reference length)
- BLEU would give low score for inadequate length even if words match

### 8c: BLEU for Czech
Why does BLEU perform poorly when evaluating Czech? There are at least two reasons. Provide examples.

**Expected Knowledge**:
- Morphological complexity: Czech is morphologically rich (7 cases, gender, number, aspect)
- Example: "město" (city), "měnit" (change) vs "městu" (city-DAT), "měnil" (changed)
- Surface form variation: single lemma has many inflections
- BLEU penalizes synonymous morphological forms (not in reference)
- Reason 1: morphological variation not captured by n-gram match
- Reason 2: flexible word order (Czech allows SOV, SVO) causes reordering that BLEU penalizes
- Solution: use morphology-aware metrics (METEOR), manual evaluation

**Resources**: [[nlp/morphology-machine-translation]]

### 8d: Moses Pipeline Problems
What are the problems of (a) automatic word alignment and (b) phrase extraction as used in the "Moses pipeline" in general or when used in phrase-based translation.

**Expected Knowledge**:
- Word alignment problems (see Question 2)
  - Cascading errors to phrase extraction
  - Asymmetric alignments (grow-diag-final heuristics)
  - Many-to-many misaligned pairs
- Phrase extraction problems:
  - Depends on alignment quality
  - Inconsistent phrase boundaries
  - Rare phrases with unreliable statistics
  - Phrases that violate linguistic structure
- Trade-offs: phrase length vs. generalization
- Filtering heuristics: frequency thresholds, coverage constraints

### 8e: Manual MT Evaluation Techniques
Suggest 3 different **manual** MT evaluation techniques and highlight their respective positive and negative aspects.

**Expected Knowledge**:
1. **Adequacy**: How much meaning is preserved? (1-5 scale)
   - Pro: captures semantic content
   - Con: slow, subjective, coarse-grained
   
2. **Fluency**: Is translation natural? (1-5 scale)
   - Pro: captures language quality
   - Con: independent from adequacy, time-consuming
   
3. **Ranking**: Which of 2+ translations is better?
   - Pro: relative judgment easier than absolute
   - Con: doesn't tell absolute quality, pairwise comparisons scale poorly
   
4. **Error analysis**: Categorize error types (e.g., word sense, reordering)
   - Pro: diagnostic, actionable feedback
   - Con: time-consuming, requires expertise

5. **TER (Translation Edit Rate)**: Count edits needed to fix translation
   - Pro: interpretable, supports partial credit
   - Con: assumes single reference, labor-intensive

**Resources**: [[nlp/machine-translation-evaluation]]

---

## Question 9: Model Optimization

**Topics**: Weight optimization, log-linear models

**Relevant Lectures**: [[nlp/smt-foundations]], [[nlp/phrase-based-machine-translation]]

**Sub-question**:

### 9a: Weight Optimization Loop
Describe the loop of weight optimization for the log-linear model as used in phrase-based MT.

**Expected Knowledge**:
- Objective: maximize BLEU on development set (via tuning)
- Log-linear model: $\text{score}(e, f) = \sum_i \lambda_i h_i(e, f)$
- MERT (Minimum Error Rate Training):
  - Generate k-best translations for each sentence
  - For each weight $\lambda_i$: compute BLEU score interval where it's optimal
  - Find weight vector that maximizes BLEU
  - Iteratively optimize using line search or simplex
- Lattice-based optimization: more efficient than k-best
- Convergence: typically 20-30 iterations
- Risk: overfitting to dev set
- Alternative: PRO (Pairwise Ranking Optimization)

---

## Question 10: Transfer-Based MT

**Topics**: Transfer-based architecture, deep-syntactic models, TectoMT

**Relevant Lectures**: [[nlp/syntax-based-smt]], [[nlp/transformers-nmt]]

**Sub-questions**:

### 10a: Transfer-Based Architecture
Describe what a "transfer-based" MT architecture means. Illustrate the design of the deep-syntactic layer used for Czech-English translation. What are the potential benefits of transferring at this deep-syntactic layer?

**Expected Knowledge**:
- Transfer-based: analysis (source) → abstract representation → generation (target)
- Deep-syntactic layer: predicate-argument structure, functional structure
- Tectogrammatical representation (TectoMT): language-independent annotation
- Benefits:
  - Abstract from surface morphology and word order
  - Closer to semantic relationships
  - More language-independent (easier transfer)
  - Handles complex reordering and morphology
- Layers: morphological → syntactic (constituent) → syntactic (dependency) → deep-syntactic

### 10b: Problems of Transfer-Based MT
What are the problems of transfer-based MT?

**Expected Knowledge**:
- Annotation complexity: deep-syntactic structures hard to define and parse
- Coverage: test data may have structures not in training
- Sparsity: abstract structures less common than surface forms
- Parse errors: errors in analysis layer propagate
- Transfer rules: hard to learn, language-pair specific
- Ambiguity: multiple deep structures for same surface form
- No robustness to errors in earlier steps

### 10c: TectoMT Statistical Model
Describe the statistical model that is used in TectoMT tree-to-tree transfer. What component of the model serves as a "language model"? What unit does this language model operate with?

**Expected Knowledge**:
- TectoMT: statistical tree-to-tree transduction model
- Components:
  - Tree-to-tree alignment (learn correspondences)
  - Transfer rules (extract rules from alignments)
  - Target language model (fluency)
- Language model: operates on target-side deep-syntactic trees
- Unit: tectogrammatical lemmas and morphosyntactic features
- Decoding: find best translation tree given source tree + LM

**Resources**: [[nlp/syntax-based-smt]], [[shared/transformers]]

---

## Question 11: MT System Combination

**Topics**: System combination, multi-engine translation

**Relevant Lectures**: [[nlp/phrase-based-machine-translation]], [[nlp/neural-machine-translation]]

**Sub-question**:

### 11a: System Combination Approach
Describe one possible approach of combining an external MT system with a phrase-based MT system. What benefits can this approach have?

**Expected Knowledge**:
- System combination: combine outputs of multiple MT systems
- Approach 1: Lattice combination
  - Generate n-best translations from phrase-based system
  - Use external system to rescore or rerank
  - Combine word lattices, find consensus translations
- Approach 2: Hybrid decoding
  - Treat external system's output as additional features/options
  - Expand search space to include external system hypotheses
  - Joint optimization of weights
- Benefits:
  - Complementary strengths: each system has different error patterns
  - Robustness: fallback to one system if other fails
  - Better coverage: combined system covers more phenomena
  - Improved fluency/adequacy from multiple perspectives

---

## Question 12: Neural MT

**Topics**: Encoder-decoder architecture, attention mechanism

**Relevant Lectures**: [[nlp/neural-machine-translation]] (Lecture 3), [[nlp/transformers-nmt]]

**Sub-questions**:

### 12a: Encoder-Decoder Architecture
Sketch the structure of an encoder-decoder architecture of neural MT. Remember to describe the components in the picture.

**Expected Knowledge**:
- Encoder: processes source sequence, outputs context vector(s)
  - RNN/LSTM/GRU cells, input embeddings
  - Bidirectional processing (forward + backward)
  - Final hidden state = context vector (bottleneck)
- Decoder: generates target sequence, conditioned on context
  - RNN/LSTM/GRU cells, output embeddings
  - Input to first step: context vector
  - Input to subsequent steps: previously generated word (or ground truth during training)
  - Output layer: softmax over target vocabulary
- Attention: decoder attends to encoder states at each step
- Diagram should show: source tokens, embeddings, encoder cells, context vector, decoder cells, target tokens

**Resources**: [[shared/sequence-to-sequence]], [[shared/attention-mechanisms]]

### 12b: Attention Problem and Solution
What problem does attention in neural MT address? Provide the key idea of the method.

**Expected Knowledge**:
- Problem: context vector bottleneck
  - Single vector must encode all source information
  - Information loss for long sequences
  - Insufficient context for distant source words
- Attention solution:
  - Decoder attends to different source positions at each step
  - Compute attention weights over encoder hidden states
  - Weighted combination of encoder states = dynamic context
  - Mechanism: compatibility function (dot product, bilinear, etc.)
- Key benefit: direct connection from decoder to each encoder position
- Allows long-range dependencies, better gradients, interpretability

**Resources**: [[shared/attention-mechanisms]], [[nlp/transformers-nmt]]

---

## Exam Preparation Tips

### Study by Topic
- **Data & Preprocessing**: Questions 1, 6
- **Alignment & Phrase Extraction**: Questions 2, 3, 8
- **Decoding & Search**: Questions 3, 7, 9
- **Syntactic Methods**: Questions 4, 5, 10
- **Evaluation Metrics**: Question 8
- **Neural MT**: Questions 12
- **System Combination**: Question 11

### Key Concepts Across Questions
1. **Trade-offs**: Generality vs. specificity, coverage vs. precision
2. **Error Analysis**: Understand failure cases and limitations
3. **Language-Pair Specific**: Czech morphology, word order, specific phenomena
4. **Multilayer Pipeline**: Errors cascade through stages
5. **Evaluation**: No perfect metric, manual evaluation complements automatic

### Linking to Lectures
- Each question maps to 1-2 primary lectures
- Secondary lectures provide broader context
- Cross-references via wikilinks help explore related topics

---

## Related pages

- [[nlp/machine-translation-evaluation]] — BLEU, METEOR, TER metrics (Questions 8)
- [[nlp/smt-foundations]] — Foundational concepts (Questions 1-7, 9)
- [[nlp/word-alignment]] — Alignment algorithms (Question 2)
- [[nlp/phrase-based-machine-translation]] — Phrase extraction and decoding (Questions 3, 8)
- [[nlp/syntax-based-smt]] — Syntactic models and hierarchical translation (Questions 4-5, 10)
- [[nlp/morphology-machine-translation]] — Morphological factors (Questions 5, 6, 8)
- [[nlp/neural-machine-translation]] — Encoder-decoder architecture (Question 12)
- [[nlp/transformers-nmt]] — Transformer models (Questions 5, 10, 12)
- [[nlp/factored-translation-models]] — Factored models (Question 6)
- [[nlp/decoding-algorithms]] — Decoding algorithms and search (Questions 3, 7)
- [[shared/attention-mechanisms]] — Attention mechanism (Question 12)
- [[shared/sequence-to-sequence]] — Seq2seq models (Question 12)
- [[nlp/exam-clusters]] — Study guide organizing topics into tiers by importance
- [[nlp/course-overview]] — Course structure and lecture map
