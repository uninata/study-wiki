# Wiki Log

Append-only record of all wiki operations.

---

## [2026-04-14] reorganize | initial setup | all

Created wiki structure with per-course subdirectories for 8 courses: ml-fundamentals, ml-methods, computer-vision, stai, nlp, deep-learning, cybersecurity, programming-for-engineers. Added shared/ folder for cross-course concepts. Created index.md, log.md, CLAUDE.md, and page template.

## [2026-04-14] ingest | all lectures + seminars + HW | ml-fundamentals

Ingested 8 lecture PDFs, 7 seminar PDFs, and 1 homework PDF. Created 10 wiki pages:
- `ml-fundamentals/course-overview.md` — course structure and exam themes
- `ml-fundamentals/empirical-risk-minimization.md` — ERM, error decomposition, generalization bounds
- `ml-fundamentals/generative-learning.md` — MLE, Fisher information, plug-in Bayes
- `ml-fundamentals/pac-learning.md` — PAC framework, sample complexity
- `ml-fundamentals/vc-dimension.md` — shattering, Fundamental Theorem, SRM
- `ml-fundamentals/svm.md` — hard/soft margin, dual, kernel SVM
- `ml-fundamentals/linear-classifiers.md` — perceptron, logistic regression, multiclass
- `ml-fundamentals/prediction-evaluation.md` — Hoeffding confidence intervals
- `shared/bayes-classifier.md` — Bayes predictor for different losses, approximation error
- `shared/kernel-functions.md` — kernel trick, polynomial/RBF kernels, kernelized regression
Updated index.md with all new pages.

## [2026-04-15] ingest | all lectures | pattern-recognition

Ingested 13 lecture PDFs (pr_01 through pr_13, knn_lecture, em_2020, neural_networks_2020). Created 13 wiki pages:
- `pattern-recognition/course-overview.md` — course structure and exam themes
- `pattern-recognition/bayesian-decision-theory.md` — Bayes rule, ROC, Neyman-Pearson, minimax
- `pattern-recognition/non-parametric-methods.md` — k-NN, Cover-Hart, Parzen windows, KDE
- `pattern-recognition/parameter-estimation.md` — MLE, MAP, Bayesian, conjugate priors
- `pattern-recognition/logistic-regression.md` — sigmoid, softmax, IRLS, Bayesian LR
- `pattern-recognition/perceptron.md` — convergence, kernel perceptron, Kozinec
- `pattern-recognition/svm.md` — hard/soft margin, kernel SVM, structured SVM, SVR
- `pattern-recognition/adaboost.md` — boosting, margin theory, Real/LogitBoost
- `pattern-recognition/clustering.md` — K-means, K-means++, K-medians/medoids, Weiszfeld
- `pattern-recognition/pca.md` — eigendecomposition, kernel PCA, probabilistic PCA, ICA
- `pattern-recognition/decision-trees.md` — CART, Gini/entropy, random forests, feature importance
- `pattern-recognition/em-algorithm.md` — GMMs, EM convergence, variational interpretation
- `pattern-recognition/neural-networks.md` — MLP, backprop, CNN/RNN, applications
Updated shared/bayes-classifier.md and shared/kernel-functions.md with PR sources and cross-links.
Updated index.md with all 13 new pages.

## [2026-04-15] ingest | all lectures + labs | computer-vision

