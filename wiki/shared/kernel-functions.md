# Kernel Functions

**Summary**: Kernel functions compute inner products in high-dimensional feature spaces without explicitly constructing the feature vectors. This "kernel trick" enables nonlinear extensions of any algorithm that depends only on dot products (SVMs, linear regression, perceptron, PCA).

**Course**: ml-fundamentals (primary), pattern-recognition, ml-methods

**Sources**: ls26_lecture_svm.pdf, ls26_seminar_kernels.pdf, understanding-machine-learning-theory-algorithms.pdf, pr_07_svm_2018.pdf, pr_06_perceptron_2022.pdf

**Last updated**: 2026-05-16

---

## Definition

A function $k: \mathcal{X} \times \mathcal{X} \to \mathbb{R}$ is a **kernel function** if there exists a feature map $\phi: \mathcal{X} \to \mathbb{R}^n$ or, more generally, $\phi: \mathcal{X} \to \mathcal{F}$ for a Hilbert space $\mathcal{F}$ such that (source: ml-fundamentals/ls26_lecture_svm.pdf; source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf):

$$k(\mathbf{x}, \mathbf{x}') = \langle \phi(\mathbf{x}), \phi(\mathbf{x}') \rangle$$

The feature space can be finite or infinite-dimensional. The kernel computes the inner product without explicitly materializing $\phi(\mathbf{x})$, which can make nonlinear learning computationally feasible even when $\mathcal{F}$ is huge (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Mercer's Theorem

A function $k(\mathbf{x}, \mathbf{x}')$ is a valid kernel if and only if for any finite set $\{\mathbf{x}_1, \ldots, \mathbf{x}_m\}$, the **Gram matrix** $K_{ij} = k(\mathbf{x}_i, \mathbf{x}_j)$ is symmetric positive semi-definite (source: ml-fundamentals/ls26_lecture_svm.pdf; source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Common Kernels

| Kernel | $k(\mathbf{x}, \mathbf{x}')$ | Input space | Feature dim | Eval cost |
|--------|----------|-------------|-------------|-----------|
| Homogeneous polynomial (deg 2) | $\langle \mathbf{x}, \mathbf{x}' \rangle^2$ | $\mathbb{R}^d$ | $d(d+1)/2$ | $O(d)$ |
| Polynomial (degree $p$) | $\langle \mathbf{x}, \mathbf{x}' \rangle^p$ | $\mathbb{R}^d$ | $\binom{d+p-1}{p}$ | $O(d)$ |
| RBF (Gaussian) | $\exp(-\gamma \|\mathbf{x} - \mathbf{x}'\|^2)$ | $\mathbb{R}^d$ | infinite | $O(d)$ |
| String sub-sequence | dynamic programming | strings | $|\Sigma|^q$ | $O(q|s||t|)$ |

(source: ml-fundamentals/ls26_lecture_svm.pdf)

## Homogeneous Polynomial Kernel (Degree 2)

The feature map $\phi: \mathbb{R}^d \to \mathbb{R}^{d(d+1)/2}$ has entries (source: ml-fundamentals/ls26_seminar_kernels.pdf):

$$\phi(\mathbf{x}) = (x_1^2, \sqrt{2}x_1x_2, \sqrt{2}x_1x_3, \ldots, x_2^2, \sqrt{2}x_2x_3, \ldots, x_d^2)^\top$$

**Proof that $k(\mathbf{x}, \mathbf{x}') = \langle \mathbf{x}, \mathbf{x}' \rangle^2$**:

$$\langle \mathbf{x}, \mathbf{x}' \rangle^2 = \left(\sum_i x_i x_i'\right)^2 = \sum_i (x_i x_i')^2 + 2\sum_{i<j}(x_i x_j)(x_i' x_j')$$

This matches $\langle \phi(\mathbf{x}), \phi(\mathbf{x}') \rangle$ after accounting for the $\sqrt{2}$ factors on cross terms.

## The Kernel Trick

Any algorithm that depends on inputs only through dot products $\langle \mathbf{x}_i, \mathbf{x}_j\rangle$ can be "kernelized" by replacing $\langle \mathbf{x}_i, \mathbf{x}_j\rangle$ with $k(\mathbf{x}_i, \mathbf{x}_j)$. This implicitly operates in the feature space without computing $\phi(\mathbf{x})$.

The representer theorem explains why this is enough for many regularized linear methods: an optimal solution can be chosen in the span of the embedded training points, $\mathbf{w}=\sum_i \alpha_i\phi(\mathbf{x}_i)$, so both training objectives and predictions can be written using only kernel evaluations (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

### Kernelized algorithms (covered in this course):

- **[[ml-fundamentals/svm#kernel-svm|Kernel SVM]]**: replace $\langle \mathbf{x}_i, \mathbf{x}_j\rangle$ in the dual with $k(\mathbf{x}_i, \mathbf{x}_j)$
- **Kernelized linear regression**: express w = sum c_i x_i, then h(x) = sum c_i k(x_i, x) + b (source: ml-fundamentals/ls26_seminar_kernels.pdf)

## Kernelized Linear Regression

The linear regression predictor $h(\mathbf{x}) = \mathbf{w}^\top \mathbf{x} + b$ can be rewritten using $\mathbf{w} = \mathbf{X}^\top \mathbf{c}$ for some coefficients $\mathbf{c}$, so that (source: ml-fundamentals/ls26_seminar_kernels.pdf):

$$h(\mathbf{x}) = \sum_i c_i \langle \mathbf{x}_i, \mathbf{x}\rangle + b$$

Replacing dot products with kernels:

$$h(\mathbf{x}) = \sum_i c_i k(\mathbf{x}_i, \mathbf{x}) + b$$

This enables nonlinear regression in high-dimensional feature spaces with the same closed-form solution structure.

## Related pages

- [[ml-fundamentals/understanding-machine-learning]]
- [[ml-fundamentals/svm]]
- [[ml-fundamentals/linear-classifiers]]
- [[pattern-recognition/svm]]
- [[pattern-recognition/perceptron]]
- [[pattern-recognition/pca]]
