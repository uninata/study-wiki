# Support Vector Machines

**Summary**: Maximum-margin linear classifier with kernel extensions for nonlinear classification. Covers primal/dual formulations, KKT conditions, soft margin with slack variables, kernel SVM, multi-class SVM, structured SVM, and SVM regression (SVR).

**Course**: pattern-recognition

**Sources**: pr_07_svm_2018.pdf

**Last updated**: 2026-05-03

---

## Hard Margin SVM

Find the hyperplane that maximizes the margin between two classes (source: pattern-recognition/pr_07_svm_2018.pdf):

**Primal**: minimize $\frac{1}{2}\|\mathbf{w}\|^2$ subject to $y_i(\mathbf{w}^\top \mathbf{x}_i + b) \geq 1$ for all $i$

The margin is $\frac{2}{\|\mathbf{w}\|}$. Only points with $y_i(\mathbf{w}^\top \mathbf{x}_i + b) = 1$ (on the margin boundary) affect the solution — these are **support vectors**.

## Dual Formulation

Using Lagrange multipliers $\alpha_i \geq 0$, the dual problem is (source: pattern-recognition/pr_07_svm_2018.pdf):

$$\max \sum_i \alpha_i - \frac{1}{2} \sum_{i,j} \alpha_i \alpha_j y_i y_j \mathbf{x}_i^\top \mathbf{x}_j$$

subject to $\alpha_i \geq 0$ and $\sum_i \alpha_i y_i = 0$.

The solution: $\mathbf{w} = \sum_i \alpha_i y_i \mathbf{x}_i$ (only support vectors have $\alpha_i > 0$).

### KKT Conditions
For each training point (source: pattern-recognition/pr_07_svm_2018.pdf):
- $\alpha_i = 0 \Rightarrow y_i(\mathbf{w}^\top \mathbf{x}_i + b) \geq 1$ (correctly classified, not on margin)
- $\alpha_i > 0 \Rightarrow y_i(\mathbf{w}^\top \mathbf{x}_i + b) = 1$ (on the margin — support vector)

## Soft Margin SVM

When data is not linearly separable, introduce **slack variables** $\xi_i \geq 0$ (source: pattern-recognition/pr_07_svm_2018.pdf):

$$\min \frac{1}{2}\|\mathbf{w}\|^2 + C \sum_i \xi_i$$

subject to $y_i(\mathbf{w}^\top \mathbf{x}_i + b) \geq 1 - \xi_i$.

- $C$ controls the trade-off between margin width and misclassification
- Large $C$: narrow margin, few violations (closer to hard margin)
- Small $C$: wide margin, more violations allowed
- Equivalent to minimizing **hinge loss**: $\ell(y, f(\mathbf{x})) = \max(0, 1 - y \cdot f(\mathbf{x}))$

### Dual with Soft Margin
Same as hard margin dual, but with $0 \leq \alpha_i \leq C$. Points with $\alpha_i = C$ are margin violators.

## Kernel SVM

Since the dual depends on data only through dot products $\mathbf{x}_i^\top \mathbf{x}_j$, replace with a kernel (source: pattern-recognition/pr_07_svm_2018.pdf):

$$\max \sum_i \alpha_i - \frac{1}{2} \sum_{i,j} \alpha_i \alpha_j y_i y_j k(\mathbf{x}_i, \mathbf{x}_j)$$

Decision function: $f(\mathbf{x}) = \sum_i \alpha_i y_i k(\mathbf{x}_i, \mathbf{x}) + b$

Common kernels: polynomial $k(\mathbf{x}, \mathbf{x}') = (\mathbf{x}^\top \mathbf{x}' + c)^p$, **RBF** $k(\mathbf{x}, \mathbf{x}') = \exp(-\gamma\|\mathbf{x} - \mathbf{x}'\|^2)$. See [[shared/kernel-functions]] for details.

The RBF kernel with small $\gamma$ gives a complex, highly nonlinear boundary; large $\gamma$ gives a smoother boundary.

## Multi-Class SVM

Approaches (source: pattern-recognition/pr_07_svm_2018.pdf):
- **One-vs-all**: $K$ binary SVMs, each separating one class from the rest. Predict: $\arg\max_k f_k(\mathbf{x})$
- **One-vs-one**: $K(K-1)/2$ binary SVMs, one for each pair. Predict by voting
- **Single optimization**: formulate as one large QP with constraints for all class pairs

## Structured SVM

Generalization where outputs are structured objects (sequences, trees, graphs) rather than class labels (source: pattern-recognition/pr_07_svm_2018.pdf):

$$\min \frac{1}{2}\|\mathbf{w}\|^2 + C \sum_i \xi_i$$

subject to $\mathbf{w}^\top [\Phi(\mathbf{x}_i, y_i) - \Phi(\mathbf{x}_i, y)] \geq \Delta(y_i, y) - \xi_i$ for all $i, y$

where $\Phi(\mathbf{x}, y)$ is a joint feature map and $\Delta(y_i, y)$ is a structured loss function.

## SVM Regression (SVR)

For regression, use an **epsilon-insensitive loss** — no penalty for errors smaller than $\epsilon$ (source: pattern-recognition/pr_07_svm_2018.pdf):

$$\min \frac{1}{2}\|\mathbf{w}\|^2 + C \sum_i (\xi_i + \xi_i^*)$$

subject to $|y_i - (\mathbf{w}^\top \mathbf{x}_i + b)| \leq \epsilon + \xi_i$ (or $\xi_i^*$).

The $\epsilon$-tube: points inside contribute no loss. Support vectors lie on or outside the tube.

## Related pages

- [[shared/svm]]
- [[pattern-recognition/perceptron]]
- [[pattern-recognition/logistic-regression]]
- [[shared/kernel-functions]]
- [[ml-fundamentals/svm]]
