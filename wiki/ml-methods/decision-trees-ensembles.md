# Decision Trees and Ensemble Methods

**Summary**: Hierarchical decision-making via binary splits; ensemble techniques (bagging, boosting, stacking) that combine weak learners into strong predictors. Covers CART, random forests, AdaBoost, gradient boosting (XGBoost), and stacking.

**Course**: ml-methods

**Sources**: mlm_week_2.pdf, mlm_week_3.pdf

**Last updated**: 2026-04-16

---

## Decision Trees (CART)

**Classification and Regression Trees (CART)**: Build a binary tree by recursively splitting features to minimize impurity (source: mlm_week_2.pdf).

### Splitting Criteria

- **Gini impurity**: $\text{Gini}(S) = 1 - \sum_c p_c^2$ where $p_c$ is the fraction of class c in set S
- **Entropy**: $H(S) = -\sum_c p_c \log p_c$
- **Information gain**: $\text{Gain} = H(S) - \frac{|S_L|}{|S|} H(S_L) - \frac{|S_R|}{|S|} H(S_R)$

At each node, choose the feature and threshold that maximize information gain.

### Pruning

Remove splits that don't improve validation error; prevents overfitting to training data (source: mlm_week_2.pdf).

## Bagging (Bootstrap Aggregating)

Reduce variance by training multiple models on bootstrap samples and averaging predictions (source: mlm_week_2.pdf):

1. For $b = 1, \ldots, B$: Sample n instances with replacement; train tree $h_b$
2. Predict: $\hat{y} = \frac{1}{B} \sum_b h_b(x)$

**Out-of-Bag (OOB) Error**: Each bootstrap omits ~37% of instances; evaluate $h_b$ on its OOB set to estimate generalization without a separate test set.

## Random Forests

Bagging + random feature subsets: At each node, only consider $\sqrt{d}$ (or $\log d$) random features (source: mlm_week_2.pdf):

- **Decorrelates trees**: Different feature subsets → diverse trees → lower variance
- **Feature importance**: Count how often each feature is used for splitting; weight by information gain
- State-of-the-art baseline for tabular data

## Boosting

Sequentially train weak learners on misclassified examples; combine them into a strong predictor (source: mlm_week_3.pdf).

### AdaBoost
$$\hat{y} = \text{sign}\left( \sum_{t=1}^T \alpha_t h_t(x) \right)$$

1. Initialize uniform sample weights $w_i = \frac{1}{n}$
2. For $t = 1, \ldots, T$:
   - Train weak learner $h_t$ on weighted data
   - Compute error: $\epsilon_t = \sum_i w_i \mathbb{1}[h_t(x_i) \neq y_i]$
   - Set $\alpha_t = \frac{1}{2} \log \frac{1 - \epsilon_t}{\epsilon_t}$
   - Increase weights on misclassified examples: $w_i \leftarrow w_i e^{-\alpha_t y_i h_t(x_i)}$
   - Renormalize weights

**Margin theory**: Boosting maximizes the margin (distance from decision boundary); larger margins → better generalization.

### Gradient Boosting

Minimize loss iteratively by fitting residuals (source: mlm_week_3.pdf):

$$h_m(x) = h_{m-1}(x) + \eta \cdot g_m(x)$$

where $g_m$ is a tree fit to the negative gradient of the loss at each training point.

- More flexible than AdaBoost; works with any differentiable loss
- Slower training (sequential); requires careful tuning of learning rate $\eta$ and tree depth

### XGBoost

Production-grade gradient boosting (source: mlm_week_3.pdf):

- **Regularization**: Penalize tree complexity (number of leaves, leaf weights)
- **Handling missing data**: Learn split directions for missing values
- **Approximate splits**: Bin features for faster training on large datasets
- **Parallel tree building**: Batch feature scans across CPUs
- State-of-the-art for structured data competitions and real-world tabular problems

## Stacking

Meta-learning: Train a second-level model on predictions of first-level models (source: mlm_week_3.pdf):

1. Partition training data into k folds
2. For each fold: Train base models on k−1 folds; predict on held-out fold
3. Concatenate all predictions → new feature matrix
4. Train meta-model (e.g., logistic regression) on new features
5. At test time: Predict with base models on test set; feed predictions to meta-model

Often outperforms single models if base models are diverse and meta-model is simple.

---

## Related Pages

- [[ml-methods/course-overview]]
- [[ml-methods/supervised-learning-recap]]
- [[ml-methods/svm]]
- [[ml-methods/reinforcement-learning]]
- [[pattern-recognition/decision-trees]] — CART, Gini/entropy, random forests, feature importance
