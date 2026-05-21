# Support Vector Machines

**Summary**: SVMs find the maximum-margin linear classifier, implementing Structural Risk Minimization. The dual formulation enables the kernel trick for nonlinear boundaries. Key concepts: hard/soft margin, hinge loss, support vectors, complementary slackness.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_svm.pdf, ls26_seminar_kernels.pdf, understanding-machine-learning-theory-algorithms.pdf

**Last updated**: 2026-05-16

---

## Motivation: Why Maximum Margin?

For linearly separable data, infinitely many linear classifiers achieve zero training error. ERM alone cannot distinguish them. The **maximum margin classifier** selects the one with the largest distance to the nearest training point, which is theoretically justified by [[vc-dimension#structural-risk-minimization-srm|Structural Risk Minimization]] (source: ml-fundamentals/ls26_lecture_svm.pdf).

The textbook emphasizes that SVMs add a second inductive bias beyond zero training error: prefer separators that remain correct under small perturbations of the examples (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). This margin bias can lower sample complexity in high-dimensional feature spaces even when the VC dimension of all halfspaces is large (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Hard-Margin SVM

Assumes linearly separable data. The margin of classifier $(\mathbf{w}, b)$ is:

$$\gamma(\mathbf{w}, b) = \min_i \frac{y_i (\langle \mathbf{w}, \mathbf{x}_i \rangle + b)}{\|\mathbf{w}\|}$$

The maximum margin classifier solves the **Quadratic Program** (source: ml-fundamentals/ls26_lecture_svm.pdf):

$$(\mathbf{w}^*, b^*) = \arg\min_{\mathbf{w}, b} \frac{1}{2} \|\mathbf{w}\|^2 \quad \text{subject to } y_i(\langle \mathbf{w}, \mathbf{x}_i \rangle + b) \geq 1 \text{ for all } i$$

**Support vectors**: training examples on the margin boundary, i.e., $I_{SV} = \{i : y_i(\langle \mathbf{w}^*, \mathbf{x}_i \rangle + b^*) = 1\}$. Removing non-support-vectors does not change the solution.

## Soft-Margin SVM

For non-separable data, introduce **slack variables** $\xi_i \geq 0$ (source: ml-fundamentals/ls26_lecture_svm.pdf):

$$(\mathbf{w}^*, b^*, \boldsymbol{\xi}^*) = \arg\min \frac{1}{2} \|\mathbf{w}\|^2 + C \sum_i \xi_i$$

subject to:
- $\langle \mathbf{w}, \mathbf{x}_i \rangle + b \geq +1 - \xi_i$, for $i \in I_+$
- $\langle \mathbf{w}, \mathbf{x}_i \rangle + b \leq -1 + \xi_i$, for $i \in I_-$
- $\xi_i \geq 0$

**Regularization constant $C > 0$**: controls the trade-off between maximizing the margin (small $\|\mathbf{w}\|^2$) and minimizing training errors (small $\sum \xi_i$).

- $C \to \infty$: approaches hard-margin SVM
- $C \to 0$: large margin, many misclassifications allowed

### SVM as Regularized Risk Minimization

The soft-margin SVM is equivalent to (source: ml-fundamentals/ls26_lecture_svm.pdf):

$$(\mathbf{w}^*, b^*) = \arg\min \left[\lambda \, \Omega(\mathbf{w}, b) + R_{\text{hinge}}(T_m, \mathbf{w}, b)\right]$$

where:
- $\Omega(\mathbf{w}, b) = \frac{1}{2}\|\mathbf{w}\|^2$ is the **regularization term**
- $R_{\text{hinge}} = \frac{1}{m} \sum_i \max\{0,\; 1 - y_i(\langle \mathbf{w}, \mathbf{x}_i \rangle + b)\}$ is the empirical risk under **hinge loss**
- $\lambda = \frac{1}{2mC}$ is the regularization parameter

The hinge loss $\psi(y, t) = \max\{0, 1 - yt\}$ is a **convex upper bound** on the 0/1 loss: $\mathbb{1}[y \neq \text{sign}(t)] \leq \max\{0, 1 - yt\}$.

This regularized formulation is not just an optimization trick: it is a way to trade empirical hinge loss against the norm of $\mathbf{w}$, so the learned classifier is biased toward low-norm, large-margin separators (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). In the homogeneous case with $\|\mathbf{x}\|\leq \rho$, textbook bounds depend on quantities such as $\rho^2\|\mathbf{w}\|^2/m$ rather than directly on the ambient dimension (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Dual Formulation

The Lagrangian dual of the soft-margin SVM (source: ml-fundamentals/ls26_lecture_svm.pdf):

$$\boldsymbol{\alpha}^* = \arg\max \left[\sum_i \alpha_i - \frac{1}{2} \sum_{i,j} \alpha_i \alpha_j y_i y_j \langle \mathbf{x}_i, \mathbf{x}_j \rangle\right]$$

subject to: $\sum_i \alpha_i y_i = 0$ and $0 \leq \alpha_i \leq C$

### Primal-dual relationship

- $\mathbf{w}^* = \sum_i \alpha_i^* y_i \mathbf{x}_i$ (w is a linear combination of training points)
- $b^* = y_i - \langle \mathbf{w}^*, \mathbf{x}_i \rangle$ for any $i$ with $\alpha_i^* \in (0, C)$

### Complementary slackness (classifying training points)

| Condition | Meaning |
|-----------|---------|
| $\alpha_i = 0$ | Point outside margin ($\xi_i = 0$, correctly classified) |
| $0 < \alpha_i < C$ | Point exactly on margin ($\xi_i = 0$, support vector) |
| $\alpha_i = C$ | Point inside margin or misclassified ($\xi_i > 0$) |

## Kernel SVM

The dual depends only on **dot products** $\langle \mathbf{x}_i, \mathbf{x}_j \rangle$. Replacing these with a kernel function $k(\mathbf{x}_i, \mathbf{x}_j) = \langle \phi(\mathbf{x}_i), \phi(\mathbf{x}_j) \rangle$ implicitly maps data to a (possibly infinite-dimensional) feature space (source: ml-fundamentals/ls26_lecture_svm.pdf).

**Kernelized dual**:

$$\boldsymbol{\alpha}^* = \arg\max \left[\sum_i \alpha_i - \frac{1}{2} \sum_{i,j} \alpha_i \alpha_j y_i y_j \, k(\mathbf{x}_i, \mathbf{x}_j)\right]$$

**Kernelized prediction**:

$$h(\mathbf{x}) = \text{sign}\!\left(\sum_{i \in I_{SV}} \alpha_i^* y_i \, k(\mathbf{x}_i, \mathbf{x}) + b^*\right)$$

Only support vectors ($\alpha_i > 0$) need to be stored. See [[shared/kernel-functions]] for kernel details.

The kernel method starts by choosing a feature map $\psi:\mathcal{X}\to\mathcal{F}$ and learning a linear separator in $\mathcal{F}$; the resulting classifier can be highly nonlinear in the original input space (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). A kernel $k(\mathbf{x},\mathbf{x}')=\langle\psi(\mathbf{x}),\psi(\mathbf{x}')\rangle$ lets the algorithm work through inner products without explicitly materializing the feature vectors (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

The representer theorem explains why this is enough: for regularized objectives depending on the training examples only through $\langle \mathbf{w},\psi(\mathbf{x}_i)\rangle$, there is an optimal solution of the form $\mathbf{w}=\sum_i \alpha_i\psi(\mathbf{x}_i)$ (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). This reduces prediction to kernel evaluations against training points.

### Common kernels

| Kernel | $k(\mathbf{x}, \mathbf{x}')$ | Feature space dim | Eval cost |
|--------|----------|-------------------|-----------|
| 2nd polynomial | $\langle \mathbf{x}, \mathbf{x}' \rangle^2$ | $d(d+1)/2$ | $O(d)$ |
| Degree $p$ polynomial | $\langle \mathbf{x}, \mathbf{x}' \rangle^p$ | $\binom{n+p-1}{p}$ | $O(d)$ |
| RBF (Gaussian) | $\exp(-\gamma \|\mathbf{x} - \mathbf{x}'\|^2)$ | $\infty$ | $O(d)$ |

## Linear vs. Kernel SVM: Complexity

| | Training memory | Prediction | Training time | When efficient? |
|---|---|---|---|---|
| Linear SVM | $O(m \cdot n)$ | $O(n)$ | $O(n \cdot m)$ to $O(n \cdot m^2)$ | Large $m$ |
| Kernel SVM | $O(m^2)$ | $O(|I_{SV}|)$ | $O(m^2)$ to $O(m^3)$ | High-dim features |

(source: ml-fundamentals/ls26_lecture_svm.pdf)

## Seminar: Homogeneous Polynomial Kernel

The feature map $\phi(\mathbf{x}) = (x_1^2, \sqrt{2}\, x_1 x_2, \ldots, x_d^2)^\top$ defines the homogeneous polynomial kernel of degree 2 (source: ml-fundamentals/ls26_seminar_kernels.pdf):

$$k(\mathbf{x}, \mathbf{x}') = \langle \phi(\mathbf{x}), \phi(\mathbf{x}') \rangle = \langle \mathbf{x}, \mathbf{x}' \rangle^2$$

Proof: expand $\langle \mathbf{x}, \mathbf{x}' \rangle^2 = \left(\sum_i x_i x_i'\right)^2 = \sum_i x_i^2 x_i'^2 + 2 \sum_{i<j} x_i x_j x_i' x_j'$, which matches $\langle \phi(\mathbf{x}), \phi(\mathbf{x}') \rangle$.

## Seminar: Kernelized Linear Regression

Linear regression can be expressed solely in terms of dot products (source: ml-fundamentals/ls26_seminar_kernels.pdf):

The predictor $h(\mathbf{x}) = \langle \mathbf{w}, \mathbf{x} \rangle + b$ with $\mathbf{w} = \sum_i c_i \mathbf{x}_i$ can be written as $h(\mathbf{x}) = \sum_i c_i \langle \mathbf{x}_i, \mathbf{x} \rangle + b$.

Replacing $\langle \mathbf{x}_i, \mathbf{x} \rangle$ with $k(\mathbf{x}_i, \mathbf{x})$ gives **kernelized linear regression**, enabling nonlinear regression without explicitly computing features.

## Connection to Theory

- SVM implements [[vc-dimension#structural-risk-minimization-srm|SRM]]: the constraint $\|\mathbf{w}\| \leq W$ defines nested hypothesis classes $\mathcal{H}_1 \subset \mathcal{H}_2 \subset \ldots$ with increasing VCdim. The parameter $C$ controls which class is selected (source: ml-fundamentals/ls26_lecture_svm.pdf).
- Generalization bound: $R(p, h) \leq R_{\text{hinge}}(T_m, h) + \epsilon_{SVM}(W, 1/m, 1/\delta)$

## Related pages

- [[ml-fundamentals/understanding-machine-learning]]
- [[shared/svm]]
- [[shared/kernel-functions]]
- [[vc-dimension]]
- [[empirical-risk-minimization]]
- [[linear-classifiers]]