Ingested 16 PDFs (local features, RANSAC, deep learning parts 1+2, learned matching, 3D reconstruction parts 1+2, KCF, KLT, deep metric learning, SSL, image retrieval, TLD, tracking by segmentation, 2 lab docs). Created 14 wiki pages:
- `computer-vision/course-overview.md` — course structure and key themes
- `computer-vision/local-features.md` — Harris, scale-space, SIFT, BRIEF, ORB, MSER
- `computer-vision/ransac.md` — robust estimation, PROSAC, LO-RANSAC, USAC
- `computer-vision/epipolar-geometry.md` — fundamental/essential matrix, stereo, correspondence
- `computer-vision/camera-models-calibration.md` — pinhole, P=K[R|t], DLT, Zhang's method
- `computer-vision/3d-reconstruction.md` — stereo, SfM, bundle adjustment, DUSt3R, MASt3R, Depth Anything
- `computer-vision/cnn-architectures.md` — LeNet through ResNet, transfer learning
- `computer-vision/object-detection-segmentation.md` — R-CNN, YOLO, SSD, FCN, U-Net, Mask R-CNN
- `computer-vision/generative-models.md` — GANs, VAEs (reparameterization, ELBO)
- `computer-vision/learned-matching.md` — SuperPoint, SuperGlue, LoFTR
- `computer-vision/visual-tracking.md` — KLT, KCF, TLD, tracking by segmentation
- `computer-vision/deep-metric-learning.md` — contrastive/triplet loss, ArcFace, face recognition
- `computer-vision/self-supervised-learning.md` — SimCLR, MoCo, BYOL, DINO, MAE
- `computer-vision/image-retrieval.md` — BoVW, VLAD, Fisher vectors, deep retrieval
Created 2 new shared pages:
- `shared/svm.md` — consolidated from ml-fundamentals and pattern-recognition SVM pages
- `shared/cnn-fundamentals.md` — consolidated CNN building blocks from PR and CV sources
Updated ml-fundamentals/svm.md, pattern-recognition/svm.md, and pattern-recognition/neural-networks.md with cross-links to shared pages.
Updated index.md with all 16 new pages.

## [2026-04-15] ingest | all lectures | deep-learning

Ingested 11 lecture PDFs (01-11, including two versions of lecture 10). Created 11 wiki pages:
- `deep-learning/course-overview.md` — course structure, 11 lectures, exam themes
- `deep-learning/linear-classifiers.md` — score functions, hinge/softmax loss, SGD, computational graphs, autodiff
- `deep-learning/mle-kl-divergence.md` — entropy, cross-entropy, KL divergence, MLE as KL minimization, exponential family
- `deep-learning/neural-networks.md` — activations (ReLU/GELU/Swish/Mish), backprop, weight init, residual connections
- `deep-learning/cnns.md` — convolution variants (depthwise separable, transposed, dilated, deformable), pooling, receptive fields
- `deep-learning/normalization.md` — batch/layer/instance/group norm, weight norm, weight standardization
- `deep-learning/optimization.md` — SGD variants, Adam/AdamW, LR scheduling, weight decay, SAM, SWA
- `deep-learning/backbone-architectures.md` — AlexNet through ConvNeXt, ViT, MLP-Mixer, NAS
- `deep-learning/task-specific-architectures.md` — object detection (R-CNN, YOLO, SSD), segmentation (FCN, U-Net, DeepLab, Mask R-CNN)
- `deep-learning/reinforcement-learning.md` — MDPs, DQN, DDPG, policy gradients, actor-critic, PPO, inverse RL
- `deep-learning/implicit-layers.md` — Neural ODEs, deep equilibrium models, differentiable optimization layers
Updated shared/cnn-fundamentals.md with DL course sources and cross-links.
Updated index.md with all 11 new pages.

## [2026-04-17] ingest | NPFL087 Statistical Machine Translation | nlp

Ingested 15 PDF source documents (770+ pages total, 100% read). Created 11 NLP-specific wiki pages and 3 shared pages:

**NLP Pages**:
- `nlp/course-overview.md` — NPFL087 course structure, grading, resources, prerequisites
- `nlp/machine-translation-evaluation.md` — BLEU, METEOR, TER, human evaluation methodologies
- `nlp/smt-foundations.md` — statistical translation model, noisy channel, log-linear models, NP-hardness
- `nlp/word-alignment.md` — alignment problem, IBM Models 1-2, EM algorithm, GIZA++, hunalign
- `nlp/phrase-based-machine-translation.md` — phrase extraction, scoring, reordering, MERT, Moses
- `nlp/neural-machine-translation.md` — seq2seq, encoder-decoder, attention, autoregressive decoding, beam search
- `nlp/morphology-machine-translation.md` — lemmatization, BPE, STE, WordPiece, linguistic vs. data-driven
- `nlp/factored-translation-models.md` — linguistic feature factorization, parallel factor decoding
- `nlp/syntax-based-smt.md` — hierarchical phrase-based models, non-terminals, CYK decoding, cube pruning
- `nlp/transformers-nmt.md` — transformer architecture, positional encoding, encoder-decoder, LLM-based translation
- `nlp/decoding-algorithms.md` — beam search, stack decoding, pruning, future cost estimation, A* search

