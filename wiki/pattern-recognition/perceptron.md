# Perceptron

**Summary**: The perceptron is a linear classifier trained by iteratively correcting misclassifications. Covers the convergence theorem, margin bound, multi-class and kernel extensions, voted/averaged perceptron, and the Kozinec algorithm.

**Course**: pattern-recognition

**Sources**: pr_06_perceptron_2022.pdf

**Last updated**: 2026-05-03

---

## Perceptron Algorithm

For linearly separable binary data, the perceptron iteratively updates the weight vector (source: pattern-recognition/pr_06_perceptron_2022.pdf):

1. Initialize $\mathbf{w}^{(0)}$ (e.g., to zero or a random vector)
2. Find a misclassified point: $\mathbf{x}_j$ such that $y_j (\mathbf{w}^\top \mathbf{x}_j) \leq 0$
3. Update: $\mathbf{w}^{(t+1)} = \mathbf{w}^{(t)} + y_j \mathbf{x}_j$
4. Repeat until no misclassifications

The algorithm adds the misclassified point (with sign) to the weight vector, rotating the decision boundary toward the correct side.

## Convergence Theorem (Novikoff)

If the data is linearly separable with margin $\gamma$ (minimum distance from any point to the hyperplane), the perceptron converges in at most (source: pattern-recognition/pr_06_perceptron_2022.pdf):

$$t \leq \left(\frac{R}{\gamma}\right)^2$$

mistakes, where $R = \max_i \|\mathbf{x}_i\|$ is the radius of the data. This is independent of the number of training points and the dimensionality.

## Margin and Separating Hyperplane

The **margin** $\gamma = \min_i y_i (\mathbf{w}^{*\top} \mathbf{x}_i) / \|\mathbf{w}^*\|$ measures how well-separated the data is. Larger margin means (source: pattern-recognition/pr_06_perceptron_2022.pdf):
- Fewer perceptron iterations needed
- Better generalization (connects to [[pattern-recognition/svm]])

## Optimal Separating Plane and Convex Hull

Finding the maximum-margin separating hyperplane is equivalent to finding the **closest point to the origin in the convex hull** of the (signed) data points (source: pattern-recognition/pr_06_perceptron_2022.pdf):

- $\mathbf{x}^* = \arg\min_{\mathbf{x} \in \text{conv}(X)} \|\mathbf{x}\|$
- The optimal $\mathbf{w}^*$ is proportional to $\mathbf{x}^*$
- The margin $m = \|\mathbf{x}^*\|$

## Kozinec Algorithm (1973)

An alternative to the standard perceptron that directly seeks the closest point in the convex hull (source: pattern-recognition/pr_06_perceptron_2022.pdf):

1. Initialize $\mathbf{w}^{(0)} = \mathbf{x}_j$ (any observation)
2. Find a wrongly classified observation $\mathbf{x}_j$: $\mathbf{w}^{(t)} \cdot \mathbf{x}_j \leq 0$
3. Update: $\mathbf{w}^{(t+1)} = (1 - \kappa^*) \mathbf{w}^{(t)} + \kappa^* \mathbf{x}_j$ where $\kappa^*$ minimizes $\|(1-\kappa) \mathbf{w}^{(t)} + \kappa \mathbf{x}_j\|$
4. Repeat

The **epsilon-solution** variant stops when $\|\mathbf{w}^{(t)}\| - \min_j (\mathbf{w}^{(t)}/\|\mathbf{w}^{(t)}\| \cdot \mathbf{x}_j) \leq \epsilon$, trading off precision for speed.

## Multi-Class Perceptron

Maintain weight vectors $\mathbf{w}_1, \ldots, \mathbf{w}_K$ for $K$ classes. Predict class $\arg\max_k \mathbf{w}_k^\top \mathbf{x}$. Update rule for a mistake (predicted class $j$ instead of true class $k$) (source: pattern-recognition/pr_06_perceptron_2022.pdf):

- $\mathbf{w}_k \leftarrow \mathbf{w}_k + \mathbf{x}$ (strengthen correct class)
- $\mathbf{w}_j \leftarrow \mathbf{w}_j - \mathbf{x}$ (weaken incorrect class)

## Kernel Perceptron

Since $\mathbf{w} = \sum_i \alpha_i \mathbf{x}_i$ ($\mathbf{w}$ is a linear combination of data points), the decision function can be written as (source: pattern-recognition/pr_06_perceptron_2022.pdf):

$$f(\mathbf{x}) = \sum_i \alpha_i k(\mathbf{x}_i, \mathbf{x})$$

where $k$ is a kernel function. This enables nonlinear classification without explicitly computing feature maps. See [[shared/kernel-functions]].

## Voted and Averaged Perceptron

To improve generalization (source: pattern-recognition/pr_06_perceptron_2022.pdf):

- **Voted perceptron**: keep all intermediate weight vectors, each votes with weight proportional to how many examples it classified correctly
- **Averaged perceptron**: use the average of all weight vectors seen during training — simpler and works well in practice

## Connection to Other Linear Classifiers

| Method | Objective | Boundary | Convergence |
|--------|-----------|----------|-------------|
| Perceptron | Zero misclassifications | Any separating hyperplane | Finite (if separable) |
| [[pattern-recognition/svm\|SVM]] | Maximum margin | Unique optimal hyperplane | QP solution |
| [[pattern-recognition/logistic-regression\|Logistic Regression]] | Cross-entropy | Probabilistic boundary | Gradient descent |

## Related pages

- [[pattern-recognition/svm]]
- [[pattern-recognition/logistic-regression]]
- [[pattern-recognition/neural-networks]]
- [[shared/kernel-functions]]
- [[ml-fundamentals/linear-classifiers]]
