# Pattern Recognition and Machine Learning — Course Overview

**Summary**: Structure and key topics of the Pattern Recognition and Machine Learning course at CVUT. Covers Bayesian decision theory, parametric and non-parametric estimation, linear classifiers, SVMs, boosting, clustering, dimensionality reduction, decision trees, EM algorithm, and neural networks.

**Course**: pattern-recognition

**Sources**: All 13 lecture PDFs in raw/pattern-recognition/

**Last updated**: 2026-04-15

---

## Course Structure

The course progresses from foundational decision theory through increasingly complex classifiers and unsupervised methods:

1. **Bayesian decision theory** (pr_01) — Prior/posterior, Bayes decision rule, loss functions, discriminant functions, ROC curves, Neyman-Pearson criterion
2. **Non-parametric methods** (pr_02, knn_lecture) — k-NN classification, Cover-Hart theorem, Parzen windows, curse of dimensionality, minimax classifiers
3. **Parameter estimation** (pr_03) — MLE, MAP, Bayesian estimation, conjugate priors, non-parametric density estimation (KDE, bandwidth selection)
4. **Logistic regression** (pr_05) — Sigmoid, cross-entropy, gradient descent, IRLS, softmax for multiclass, Bayesian logistic regression, regularization
5. **Perceptron** (pr_06) — Algorithm, convergence theorem, margin bound, kernel perceptron, Kozinec algorithm
6. **Support vector machines** (pr_07) — Maximum margin, primal/dual, KKT conditions, soft margin, kernel SVM, SVR
7. **AdaBoost** (pr_08) — Weak/strong learners, data reweighting, exponential loss, margin theory, Real AdaBoost, LogitBoost
8. **Clustering** (pr_10) — K-means, Lloyd's algorithm, K-means++ initialization, K-medians, K-medoids, string clustering
9. **PCA** (pr_12) — Eigendecomposition, variance explained, kernel PCA, probabilistic PCA, ICA
10. **Decision trees** (pr_13) — CART, Gini/entropy/misclassification, pruning, random forests, bagging
11. **EM algorithm** (em_2020) — Mixture models, E-step/M-step, GMMs, connection to K-means, convergence
12. **Neural networks** (neural_networks_2020) — MLP, backpropagation, activation functions, regularization, CNN/RNN overview, applications

## Key Exam Themes

- **Bayesian decision making**: Deriving optimal decision rules for given loss functions, computing ROC curves, Neyman-Pearson vs minimax strategies
- **Estimation**: MLE vs MAP vs full Bayesian — properties, trade-offs, conjugate prior derivations
- **Linear classifiers**: Comparing perceptron, logistic regression, and SVM — convergence guarantees, decision boundaries, kernel extensions
- **Boosting**: AdaBoost weight updates, training error bounds, margin interpretation
- **Unsupervised learning**: K-means convergence and local minima, K-means++ guarantees, EM derivation for GMMs, PCA reconstruction error
- **Trees and ensembles**: Splitting criteria comparison, pruning, random forests vs boosted trees

## Overlap with Other Courses

This course shares significant content with [[ml-fundamentals/course-overview|ML Fundamentals]]:
- [[shared/bayes-classifier]] — Bayes predictor, approximation error (both courses)
- [[shared/kernel-functions]] — Kernel trick, Mercer's theorem (both courses)
- SVM: PR covers structured SVM and SVR in addition to the standard formulation
- Perceptron: PR adds Kozinec algorithm and voted/averaged perceptron
- Logistic regression: PR covers Bayesian logistic regression with Laplace approximation

## Related pages

- [[pattern-recognition/bayesian-decision-theory]]
- [[pattern-recognition/non-parametric-methods]]
- [[pattern-recognition/parameter-estimation]]
- [[pattern-recognition/logistic-regression]]
- [[pattern-recognition/perceptron]]
- [[pattern-recognition/svm]]
- [[pattern-recognition/adaboost]]
- [[pattern-recognition/clustering]]
- [[pattern-recognition/pca]]
- [[pattern-recognition/decision-trees]]
- [[pattern-recognition/em-algorithm]]
- [[pattern-recognition/neural-networks]]