**Shared Pages** (cross-course concepts):
- `shared/attention-mechanisms.md` — scaled dot-product, multi-head attention, variants, complexity
- `shared/sequence-to-sequence.md` — encoder-decoder, teacher forcing, attention-enhanced seq2seq
- `shared/transformers.md` — architecture, self-attention, positional encoding, scaling, applications

Updated index.md with NLP section (11 pages) and shared section (3 new pages).
Identified key cross-course concepts: attention-mechanisms, sequence-to-sequence, transformers referenced from ml-methods, deep-learning.

## [2026-04-19] ingest | Programming for Engineers lectures | programming-for-engineers

Ingested 11 Quarto markdown lectures + 1 Python cheatsheet PDF (75+ pages total, 100% read). Created 11 programming-for-engineers-specific wiki pages and 3 shared cross-course pages:

**Programming for Engineers Pages**:
- `programming-for-engineers/course-overview.md` — course structure, 11-lecture curriculum, learning objectives
- `programming-for-engineers/basic-data-structures.md` — lists, dicts, tuples, sets with operations and complexity
- `programming-for-engineers/matrices-and-arrays.md` — 2D arrays, matrix operations (addition, transposition, multiplication)
- `programming-for-engineers/array-searching.md` — linear, binary, interpolation search algorithms with complexity
- `programming-for-engineers/array-processing-statistics.md` — min/max, mean, cumulative sum, integral images for 2D
- `programming-for-engineers/strings-and-io.md` — string operations, immutability, file I/O, Unicode handling
- `programming-for-engineers/numpy-fundamentals.md` — array creation, indexing, reshaping, broadcasting, aggregation
- `programming-for-engineers/sorting-algorithms.md` — bubble, quick, merge sort with complexity and visualization
- `programming-for-engineers/abstract-data-types.md` — stacks, queues, trees; OOP basics, magic methods, encapsulation
- `programming-for-engineers/recursion-and-trees.md` — recursion patterns, binary trees, tree traversal (preorder/inorder/postorder)
- `programming-for-engineers/scientific-python-libraries.md` — NumPy, SciPy, Pandas, scikit-learn, scikit-image integration

**Shared Pages** (cross-course concepts):
- `shared/algorithmic-complexity.md` — Big-O notation, operation counting, empirical measurement, practical trade-offs
- `shared/array-operations.md` — indexing, slicing, broadcasting rules, aggregation, view vs copy semantics
- `shared/visualization-techniques.md` — matplotlib plots, algorithm visualization, training curves, best practices

Updated index.md with programming-for-engineers section (11 pages) and expanded shared section (3 new pages).
Identified cross-course connections: algorithmic-complexity referenced from deep-learning, ml-methods; array-operations used in deep-learning, computer-vision; visualization-techniques used across all quantitative courses.

## [2026-05-02] ingest | lectures 9-11 | nlp

Ingested 3 additional NLP lecture PDFs (09-word-and-sent-reprs.pdf, 10-multilingual-mt.pdf, 11-multimodal-mt.pdf). Created 3 NLP-specific wiki pages and 3 shared cross-course pages:

**NLP Pages**:
- `nlp/word-and-sentence-representations.md` — Word2Vec, GloVe, FastText, ELMo, BERT, contextual embeddings, sentence pooling
- `nlp/multilingual-machine-translation.md` — Pivot-based MT, cross-lingual transfer, zero-shot translation, shared vocabularies, language family clustering
- `nlp/multimodal-machine-translation.md` — Vision-text fusion (early/late/hierarchical), spatial/semantic grounding, Multi30K dataset, BLEU improvements

**Shared Pages** (cross-course concepts):
- `shared/word-embeddings.md` — Word2Vec (Skip-gram, CBOW), GloVe, FastText subword n-grams, applications in MT and cross-lingual alignment
- `shared/contextual-embeddings.md` — ELMo (bidirectional LSTMs), BERT (MLM, NSP), T5, GPT variants, layer-wise interpretation, multilingual variants
- `shared/multimodal-fusion.md` — Early/late/intermediate fusion strategies, spatial/cross-modal attention, gated fusion, applications in translation and VQA

