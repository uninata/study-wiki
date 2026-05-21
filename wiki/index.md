# Wiki Index

Master table of contents for the CVUT LLM Wiki.

## Courses

### ML Fundamentals
- [[ml-fundamentals/course-overview]] — Logical structure of the course and key exam themes
- [[ml-fundamentals/understanding-machine-learning]] — Source summary and chapter map for Shalev-Shwartz & Ben-David's textbook
- [[ml-fundamentals/empirical-risk-minimization]] — True/empirical risk, error decomposition, Hoeffding + union bound generalization
- [[ml-fundamentals/generative-learning]] — MLE for Gaussian mixtures, Fisher information, Cramer-Rao bound, plug-in Bayes
- [[ml-fundamentals/pac-learning]] — PAC framework, sample complexity for finite H, decision tree and LLM prompt examples
- [[ml-fundamentals/vc-dimension]] — Shattering, VC dimension examples, Fundamental Theorem, Structural Risk Minimization
- [[ml-fundamentals/svm]] — Hard/soft margin, hinge loss, dual formulation, kernel trick, support vectors
- [[ml-fundamentals/linear-classifiers]] — Perceptron (Novikoff theorem), logistic regression, multiclass, generic linear classifier
- [[ml-fundamentals/prediction-evaluation]] — Hoeffding confidence intervals, test set size requirements, loss-specific formulas
- [[ml-fundamentals/bayesian-learning]] — MLE vs MAP vs full Bayes, conjugate priors, linear-Gaussian model, uncertainty decomposition
- [[ml-fundamentals/deep-learning-generalization]] — Double descent, implicit regularization/bias of GD, architectural inductive bias
- [[ml-fundamentals/model-selection]] — SRM, hold-out, K-fold CV, nested CV, Nadeau-Bengio corrected CIs
- [[ml-fundamentals/unsupervised-learning]] — EM algorithm, GMMs, Jensen's inequality lower bound, coordinate ascent view

### ML Methods
- [[ml-methods/course-overview]] — Course structure, 8-week curriculum from classical supervised learning to RL
- [[ml-methods/supervised-learning-recap]] — ERM, bias-variance, cross-validation, regularization (L1, L2, Elastic Net)
- [[ml-methods/decision-trees-ensembles]] — CART, random forests, AdaBoost, gradient boosting (XGBoost), stacking
- [[ml-methods/svm]] — Hard/soft margin, kernel trick, multiclass, SVR
- [[ml-methods/neuro-symbolic-learning]] — GNN limits (1-WL test), knowledge graph embeddings (TransE), logic as loss
- [[ml-methods/llm-structured-data]] — Transformers, in-context learning, tabular foundation models, Graphormer, Text-to-SQL
- [[ml-methods/interpretability-xai]] — Fidelity-plausibility paradox, LIME, SHAP, concept-based interpretability
- [[ml-methods/reinforcement-learning]] — MRPs, MDPs, Bellman equations, policy/value iteration, convergence
- [[ml-methods/model-free-rl]] — MC policy evaluation, TD learning, SARSA, Q-learning
- [[ml-methods/deep-rl]] — Value function approximation, DQN, Double Q-learning, policy gradient, REINFORCE, actor-critic
- [[ml-methods/multi-armed-bandits]] — Regret, UCB with sublinear guarantee, Thompson sampling, Bayesian bandits

### Computer Vision
- [[computer-vision/course-overview]] — Course structure, key topics, and exam themes
- [[computer-vision/exam-questions]] — Full 43-question exam bank with cross-links to wiki pages
- [[computer-vision/local-features]] — Harris corners, scale-space, SIFT, BRIEF, ORB, MSER, affine covariant regions
- [[computer-vision/ransac]] — Robust estimation, hypothesis-and-verify, PROSAC, LO-RANSAC, USAC
- [[computer-vision/epipolar-geometry]] — Fundamental/essential matrix, 7-pt/8-pt algorithms, stereo rectification
- [[computer-vision/camera-models-calibration]] — Pinhole model, P=K[R|t], DLT, Zhang's method, radial distortion
- [[computer-vision/3d-reconstruction]] — Stereo, triangulation, SfM, bundle adjustment, DUSt3R, MASt3R, Depth Anything
- [[computer-vision/cnn-architectures]] — LeNet through ResNet, transfer learning, data augmentation
- [[computer-vision/object-detection-segmentation]] — R-CNN family, YOLO, SSD, FCN, U-Net, Mask R-CNN
- [[computer-vision/generative-models]] — GANs (adversarial training), VAEs (reparameterization trick, ELBO)
- [[computer-vision/learned-matching]] — SuperPoint, SuperGlue (GNN + attention), LoFTR (detector-free)
- [[computer-vision/visual-tracking]] — KLT, KCF (correlation filters), TLD (P-N learning), SAM-based tracking
- [[computer-vision/deep-metric-learning]] — Contrastive/triplet loss, ArcFace/CosFace, face recognition
- [[computer-vision/self-supervised-learning]] — SimCLR, MoCo, BYOL, DINO, MAE
- [[computer-vision/image-retrieval]] — BoVW, VLAD, Fisher vectors, deep retrieval, spatial verification

