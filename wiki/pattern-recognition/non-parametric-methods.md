# Non-Parametric Methods

**Summary**: Classification and density estimation methods that make no assumptions about the functional form of class distributions. Covers k-NN, Cover-Hart theorem, Parzen windows, curse of dimensionality, and minimum distance classifiers.

**Course**: pattern-recognition

**Sources**: pr_02_non_bayes_2023.pdf, pr_ml_course_lecture_4_knn_non_parametric_density_estimation_.pdf

**Last updated**: 2026-04-15

---

## k-Nearest Neighbors (k-NN)

Given a training set and a query point x, the **k-NN classifier** finds the k closest training points and assigns x to the majority class among them (source: pattern-recognition/pr_02_non_bayes_2023.pdf).

### Properties
- **No training phase** — all computation happens at prediction time
- **Decision boundary** is piecewise linear (Voronoi-based for 1-NN)
- Choice of **distance metric** matters: Euclidean, Manhattan, Mahalanobis
- Choice of **k** controls the bias-variance trade-off: small k = low bias, high variance

### Cover-Hart Theorem

For 1-NN with infinite training data, the asymptotic error rate R_1NN satisfies (source: pattern-recognition/pr_02_non_bayes_2023.pdf):

**R* <= R_1NN <= 2R*(1 - R*)**

where R* is the Bayes error. This means the 1-NN error is at most twice the Bayes error. For k-NN as k -> infinity and k/n -> 0, R_kNN -> R*.

## Parzen Windows (Kernel Density Estimation)

Estimate the density at point x by placing a kernel K centered on each training point (source: pattern-recognition/pr_ml_course_lecture_4_knn_non_parametric_density_estimation_.pdf):

**p_hat(x) = (1/n) sum_i (1/h^d) K((x - x_i) / h)**

where h is the **bandwidth** (smoothing parameter) and d is the dimensionality.

### Common Kernels for KDE
- **Rectangular**: K(u) = 1/2 for |u| <= 1 (histogram-like)
- **Gaussian**: K(u) = (1/sqrt(2*pi)) exp(-u^2/2)
- **Epanechnikov**: K(u) = 3/4 (1 - u^2) for |u| <= 1 (optimal in MSE sense)

### Bandwidth Selection

The bandwidth h controls the bias-variance trade-off (source: pattern-recognition/pr_ml_course_lecture_4_knn_non_parametric_density_estimation_.pdf):
- **h too small**: density estimate is spiky (low bias, high variance)
- **h too large**: density estimate is oversmoothed (high bias, low variance)

Methods for choosing h:
- **Silverman's rule**: h = 1.06 * sigma * n^(-1/5) for Gaussian data
- **Cross-validation**: maximize leave-one-out likelihood

### Multivariate KDE

For d-dimensional data, use a product kernel or a multivariate kernel with bandwidth matrix H. The **Silverman rule** generalizes to h_j = sigma_j * n^(-1/(d+4)).

## Nearest-Neighbor Density Estimation

Fix the number of neighbors k and let the volume grow to enclose them (source: pattern-recognition/pr_ml_course_lecture_4_knn_non_parametric_density_estimation_.pdf):

**p_hat(x) = k / (n * V_k(x))**

where V_k(x) is the volume of the smallest hypersphere centered at x containing k training points. This is dual to Parzen windows (fix volume vs fix k).

## Curse of Dimensionality

In high dimensions (source: pattern-recognition/pr_02_non_bayes_2023.pdf):
- Volume of a hypercube grows exponentially — data becomes sparse
- Most of the volume is near the surface, not the center
- Distance between nearest and farthest neighbor converges — all points appear equidistant
- k-NN and KDE require exponentially more data to maintain accuracy

## Minimum Distance Classifier

Assign x to the class whose **mean** (prototype) is closest (source: pattern-recognition/pr_02_non_bayes_2023.pdf):

**d(x) = argmin_k ||x - mu_k||**

This is optimal for equal-covariance spherical Gaussians with equal priors. The decision boundary is the perpendicular bisector between class means.

## Related pages

- [[pattern-recognition/bayesian-decision-theory]]
- [[pattern-recognition/parameter-estimation]]
- [[pattern-recognition/clustering]]
- [[shared/bayes-classifier]]
