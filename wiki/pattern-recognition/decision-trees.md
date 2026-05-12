# Decision Trees

**Summary**: Tree-structured classifiers that partition the feature space via a sequence of axis-aligned splits. Covers CART algorithm, splitting criteria (Gini, entropy, misclassification), pruning, regression trees, random forests, bagging, and feature importance.

**Course**: pattern-recognition

**Sources**: pr_13_dec_trees_2017_01_09.pdf

**Last updated**: 2026-04-15

---

## CART Algorithm

Classification and Regression Trees recursively split the data (source: pattern-recognition/pr_13_dec_trees_2017_01_09.pdf):

1. For each node, find the feature j and threshold t that best splits the data
2. Create two child nodes: left (x_j <= t) and right (x_j > t)
3. Assign each leaf the majority class (classification) or mean value (regression)
4. Repeat until a stopping criterion is met (max depth, min samples, purity)

Decision boundaries are **axis-aligned rectangles** — each split is perpendicular to one feature axis.

## Splitting Criteria

For a node with class proportions p_k (fraction of class k), the impurity measures are (source: pattern-recognition/pr_13_dec_trees_2017_01_09.pdf):

| Criterion | Formula | Properties |
|-----------|---------|------------|
| **Gini index** | sum_k p_k(1 - p_k) | Maximum at uniform distribution, zero when pure |
| **Entropy** | -sum_k p_k log(p_k) | Information-theoretic, slightly more balanced splits |
| **Misclassification error** | 1 - max_k p_k | Not differentiable, rarely used for growing |

The **information gain** for a split is: Delta = I(parent) - sum_children (n_child/n_parent) I(child).

Gini and entropy are preferred over misclassification error for tree growing because they are more sensitive to changes in class distributions.

## Pruning

Fully grown trees overfit. **Cost-complexity pruning** balances tree size against accuracy (source: pattern-recognition/pr_13_dec_trees_2017_01_09.pdf):

**R_alpha(T) = R(T) + alpha |T|**

where R(T) is the training error, |T| is the number of leaves, and alpha >= 0 is the complexity parameter. For each alpha, find the subtree that minimizes R_alpha. Cross-validation selects the optimal alpha.

## Regression Trees

For continuous targets, each leaf predicts the **mean** of its training points. Split quality is measured by reduction in **sum of squared errors** (source: pattern-recognition/pr_13_dec_trees_2017_01_09.pdf):

**SSE = sum_{x in node} (y - y_bar)^2**

## Random Forests

Ensemble of decision trees combining **bagging** and **random feature selection** (source: pattern-recognition/pr_13_dec_trees_2017_01_09.pdf):

1. For each tree t = 1, ..., T:
   - Draw a **bootstrap sample** (n samples with replacement)
   - At each split, consider only a **random subset** of sqrt(d) features (classification) or d/3 (regression)
   - Grow the tree fully (no pruning)
2. Predict by **majority vote** (classification) or **average** (regression)

### Key Properties
- Reduces variance compared to a single tree
- Decorrelation of trees via random feature selection improves ensemble diversity
- Resistant to overfitting — performance generally improves with more trees

### Out-of-Bag (OOB) Estimation
Each bootstrap sample leaves out ~37% of data. These **out-of-bag** samples provide a free estimate of generalization error without needing a separate validation set.

### Feature Importance
Measured by the total reduction in impurity across all splits using that feature, averaged over all trees. Alternatively, **permutation importance**: measure accuracy drop when a feature's values are randomly shuffled.

## Boosted Trees and Gradient Boosting

See [[pattern-recognition/adaboost]] for AdaBoost with decision stumps. **Gradient boosting** generalizes: each new tree fits the negative gradient (pseudo-residuals) of the loss function. Combined with shallow trees (depth 3-6), this is one of the most effective methods for tabular data.

## Related pages

- [[pattern-recognition/adaboost]]
- [[pattern-recognition/clustering]]
- [[pattern-recognition/bayesian-decision-theory]]
- [[ml-methods/decision-trees-ensembles]] — CART, random forests, XGBoost, stacking
