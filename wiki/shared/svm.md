# Support Vector Machines

**Summary**: Maximum-margin linear classifiers with kernel extensions for nonlinear boundaries. The dual formulation depends only on dot products, enabling the kernel trick. Covers hard/soft margin, hinge loss, dual/KKT conditions, kernel SVM, multi-class, structured SVM, and SVR.

**Course**: ml-fundamentals, pattern-recognition, computer-vision

**Sources**: ls26_lecture_svm.pdf, ls26_seminar_kernels.pdf, pr_07_svm_2018.pdf

**Last updated**: 2026-05-03

---

## Motivation: Why Maximum Margin?

For linearly separable data, infinitely many linear classifiers achieve zero training error. The **maximum margin classifier** selects the one with the largest distance to the nearest training point, justified by [[ml-fundamentals/vc-dimension#structural-risk-minimization-srm|Structural Risk Minimization]]: constraining $\|\mathbf{w}\|$ controls model complexity (source: ml-fundamentals/ls26_lecture_svm.pdf).

## Hard-Margin SVM

Assumes linearly separable data. The margin of classifier $(\mathbf{w}, b)$ is:

$$\gamma(\mathbf{w}, b) = \min_i \frac{y_i (\mathbf{w}^\top \mathbf{x}_i + b)}{\|\mathbf{w}\|}$$

The maximum margin classifier solves (source: ml-fundamentals/ls26_lecture_svm.pdf):

$$\min_{\mathbf{w}, b} \frac{1}{2}\|\mathbf{w}\|^2 \quad \text{s.t.} \quad y_i(\mathbf{w}^\top \mathbf{x}_i + b) \geq 1 \;\; \forall\, i$$

The margin width is $2/\|\mathbf{w}\|$. **Support vectors** are points on the margin boundary: $y_i(\mathbf{w}^\top \mathbf{x}_i + b) = 1$. Removing non-support-vectors does not change the solution.

## Soft-Margin SVM

For non-separable data, introduce **slack variables** $\xi_i \geq 0$ (source: ml-fundamentals/ls26_lecture_svm.pdf, pattern-recognition/pr_07_svm_2018.pdf):

$$\min_{\mathbf{w}, b, \boldsymbol{\xi}} \frac{1}{2}\|\mathbf{w}\|^2 + C \sum_i \xi_i$$

$$\text{s.t.} \quad y_i(\mathbf{w}^\top \mathbf{x}_i + b) \geq 1 - \xi_i, \quad \xi_i \geq 0$$

**$C$ controls the margin-violation trade-off**:
- $C \to \infty$: approaches hard margin
- $C \to 0$: wide margin, many violations allowed

### SVM as Regularized Hinge Loss

Equivalent to (source: ml-fundamentals/ls26_lecture_svm.pdf):

$$(\mathbf{w}^*, b^*) = \arg\min_{\mathbf{w}, b} \left[\lambda \cdot \frac{1}{2}\|\mathbf{w}\|^2 + \frac{1}{m} \sum_i \max\{0,\, 1 - y_i(\mathbf{w}^\top \mathbf{x}_i + b)\}\right]$$

The **hinge loss** $\max\{0, 1 - yt\}$ is a convex upper bound on 0/1 loss.

## Dual Formulation

Using Lagrange multipliers $\alpha_i$, the dual is (source: ml-fundamentals/ls26_lecture_svm.pdf, pattern-recognition/pr_07_svm_2018.pdf):

$$\max_{\boldsymbol{\alpha}} \sum_i \alpha_i - \frac{1}{2} \sum_{i,j} \alpha_i \alpha_j y_i y_j \mathbf{x}_i^\top \mathbf{x}_j$$

$$\text{s.t.} \quad \sum_i \alpha_i y_i = 0, \quad 0 \leq \alpha_i \leq C$$

### Primal-dual relationship

- $\mathbf{w}^* = \sum_i \alpha_i y_i \mathbf{x}_i$ (w is a linear combination of training points)
- $b^* = y_j - \mathbf{w}^{*\top} \mathbf{x}_j$ for any $j$ with $0 < \alpha_j < C$

### KKT / Complementary Slackness

| Condition | Meaning |
| --------------- | ----------------------------------------------------- |
| $\alpha_i = 0$ | Point outside margin, correctly classified ($\xi_i = 0$) |
| $0 < \alpha_i < C$ | Point exactly on margin — support vector ($\xi_i = 0$) |
| $\alpha_i = C$ | Point inside margin or misclassified ($\xi_i > 0$) |

(source: ml-fundamentals/ls26_lecture_svm.pdf, pattern-recognition/pr_07_svm_2018.pdf)

## Kernel SVM

The dual depends only on dot products $\mathbf{x}_i^\top \mathbf{x}_j$. Replacing with a kernel $k(\mathbf{x}_i, \mathbf{x}_j) = \boldsymbol{\phi}(\mathbf{x}_i)^\top \boldsymbol{\phi}(\mathbf{x}_j)$ implicitly operates in a high-dimensional feature space (source: ml-fundamentals/ls26_lecture_svm.pdf):

**Kernelized dual**:

$$\max_{\boldsymbol{\alpha}} \sum_i \alpha_i - \frac{1}{2} \sum_{i,j} \alpha_i \alpha_j y_i y_j k(\mathbf{x}_i, \mathbf{x}_j)$$

**Kernelized prediction**:

$$f(\mathbf{x}) = \sum_{i \in SV} \alpha_i y_i k(\mathbf{x}_i, \mathbf{x}) + b$$

Only support vectors need to be stored. See [[shared/kernel-functions]] for kernel details.

### Common Kernels

| Kernel | $k(\mathbf{x}, \mathbf{x}')$ | Feature dim |
|--------|----------|-------------|
| Polynomial (degree $p$) | $(\mathbf{x}^\top \mathbf{x}' + c)^p$ | $\binom{d+p-1}{p}$ |
| RBF (Gaussian) | $\exp(-\gamma \|\mathbf{x} - \mathbf{x}'\|^2)$ | $\infty$ |

RBF: small $\gamma$ → complex boundary; large $\gamma$ → smooth boundary (source: pattern-recognition/pr_07_svm_2018.pdf).

## Linear vs. Kernel SVM: Complexity

| | Training | Prediction | When efficient? |
|---|---|---|---|
| Linear SVM | $O(nm)$ to $O(nm^2)$ | $O(n)$ | Large $m$, moderate $n$ |
| Kernel SVM | $O(m^2)$ to $O(m^3)$ | $O(\lvert SV \rvert)$ | High-dim features |

(source: ml-fundamentals/ls26_lecture_svm.pdf)

## Multi-Class SVM

Approaches (source: pattern-recognition/pr_07_svm_2018.pdf):
- **One-vs-all**: $K$ binary SVMs, predict $\arg\max_k f_k(\mathbf{x})$
- **One-vs-one**: $K(K-1)/2$ pairwise SVMs, predict by voting
- **Single optimization**: one large QP with constraints for all class pairs

## Structured SVM

Outputs are structured objects (sequences, trees, graphs) rather than class labels (source: pattern-recognition/pr_07_svm_2018.pdf):

$$\min_{\mathbf{w}, \boldsymbol{\xi}} \frac{1}{2}\|\mathbf{w}\|^2 + C \sum_i \xi_i$$

$$\text{s.t.} \quad \mathbf{w}^\top [\Phi(\mathbf{x}_i, y_i) - \Phi(\mathbf{x}_i, y)] \geq \Delta(y_i, y) - \xi_i \quad \forall\, i, y$$

where $\Phi(\mathbf{x}, y)$ is a joint feature map and $\Delta(y_i, y)$ is a structured loss.

## SVM Regression (SVR)

Uses **$\epsilon$-insensitive loss** — no penalty for errors smaller than $\epsilon$ (source: pattern-recognition/pr_07_svm_2018.pdf):

$$\min_{\mathbf{w}, b, \boldsymbol{\xi}} \frac{1}{2}\|\mathbf{w}\|^2 + C \sum_i (\xi_i + \xi_i^*)$$

$$\text{s.t.} \quad |y_i - (\mathbf{w}^\top \mathbf{x}_i + b)| \leq \epsilon + \xi_i$$

The $\epsilon$-tube: points inside contribute no loss. Support vectors lie on or outside the tube.

## Connection to Theory

- SVM implements [[ml-fundamentals/vc-dimension#structural-risk-minimization-srm|SRM]]: the constraint $\|\mathbf{w}\| \leq W$ defines nested hypothesis classes with increasing VCdim. $C$ controls which class is selected (source: ml-fundamentals/ls26_lecture_svm.pdf).
- Generalization bound: $R(p, h) \leq R_{\text{hinge}}(T_m, h) + \epsilon_{\text{SVM}}(W, 1/m, 1/\delta)$

## Related pages

- [[shared/kernel-functions]]
- [[ml-fundamentals/svm]]
- [[ml-fundamentals/vc-dimension]]
- [[ml-fundamentals/empirical-risk-minimization]]
- [[ml-fundamentals/linear-classifiers]]
- [[pattern-recognition/svm]]
- [[pattern-recognition/perceptron]]
- [[pattern-recognition/logistic-regression]]