Updated index.md: NLP section now 14 pages (added 3), shared section now 13 pages (added 3).
Identified new cross-course connections: contextual-embeddings referenced from deep-learning, pattern-recognition; word-embeddings used in programming-for-engineers; multimodal-fusion used in computer-vision.

## [2026-05-02] update | exam-clusters | nlp

Created `nlp/exam-clusters.md` — study guide organizing NPFL087 exam topics into three tiers by importance (Tier 1: Must Know, Tier 2: Important, Tier 3: Compact Coverage). Includes confidence tracking, practice needs, and wikilinks to 20+ relevant lecture pages and concepts. Provides structured study strategy and reference guide for exam preparation.

Updated index.md: NLP section now 15 pages (added exam-clusters + exam-questions entries).

## [2026-05-03] update | word-alignment citation | nlp

Fixed missing source attribution in `nlp/word-alignment.md`. Renamed "Hamming Distance" section to "Distance Metrics for Alignment" and expanded "Weighted Edit Distance" to "Edit Distance" with proper source citations to Church-Gale 1993 paper. The section now accurately references the Church-Gale work on distance measures for sequence alignment, replacing unsourced content with properly attributed material from the foundational alignment paper.

## [2026-05-03] lint | nlp section | nlp

Audited all 16 NLP wiki pages. Found and fixed 11 issues:
- `nlp/course-overview.md`: Added wikilinks for lectures 9–11 and links to exam-questions, exam-clusters in Related Pages
- `nlp/smt-foundations.md`: Fixed typo "RBMTvs." → "RBMT vs."
- `nlp/word-alignment.md`: Fixed plain-text math in Fertility-Distortion section (φ → $\varphi$, added LaTeX for parameters), added source citation
- `nlp/neural-machine-translation.md`: Fixed plain-text `d_k` → `$d_k$`
- `nlp/transformers-nmt.md`: Fixed plain-text `d_model` → `$d_{\text{model}}$`
- `nlp/word-and-sentence-representations.md`: Added inline source citations (3 sections)
- `nlp/multilingual-machine-translation.md`: Added inline source citations (3 sections)
- `nlp/multimodal-machine-translation.md`: Added inline source citations (3 sections)
- `nlp/exam-questions.md`: Added links to exam-clusters and course-overview in Related Pages
Remaining gaps: 3 exam-cluster topics lack wiki pages (Transfer-based MT, System combination, MERT).

## [2026-05-03] ingest | MT Talks articles + transformer tutorials + course syllabus | nlp

Ingested 9 new documents from raw/nlp/: Deep Syntax.md (full NPFL087 course syllabus), 4 MT Talks articles (Constituency Trees, Dependency Trees, Phrase-based Model, Rich Vocabulary), 3 transformer tutorial articles (The Annotated Transformer, The Illustrated Transformer, Transformer Architecture: Attention Is All You Need), and untitled.md (empty stub). The research paper PDF (Promoting the knowledge of source syntax in Transformer NMT) was identified but not ingested (binary).

**New NLP pages** (2):
- `nlp/transfer-based-mt.md` — TectoMT analysis-transfer-synthesis pipeline, t-layer, HMTM tree language model, benefits/problems of deep-syntactic transfer; fills a previously identified gap
- `nlp/mt-system-combination.md` — Confusion network decoding, hypothesis selection, combining external MT with PBMT; fills a previously identified gap

**Updated pages** (5):
- `nlp/syntax-based-smt.md`: Added full Constituency Trees section (CFG, synchronous CFG, tree-to-string/string-to-tree/tree-to-tree), Dependency Trees section (Prague-style, non-projectivity, ~25% Czech sentences non-projective); updated sources and Related Pages
- `nlp/morphology-machine-translation.md`: Rewrote Language-Specific Challenges section with detailed German/Finnish/Czech content; added MT pipeline impact table; added source citation
- `shared/transformers.md`: Added exact Adam hyperparameters (β₁=0.9, β₂=0.98, ε=10⁻⁹), Noam LR schedule as LaTeX formula with rationale, dropout details (P_drop=0.1), label smoothing (ε_ls=0.1), original paper hyperparameters table, WMT training results; updated sources

