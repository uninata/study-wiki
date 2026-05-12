# AdaBoost

**Summary**: Ensemble method that combines many weak learners into a strong classifier by iteratively reweighting training examples. Covers the AdaBoost algorithm, exponential loss interpretation, training error bounds, margin theory, and variants (Real AdaBoost, LogitBoost).

**Course**: pattern-recognition

**Sources**: pr_08_adaboost_2017.pdf

**Last updated**: 2026-04-15

---

## Boosting Framework

The central idea: if we can find classifiers slightly better than random guessing (**weak learners**), we can combine them into an arbitrarily accurate classifier (source: pattern-recognition/pr_08_adaboost_2017.pdf).

## AdaBoost Algorithm

For binary classification with labels y_i in {-1, +1} (source: pattern-recognition/pr_08_adaboost_2017.pdf):

1. Initialize weights: D_1(i) = 1/n for all training examples
2. For t = 1, ..., T:
   - Train weak learner h_t on data weighted by D_t
   - Compute weighted error: **epsilon_t = sum_{i: h_t(x_i) != y_i} D_t(i)**
   - Compute learner weight: **alpha_t = (1/2) ln((1 - epsilon_t) / epsilon_t)**
   - Update weights: **D_{t+1}(i) = D_t(i) exp(-alpha_t y_i h_t(x_i)) / Z_t**
   - Z_t is the normalization constant
3. Final classifier: **H(x) = sign(sum_t alpha_t h_t(x))**

### Key Properties
- Misclassified points get **higher** weight in the next round
- Correctly classified points get **lower** weight
- Weak learners with lower error get **higher** alpha_t
- If epsilon_t = 0.5 (random guessing), alpha_t = 0 (learner is ignored)
- If epsilon_t > 0.5, alpha_t < 0 (learner is flipped)

## Exponential Loss Interpretation

AdaBoost minimizes the **exponential loss** (source: pattern-recognition/pr_08_adaboost_2017.pdf):

**L = sum_i exp(-y_i F(x_i))** where F(x) = sum_t alpha_t h_t(x)

Each boosting iteration performs coordinate descent in function space, greedily adding the weak learner that most reduces the exponential loss.

## Training Error Bound

The training error of AdaBoost satisfies (source: pattern-recognition/pr_08_adaboost_2017.pdf):

**err_train <= prod_t 2 sqrt(epsilon_t (1 - epsilon_t)) = prod_t sqrt(1 - 4 gamma_t^2)**

where gamma_t = 1/2 - epsilon_t is the "edge" of weak learner t over random guessing. If each gamma_t >= gamma > 0, the training error decreases **exponentially** in T:

**err_train <= exp(-2 T gamma^2)**

## Margin Theory

The **margin** of example (x_i, y_i) is (source: pattern-recognition/pr_08_adaboost_2017.pdf):

**margin_i = y_i sum_t alpha_t h_t(x_i) / sum_t |alpha_t|**

This is in [-1, 1]. Positive margin means correct classification. AdaBoost tends to increase margins even after training error reaches zero, which explains its resistance to overfitting in practice.

The generalization error depends on the **margin distribution**, not just training error.

## Convergence Analysis

AdaBoost converges in the sense that (source: pattern-recognition/pr_08_adaboost_2017.pdf):
- Training error drops to zero quickly (exponentially if edges are bounded away from zero)
- The exponential loss continues to decrease even after perfect training accuracy
- The margin distribution keeps improving with more iterations

## Variants

### Real AdaBoost
Instead of binary h_t in {-1, +1}, allow real-valued confidence-rated predictions h_t(x) in R (source: pattern-recognition/pr_08_adaboost_2017.pdf). The weak learner outputs a log-odds estimate directly.

### LogitBoost
Replaces the exponential loss with **logistic loss** (source: pattern-recognition/pr_08_adaboost_2017.pdf):

**L = sum_i log(1 + exp(-2 y_i F(x_i)))**

This is more robust to outliers and noisy labels than exponential loss. Each iteration fits a weighted least-squares regression to pseudo-residuals.

## Connection to Other Methods

- AdaBoost with decision stumps is a common default — each weak learner splits on one feature
- [[pattern-recognition/decision-trees#random-forests]]: bagging + random feature selection (different ensemble strategy)
- Gradient boosting generalizes the boosting framework to arbitrary differentiable loss functions

## Related pages

- [[pattern-recognition/decision-trees]]
- [[pattern-recognition/logistic-regression]]
- [[pattern-recognition/bayesian-decision-theory]]
