# Support Vector Machines (SVM)

**Summary**: Maximum-margin classifiers in linear and non-linear (kernel) spaces. Covers hard and soft margin formulations, the kernel trick, multiclass extensions, and support vector regression (SVR).

**Course**: ml-methods

**Sources**: mlm_week_4.pdf

**Last updated**: 2026-04-16

---

## Hard-Margin SVM

Find the maximum-margin separating hyperplane for linearly separable data (source: mlm_week_4.pdf):

$$\min_{w, b} \frac{1}{2} \|w\|^2 \quad \text{s.t.} \quad y_i (w^T x_i + b) \geq 1 \text{ for all } i$$

The margin (distance between decision boundary and nearest data point) is $\frac{1}{\|w\|}$.

### Dual Formulation

Convert to a dual problem over Lagrange multipliers $\alpha_i \geq 0$:

$$\max_{\alpha} \sum_i \alpha_i - \frac{1}{2} \sum_{i,j} \alpha_i \alpha_j y_i y_j x_i^T x_j \quad \text{s.t.} \quad \sum_i \alpha_i y_i = 0, \, \alpha_i \geq 0$$

Decision function: $f(x) = \text{sign}\left( \sum_i \alpha_i^* y_i x_i^T x + b \right)$

**Support vectors**: Only points with $\alpha_i > 0$ (typically a small subset) determine the boundary.

## Soft-Margin SVM

Allow slack variables for misclassification on non-separable data (source: mlm_week_4.pdf):

$$\min_{w, b, \xi} \frac{1}{2} \|w\|^2 + C \sum_i \xi_i \quad \text{s.t.} \quad y_i (w^T x_i + b) \geq 1 - \xi_i, \, \xi_i \geq 0$$

- $\xi_i$ = slack for point $i$ (0 if correctly classified)
- $C$ = regularization parameter balancing margin vs. training error
- Dual form analogous to hard-margin (just adds upper bound $\alpha_i \leq C$)

## The Kernel Trick

Apply SVM to non-linearly separable data by implicitly working in a high-dimensional feature space via kernel function $K(x_i, x_j) = \phi(x_i)^T \phi(x_j)$ (source: mlm_week_4.pdf).

### Common Kernels

- **Linear**: $K(x, y) = x^T y$
- **Polynomial**: $K(x, y) = (x^T y + c)^d$
- **RBF (Gaussian)**: $K(x, y) = \exp\left( -\gamma \|x - y\|^2 \right)$

Replace every inner product $x_i^T x_j$ with $K(x_i, x_j)$ in the SVM dual.

## Multiclass SVM

Extend binary SVM to $c$ classes (source: mlm_week_4.pdf):

- **One-vs-Rest**: Train $c$ binary classifiers; predict with highest score
- **One-vs-One**: Train $\binom{c}{2}$ pairwise classifiers; predict by majority vote
- **Cramer-Singer**: Directly optimize single $c$-class margin (more complex but principled)

## Support Vector Regression (SVR)

Regression analog using $\epsilon$-insensitive loss (source: mlm_week_4.pdf):

$$L_\epsilon(y, f(x)) = \max(0, |y - f(x)| - \epsilon)$$

Penalize errors larger than $\epsilon$; allow up to $\epsilon$ error for free. Dual formulation similar to soft-margin SVM but with different Lagrange constraints.

---

## Related Pages

- [[ml-methods/course-overview]]
- [[ml-methods/supervised-learning-recap]]
- [[ml-methods/decision-trees-ensembles]]
- [[shared/svm]]
- [[shared/kernel-functions]]