### Selected Topics in AI (STAI)
_No pages yet._

### NLP
- [[nlp/course-overview]] — NPFL087 course structure, grading, prerequisites, resources
- [[nlp/exam-questions]] — Exam question bank for the 7-question written exam, with topic prompts and expected knowledge
- [[nlp/exam-clusters]] — Study guide organizing exam topics into three tiers by importance, with confidence tracking and practice needs
- [[nlp/machine-translation-evaluation]] — Manual MT evaluation, BLEU caveats, empirical confidence intervals, LLM judges
- [[nlp/smt-foundations]] — Statistical translation model, noisy channel, log-linear models, NP-hardness
- [[nlp/word-alignment]] — Alignment problem formulation, IBM Models 1-2, EM algorithm, GIZA++, hunalign
- [[nlp/phrase-based-machine-translation]] — Phrase extraction, scoring, stack-based decoding, reordering models, MERT tuning, Moses
- [[nlp/neural-machine-translation]] — Encoder-decoder, attention mechanisms, autoregressive decoding, beam search
- [[nlp/morphology-machine-translation]] — Lemmatization, BPE, STE, WordPiece, linguistic vs. data-driven approaches
- [[nlp/factored-translation-models]] — Linguistic factor decomposition, parallel factor decoding, morphologically rich languages
- [[nlp/syntax-based-smt]] — Hierarchical phrase-based models, CFG, constituency/dependency trees, CYK decoding, cube pruning
- [[nlp/transfer-based-mt]] — TectoMT, deep-syntactic (t-layer) transfer, HMTM tree language model, analysis-transfer-synthesis pipeline
- [[nlp/mt-system-combination]] — Confusion network decoding, hypothesis selection, combining external MT with PBMT
- [[nlp/transformers-nmt]] — Transformer architecture for NMT, self-attention, positional encoding, source-syntax evidence, LLM-based translation
- [[nlp/decoding-algorithms]] — Beam search, stack decoding, pruning strategies, future cost estimation, A* search
- [[nlp/word-and-sentence-representations]] — Word embeddings (Word2Vec, GloVe, FastText), contextual embeddings (ELMo, BERT), sentence pooling
- [[nlp/multilingual-machine-translation]] — Pivot-based MT, cross-lingual transfer, zero-shot translation, shared vocabularies
- [[nlp/multimodal-machine-translation]] — Vision-text fusion, image grounding, spatial attention, multimodal evaluation

### Deep Learning
- [[deep-learning/course-overview]] — Course structure, 11 lectures, key exam themes
- [[deep-learning/linear-classifiers]] — Score functions, hinge/softmax loss, SGD, computational graphs, autodiff
- [[deep-learning/mle-kl-divergence]] — Entropy, cross-entropy, KL divergence, MLE as KL minimization, exponential family
- [[deep-learning/neural-networks]] — Activations (ReLU/GELU/Swish/Mish), backprop, weight init (Xavier/He), residual connections
- [[deep-learning/cnns]] — Standard/depthwise-separable/transposed/dilated/deformable convolutions, pooling, receptive fields
- [[deep-learning/normalization]] — Batch/layer/instance/group norm, weight norm, weight standardization
- [[deep-learning/optimization]] — Adam, AdamW, LR scheduling (cosine, warmup), weight decay vs L2, SAM, SWA
- [[deep-learning/backbone-architectures]] — AlexNet through ConvNeXt, ViT, MLP-Mixer, NAS, EfficientNet
- [[deep-learning/task-specific-architectures]] — Object detection (R-CNN, YOLO, SSD), segmentation (FCN, U-Net, DeepLab, Mask R-CNN)
- [[deep-learning/reinforcement-learning]] — MDPs, DQN, DDPG, policy gradients, actor-critic, PPO, inverse RL
- [[deep-learning/implicit-layers]] — Neural ODEs, deep equilibrium models, differentiable optimization layers

