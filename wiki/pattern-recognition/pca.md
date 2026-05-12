# Principal Component Analysis (PCA)

**Summary**: Linear dimensionality reduction that finds orthogonal directions of maximum variance in the data. Covers eigendecomposition, reconstruction error, variance explained, kernel PCA, probabilistic PCA, factor analysis, and ICA.

**Course**: pattern-recognition

**Sources**: pr_12_pca_2017_01_02.pdf

**Last updated**: 2026-04-15

---

## Standard PCA

Given centered data X (n x d), PCA finds orthogonal directions that capture maximum variance (source: pattern-recognition/pr_12_pca_2017_01_02.pdf).

### Covariance Matrix Approach

1. Compute the sample covariance matrix: **C = (1/n) X^T X**
2. Eigendecomposition: C = V Lambda V^T, where Lambda = diag(lambda_1, ..., lambda_d) with lambda_1 >= ... >= lambda_d
3. The k-th principal component direction is the k-th eigenvector v_k
4. Project data: z = V_p^T x, where V_p = [v_1, ..., v_p] contains the top p eigenvectors

### Variance Explained

The proportion of variance captured by the first p components (source: pattern-recognition/pr_12_pca_2017_01_02.pdf):

**variance_explained = sum_{k=1}^{p} lambda_k / sum_{k=1}^{d} lambda_k**

Choose p to retain a desired fraction (e.g., 95%) of total variance.

### Reconstruction Error

The reconstruction x_hat = V_p V_p^T x has MSE (source: pattern-recognition/pr_12_pca_2017_01_02.pdf):

**MSE = sum_{k=p+1}^{d} lambda_k**

PCA minimizes reconstruction error among all linear projections to p dimensions.

## Kernel PCA

For nonlinear dimensionality reduction, apply PCA in a kernel-induced feature space (source: pattern-recognition/pr_12_pca_2017_01_02.pdf):

1. Compute the kernel (Gram) matrix: K_{ij} = k(x_i, x_j)
2. Center the kernel matrix: K_c = K - 1_n K - K 1_n + 1_n K 1_n
3. Eigendecomposition of K_c: K_c = U Lambda U^T
4. The k-th principal component of a new point x: z_k = sum_i alpha_k^i k(x_i, x)

This finds nonlinear structure without explicitly computing the feature map. See [[shared/kernel-functions]].

## Probabilistic PCA

A latent variable model that recovers PCA as a special case (source: pattern-recognition/pr_12_pca_2017_01_02.pdf):

- Latent variable: z ~ N(0, I_p)
- Observation model: x = Wz + mu + epsilon, where epsilon ~ N(0, sigma^2 I)
- MLE for W recovers the PCA solution (up to rotation)

Advantages over standard PCA:
- Handles missing data naturally
- Provides a likelihood for model comparison
- Can be estimated with EM (useful when d is very large)

## Factor Analysis

Similar to probabilistic PCA but with a **diagonal** noise covariance (source: pattern-recognition/pr_12_pca_2017_01_02.pdf):

x = Wz + mu + epsilon, where epsilon ~ N(0, Psi) with Psi = diag(psi_1, ..., psi_d)

Each observed dimension has its own noise variance. More flexible than PPCA but no longer has a closed-form MLE — requires EM or gradient methods.

## Independent Component Analysis (ICA)

Finds **statistically independent** (not just uncorrelated) components (source: pattern-recognition/pr_12_pca_2017_01_02.pdf):

x = As, where s has independent non-Gaussian components.

Key differences from PCA:
- PCA: orthogonal, maximizes variance, works with Gaussian data
- ICA: maximizes independence (e.g., via kurtosis or mutual information), requires non-Gaussianity
- Classic application: blind source separation (cocktail party problem)

## Sparse PCA

Adds an L1 penalty to encourage sparse loadings (source: pattern-recognition/pr_12_pca_2017_01_02.pdf). The principal components use only a subset of original features, improving interpretability at the cost of optimality.

## Related pages

- [[pattern-recognition/clustering]]
- [[pattern-recognition/em-algorithm]]
- [[pattern-recognition/neural-networks]]
- [[shared/kernel-functions]]
