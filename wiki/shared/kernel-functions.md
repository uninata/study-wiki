# Kernel Functions

**Summary**: Kernel functions compute inner products in high-dimensional feature spaces without explicitly constructing the feature vectors. This "kernel trick" enables nonlinear extensions of any algorithm that depends only on dot products (SVMs, linear regression, perceptron, PCA).

**Course**: ml-fundamentals (primary), pattern-recognition, ml-methods

**Sources**: ls26_lecture_svm.pdf, ls26_seminar_kernels.pdf, pr_07_svm_2018.pdf, pr_06_perceptron_2022.pdf

**Last updated**: 2026-04-15

---

## Definition

A function k: X x X -> R is a **kernel function** if there exists a feature map phi: X -> R^n such that (source: ml-fundamentals/ls26_lecture_svm.pdf):

**k(x, x') = ⟨phi(x), phi(x')⟩**

The feature space can be finite or infinite-dimensional. The kernel computes the inner product in O(d) time regardless of the feature space dimension.

## Mercer's Theorem

A function k(x, x') is a valid kernel if and only if for any finite set {x_1, ..., x_m}, the **Gram matrix** K_{ij} = k(x_i, x_j) is symmetric positive semi-definite (source: ml-fundamentals/ls26_lecture_svm.pdf).

## Common Kernels

| Kernel | k(x, x') | Input space | Feature dim | Eval cost |
|--------|----------|-------------|-------------|-----------|
| Homogeneous polynomial (deg 2) | ⟨x, x'⟩² | R^d | d(d+1)/2 | O(d) |
| Polynomial (degree p) | ⟨x, x'⟩^p | R^d | C(d+p-1, p) | O(d) |
| RBF (Gaussian) | exp(-gamma \|\|x - x'\|\|²) | R^d | infinity | O(d) |
| String sub-sequence | dynamic programming | strings | \|Sigma\|^q | O(q\|s\|\|t\|) |

(source: ml-fundamentals/ls26_lecture_svm.pdf)

## Homogeneous Polynomial Kernel (Degree 2)

The feature map phi: R^d -> R^{d(d+1)/2} has entries (source: ml-fundamentals/ls26_seminar_kernels.pdf):

phi(x) = (x_1², sqrt(2) x_1 x_2, sqrt(2) x_1 x_3, ..., x_2², sqrt(2) x_2 x_3, ..., x_d²)^T

**Proof that k(x, x') = ⟨x, x'⟩²**:

Expand ⟨x, x'⟩² = (sum_i x_i x_i')² = sum_i (x_i x_i')² + 2 sum_{i<j} (x_i x_j)(x_i' x_j')

This matches ⟨phi(x), phi(x')⟩ = sum_i (x_i²)(x_i'²) + sum_{i!=j} (sqrt(2) x_i x_j)(sqrt(2) x_i' x_j').

## The Kernel Trick

Any algorithm that depends on inputs only through dot products ⟨x_i, x_j⟩ can be "kernelized" by replacing ⟨x_i, x_j⟩ with k(x_i, x_j). This implicitly operates in the feature space without computing phi(x).

### Kernelized algorithms (covered in this course):

- **[[ml-fundamentals/svm#kernel-svm|Kernel SVM]]**: replace ⟨x_i, x_j⟩ in the dual with k(x_i, x_j)
- **Kernelized linear regression**: express w = sum c_i x_i, then h(x) = sum c_i k(x_i, x) + b (source: ml-fundamentals/ls26_seminar_kernels.pdf)

## Kernelized Linear Regression

The linear regression predictor h(x) = w^T x + b can be rewritten using w = X^T c for some coefficients c, so that (source: ml-fundamentals/ls26_seminar_kernels.pdf):

h(x) = sum_i c_i ⟨x_i, x⟩ + b

Replacing dot products with kernels: **h(x) = sum_i c_i k(x_i, x) + b**

This enables nonlinear regression in high-dimensional feature spaces with the same closed-form solution structure.

## Related pages

- [[ml-fundamentals/svm]]
- [[ml-fundamentals/linear-classifiers]]
- [[pattern-recognition/svm]]
- [[pattern-recognition/perceptron]]
- [[pattern-recognition/pca]]