Updated index.md: NLP section now 18 pages (added transfer-based-mt, mt-system-combination; both previously identified gaps now filled). MERT was already covered in phrase-based-machine-translation.md.

## [2026-05-03] lint | full wiki | all

Full wiki audit across all 87 content pages. Found and fixed 9 issue categories:

**Fixed:**
1. **8 ml-methods pages added to index.md** — course-overview, supervised-learning-recap, decision-trees-ensembles, svm, neuro-symbolic-learning, llm-structured-data, interpretability-xai, reinforcement-learning
2. **Plain-text math → LaTeX** in 6 pages: shared/bayes-classifier.md, shared/svm.md, ml-fundamentals/svm.md, ml-fundamentals/linear-classifiers.md, ml-fundamentals/generative-learning.md, ml-fundamentals/pac-learning.md, pattern-recognition/svm.md, pattern-recognition/perceptron.md
3. **5 stale `Last updated` dates** corrected (shared/transformers, nlp/syntax-based-smt, nlp/morphology-machine-translation, nlp/course-overview, nlp/exam-clusters)
4. **3 shared pages `Courses` → `Course`** (contextual-embeddings, multimodal-fusion, word-embeddings)
5. **Source path normalization** — removed `raw/nlp/` prefix from 5 pages (transfer-based-mt, mt-system-combination, syntax-based-smt, morphology-machine-translation, shared/transformers)
6. **4 missing cross-links added** between overlapping pages (deep-learning/RL↔ml-methods/RL, pattern-recognition/decision-trees↔ml-methods/decision-trees-ensembles, pattern-recognition/neural-networks→deep-learning/neural-networks)

**Remaining (documented, not yet fixed):**
- Plain-text math in ~7 more pattern-recognition pages (adaboost, non-parametric-methods, clustering, decision-trees, parameter-estimation, pca) and ~5 deep-learning pages
- Short-form wikilinks in ml-fundamentals/ (~40 instances using `[[svm]]` instead of `[[ml-fundamentals/svm]]`)
- Plain-text math in ml-fundamentals/prediction-evaluation.md
- Missing log entry for original ml-methods ingest

## [2026-05-11] lint | computer-vision section | computer-vision

Audited all 14 computer-vision wiki pages. Found 1 issue category (plain-text math) across 10 pages; remaining 4 pages (cnn-architectures, object-detection-segmentation, learned-matching, course-overview) were clean.

**Fixed — plain-text math → LaTeX in 10 pages:**
- `generative-models.md`: GAN minimax objective, ELBO, reparameterization trick, q_φ/p_θ notation
- `deep-metric-learning.md`: contrastive/triplet/ArcFace/CosFace loss formulas, embedding function notation
- `self-supervised-learning.md`: InfoNCE loss, MoCo momentum update
- `camera-models-calibration.md`: K matrix as bmatrix, P=K[R|t], distortion formula, DLT/Zhang constraints
- `epipolar-geometry.md`: epipolar constraint x'ᵀFx=0, 7-pt/8-pt formulas, E=[t]×R, disparity Z=fB/d
- `3d-reconstruction.md`: bundle adjustment objective, projection equations
- `local-features.md`: Harris structure tensor, eigenvalue analysis, LoG/DoG, MSER stability
- `ransac.md`: termination criterion k=log(1-p)/log(1-wⁿ), threshold/noise notation
- `visual-tracking.md`: KCF circulant DFT trick, ridge regression, kernelized correlation filter
- `image-retrieval.md`: IDF, VLAD residual formula, complexity notation

No other issues found: all pages had proper structure, citations, Related Pages sections, full-path wikilinks, and no orphans.

## [2026-05-12] ingest | ls25_lecture_bayesian.pdf, ls26_lecture_dl.pdf, ls26_lecture_model_selection.pdf, ls26_lecture_unsuper.pdf | ml-fundamentals

Ingested 4 new lecture PDFs. Created 4 wiki pages:
- `ml-fundamentals/bayesian-learning.md` — MLE vs MAP vs full Bayes, conjugate priors, linear-Gaussian model, predictive posterior, uncertainty decomposition
- `ml-fundamentals/deep-learning-generalization.md` — double descent, implicit regularization/bias of GD, architectural inductive bias
- `ml-fundamentals/model-selection.md` — SRM, hold-out, K-fold CV, nested CV, Nadeau-Bengio corrected CIs
- `ml-fundamentals/unsupervised-learning.md` — EM algorithm, GMMs, Jensen's inequality, coordinate ascent view
Updated cross-links in: generative-learning.md, empirical-risk-minimization.md, shared/bayes-classifier.md. Updated index.md.

