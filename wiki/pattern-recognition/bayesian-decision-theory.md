# Bayesian Decision Theory

**Summary**: Foundational framework for optimal classification under uncertainty. Covers Bayes decision rule, loss functions, discriminant functions, ROC curves, and Neyman-Pearson criterion for hypothesis testing.

**Course**: pattern-recognition

**Sources**: pr_01_intro_and_bayes_2021.pdf, pr_02_non_bayes_2023.pdf

**Last updated**: 2026-04-15

---

## Bayes Decision Rule

Given prior probabilities $p(k)$ for classes $k = 1, \ldots, K$ and class-conditional densities $p(\mathbf{x}|k)$, the **posterior** is (source: pattern-recognition/pr_01_intro_and_bayes_2021.pdf):

$$p(k|\mathbf{x}) = \frac{p(\mathbf{x}|k) p(k)}{p(\mathbf{x})}$$

The **Bayes decision rule** for a general loss function $\ell(k, d)$ assigns observation $\mathbf{x}$ to the decision $d$ that minimizes expected loss:

$$d^*(\mathbf{x}) = \arg\min_{d} \sum_{k} p(k|\mathbf{x}) \ell(k, d)$$

For the $0/1$ loss, this reduces to the MAP rule: $d^*(\mathbf{x}) = \arg\max_k p(k|\mathbf{x})$. See [[shared/bayes-classifier]] for the full loss-function table.

## Discriminant Functions

A classifier can be expressed via **discriminant functions** $g_k(\mathbf{x})$ for each class, assigning $\mathbf{x}$ to $\arg\max_k g_k(\mathbf{x})$. Common choices (source: pattern-recognition/pr_01_intro_and_bayes_2021.pdf):

- $g_k(\mathbf{x}) = p(k|\mathbf{x})$ — posterior probability
- $g_k(\mathbf{x}) = p(\mathbf{x}|k) p(k)$ — joint likelihood
- $g_k(\mathbf{x}) = \log p(\mathbf{x}|k) + \log p(k)$ — log posterior

For two-class problems, a single discriminant $g(\mathbf{x}) = g_1(\mathbf{x}) - g_2(\mathbf{x})$ suffices: classify as class 1 if $g(\mathbf{x}) > 0$.

## ROC Curves

For binary classification with a threshold on a score function, the **ROC curve** plots true positive rate (TPR) vs false positive rate (FPR) as the threshold varies (source: pattern-recognition/pr_01_intro_and_bayes_2021.pdf):

- **TPR** (sensitivity) $= \mathbb{P}(\text{decide 1} \mid \text{true class} = 1)$
- **FPR** (1 - specificity) $= \mathbb{P}(\text{decide 1} \mid \text{true class} = 0)$

The area under the ROC curve (AUC) summarizes classifier performance. A random classifier has AUC $= 0.5$.

## Neyman-Pearson Criterion

When the costs of false positives and false negatives differ fundamentally (e.g., medical testing), the **Neyman-Pearson** approach fixes the constraint (source: pattern-recognition/pr_02_non_bayes_2023.pdf):

$$\text{Maximize } \text{TPR} \text{ subject to } \text{FPR} \leq \alpha$$

This uses a likelihood ratio test with threshold chosen to satisfy the FPR constraint. Can involve **randomized strategies** when the constraint cannot be met with equality using a deterministic rule.

## Minimax Classifier

The **minimax** strategy minimizes the maximum possible risk across all prior distributions (source: pattern-recognition/pr_02_non_bayes_2023.pdf):

$$d_{\text{minimax}} = \arg\min_{d} \max_{p} R(p, d)$$

This is the game-theoretic solution: the classifier that performs best under the worst-case prior. The minimax solution corresponds to the point on the ROC curve where a specific tangent line touches.

## Wald Sequential Decision

The **sequential probability ratio test** (SPRT) processes observations one at a time, accumulating the log-likelihood ratio until it crosses an upper or lower threshold. This minimizes the expected number of observations needed for a given error rate (source: pattern-recognition/pr_02_non_bayes_2023.pdf).

## Related pages

- [[shared/bayes-classifier]]
- [[pattern-recognition/non-parametric-methods]]
- [[pattern-recognition/parameter-estimation]]
- [[pattern-recognition/logistic-regression]]
- [[ml-fundamentals/empirical-risk-minimization]]