### Cybersecurity
_No pages yet._

### Programming for Engineers
- [[programming-for-engineers/course-overview]] — Course structure, learning objectives, 11-lecture curriculum
- [[programming-for-engineers/basic-data-structures]] — Lists, dicts, tuples, sets with operations and complexity
- [[programming-for-engineers/matrices-and-arrays]] — 2D arrays, matrix operations (addition, transposition, multiplication)
- [[programming-for-engineers/array-searching]] — Linear, binary, interpolation search algorithms and complexity
- [[programming-for-engineers/array-processing-statistics]] — Min/max, mean, cumulative sum, integral images
- [[programming-for-engineers/strings-and-io]] — String operations, immutability, file I/O, Unicode
- [[programming-for-engineers/numpy-fundamentals]] — Array creation, indexing, reshaping, broadcasting, aggregation
- [[programming-for-engineers/sorting-algorithms]] — Bubble, quick, merge sort with complexity comparison
- [[programming-for-engineers/abstract-data-types]] — Stacks, queues, trees; OOP basics and magic methods
- [[programming-for-engineers/recursion-and-trees]] — Recursion patterns, tree traversal (preorder, inorder, postorder)
- [[programming-for-engineers/scientific-python-libraries]] — NumPy, SciPy, Pandas, scikit-learn, scikit-image

### Pattern Recognition and Machine Learning
- [[pattern-recognition/course-overview]] — Course structure, key topics, and exam themes
- [[pattern-recognition/bayesian-decision-theory]] — Bayes decision rule, ROC curves, Neyman-Pearson, minimax
- [[pattern-recognition/non-parametric-methods]] — k-NN, Cover-Hart theorem, Parzen windows, curse of dimensionality
- [[pattern-recognition/parameter-estimation]] — MLE, MAP, Bayesian estimation, conjugate priors, KDE
- [[pattern-recognition/logistic-regression]] — Sigmoid, softmax, IRLS, Bayesian logistic regression, regularization
- [[pattern-recognition/perceptron]] — Convergence theorem, kernel perceptron, Kozinec algorithm
- [[pattern-recognition/svm]] — Hard/soft margin, kernel SVM, structured SVM, SVR
- [[pattern-recognition/adaboost]] — Boosting, exponential loss, margin theory, Real/LogitBoost
- [[pattern-recognition/clustering]] — K-means, K-means++, K-medians, K-medoids, Weiszfeld algorithm
- [[pattern-recognition/pca]] — Eigendecomposition, kernel PCA, probabilistic PCA, ICA
- [[pattern-recognition/decision-trees]] — CART, Gini/entropy, pruning, random forests, feature importance
- [[pattern-recognition/em-algorithm]] — GMMs, E-step/M-step, connection to K-means, convergence
- [[pattern-recognition/neural-networks]] — MLP, backpropagation, CNN/RNN, regularization, applications

### Shared Concepts
- [[shared/bayes-classifier]] — Optimal predictor for each loss function, approximation error analysis
- [[shared/kernel-functions]] — Kernel trick, Mercer's theorem, polynomial/RBF kernels, kernelized regression
- [[shared/svm]] — Maximum-margin classifiers, hard/soft margin, dual, kernel SVM, structured SVM, SVR
- [[shared/cnn-fundamentals]] — CNN building blocks, key architectures (LeNet–ResNet), training techniques
- [[shared/attention-mechanisms]] — Scaled dot-product attention, multi-head attention, interpretability, efficient variants
- [[shared/sequence-to-sequence]] — Encoder-decoder architecture, teacher forcing, attention-enhanced seq2seq
- [[shared/transformers]] — Self-attention, multi-layer architecture, positional encoding, encoder-decoder variants
- [[shared/algorithmic-complexity]] — Big-O notation, operation counting, empirical measurement, practical trade-offs
- [[shared/array-operations]] — Indexing, slicing, broadcasting, aggregation, view vs copy semantics
- [[shared/visualization-techniques]] — matplotlib plots, algorithm visualization, training curves, best practices
- [[shared/word-embeddings]] — Word2Vec, GloVe, FastText, static representations, OOV handling, cross-lingual alignment
- [[shared/contextual-embeddings]] — ELMo, BERT, transformers, context-dependent representations, layer-wise analysis
- [[shared/multimodal-fusion]] — Vision-text integration, spatial/cross-modal attention, gated fusion, applications