## [2026-05-12] ingest | mlm_lecture_9.pdf, mlm_lecture_11.pdf, bandits-intro-ucb.pdf, thompson_sampling.pdf | ml-methods

Ingested 4 new lecture PDFs. Created 3 wiki pages:
- `ml-methods/model-free-rl.md` — MC policy evaluation, TD learning, SARSA, Q-learning
- `ml-methods/deep-rl.md` — VFA (linear + NN), DQN, experience replay, Double Q-learning, policy gradient, REINFORCE, actor-critic
- `ml-methods/multi-armed-bandits.md` — Regret, UCB with proof sketch, Thompson sampling, Beta-Bernoulli conjugacy
Updated cross-links in: reinforcement-learning.md. Updated index.md.

## [2026-05-12] ingest | courses_mpv_labs_exam_questions [CourseWare Wiki].pdf | computer-vision

Created `computer-vision/exam-questions.md` — all 43 exam questions organized by topic (local features, retrieval, RANSAC, detection, tracking, Hough transform, DNNs) with cross-links to 10+ wiki pages per section. Added topic coverage map and notably absent topics. Updated index.md and course-overview.md.

## [2026-05-16] ingest | understanding-machine-learning-theory-algorithms.pdf | ml-fundamentals

Ingested relevant chapters from Shalev-Shwartz and Ben-David's *Understanding Machine Learning*. Created `ml-fundamentals/understanding-machine-learning.md` as a source summary and chapter map. Enhanced `course-overview.md`, `empirical-risk-minimization.md`, `pac-learning.md`, `vc-dimension.md`, `model-selection.md`, `linear-classifiers.md`, `svm.md`, `prediction-evaluation.md`, `generative-learning.md`, `unsupervised-learning.md`, `deep-learning-generalization.md`, and `shared/kernel-functions.md` with textbook-backed context, citations, and links. Updated `wiki/index.md`.

## [2026-05-21] lint | nlp and ml-fundamentals source grounding | nlp, ml-fundamentals

Audited NLP and ML Fundamentals wiki pages against available raw resources. Fixed the finite-class PAC sample complexity formula in `ml-fundamentals/pac-learning.md` to match `ls26_lecture_pac.pdf` and seminar examples. Corrected the NLP exam-question description in `wiki/index.md` from a stale "12 questions" summary to the 7-question written-exam structure in `Statistical Machine Translation _ ÚFAL_exam_questions.pdf`.

## [2026-05-21] ingest | previously unreflected NLP raw resources | nlp

Ingested substantive NLP raw resources that were present but not accurately reflected in page source metadata or content. Updated `nlp/phrase-based-machine-translation.md` with `Phrase-based Model - MT Talks.md` details on non-linguistic phrases, consistency constraints, stack-based decoding, coverage vectors, pruning, and future cost. Updated `nlp/transformers-nmt.md` with Transformer tutorial sources and `Promoting the knowledge of source syntax in Transformer NMT.pdf`, including its multi-task syntax experiments, dummy diagonal-parse control, and regularization interpretation. Rewrote `nlp/machine-translation-evaluation.md` to match `01-eval.pdf`, replacing unsupported METEOR/TER-centered material with manual evaluation protocols, BLEU caveats, chrF/BEER/HTER mentions, empirical confidence intervals, LLM-judge caveats, and component-vs-end-to-end evaluation. Updated `nlp/exam-questions.md` and `nlp/morphology-machine-translation.md` to point evaluation advice back to supported lecture material. Updated `shared/transformers.md` source metadata and `wiki/index.md`.

Metadata-only or one-line raw stubs (`Statistical Machine Translation.md`, `Statistical Machine Translation (1).md`, `Statistical Machine Translation (2).md`, `Statistical Machine Translation (3).md`, `untitled.md`) were inspected but not used as factual sources because they contain no substantive lecture content.
