# Linear Classifiers

**Summary**: Practical linear classification methods: linear regression as a baseline, the perceptron algorithm with convergence guarantees, logistic regression via maximum likelihood, and multi-class extensions via the generic linear classifier framework.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_linear.pdf, understanding-machine-learning-theory-algorithms.pdf

**Last updated**: 2026-05-16

---

## Linear Regression (Baseline)

Linear predictor: $h(\mathbf{x}; \mathbf{w}, b) = \mathbf{w}^\top \mathbf{x} + b$

The textbook treats affine predictors as linear functions after augmenting each input with a constant coordinate $1$, so the bias $b$ can be folded into an expanded weight vector (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). Binary classifiers, regression predictors, and logistic regression all reuse this same score $\langle \mathbf{w},\mathbf{x}\rangle + b$ with different output maps and losses (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

**ERM with squared loss** gives a closed-form solution (source: ml-fundamentals/ls26_lecture_linear.pdf):

$$[\mathbf{w}_m, b_m] = (\mathbf{X}^\top \mathbf{X})^{-1} \mathbf{X}^\top \mathbf{y}$$

where $\mathbf{X} = [\mathbf{x}_1^\top\; 1;\; \ldots;\; \mathbf{x}_m^\top\; 1] \in \mathbb{R}^{m \times (d+1)}$.

**MLE derivation**: assuming $y = \mathbf{w}^\top \mathbf{x} + b + \epsilon$ with $\epsilon \sim \mathcal{N}(0, \sigma^2)$, maximizing the log-likelihood is equivalent to minimizing the sum of squared errors. The MLE and ERM solutions coincide.

**Plug-in Bayes predictor** for squared loss: $h(\mathbf{x}) = \mathbb{E}[y|\mathbf{x}] = \mathbf{w}_m^\top \mathbf{x} + b_m$ (the conditional mean).

## Binary Linear Classifier

$$h(\mathbf{x}; \mathbf{w}, b) = \text{sign}(\mathbf{w}^\top \mathbf{x} + b) = \begin{cases} +1 & \text{if } \mathbf{w}^\top \mathbf{x} + b \geq 0 \\ -1 & \text{otherwise} \end{cases}$$

Parameterized by weight vector $\mathbf{w} \in \mathbb{R}^d$ and bias $b \in \mathbb{R}$.

For halfspaces in $\mathbb{R}^d$, $\mathrm{VCdim} = d+1$, so plain ERM has sample complexity controlled by the input dimension (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). In the separable case, ERM can be implemented by finding any hyperplane satisfying $y_i(\langle \mathbf{w},\mathbf{x}_i\rangle+b)>0$; in the nonseparable case, exact 0/1-loss ERM is computationally hard, which motivates surrogate losses such as logistic and hinge loss (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Multiclass Linear Classifier

For $Y$ classes, predict the class with the highest score (source: ml-fundamentals/ls26_lecture_linear.pdf):

$$h(\mathbf{x}; \mathbf{W}, \mathbf{b}) = \arg\max_y (\mathbf{w}_y^\top \mathbf{x} + b_y)$$

Parameterized by weight matrix $\mathbf{W} = [\mathbf{w}_1, \ldots, \mathbf{w}_Y] \in \mathbb{R}^{d \times Y}$ and bias vector $\mathbf{b} \in \mathbb{R}^Y$.

## Generic Linear Classifier Framework

Both binary and multiclass classifiers are special cases of (source: ml-fundamentals/ls26_lecture_linear.pdf):

$$h(\mathbf{x}; \boldsymbol{\theta}) = \arg\max_{y \in \mathcal{Y}} \boldsymbol{\theta}^\top \phi(\mathbf{x}, y)$$

where $\phi: \mathcal{X} \times \mathcal{Y} \to \mathbb{R}^n$ is a **joint feature map** of input-label pairs.

- **Binary case**: $\phi(\mathbf{x}, y) = [y\mathbf{x},\; y]$, $\boldsymbol{\theta} = [\mathbf{w},\; b]$
- **Multiclass case**: $\phi(\mathbf{x}, y)$ places $[\mathbf{x}, 1]$ into the $y$-th slot and zeros elsewhere, $\boldsymbol{\theta} = [\mathbf{w}_1, b_1, \ldots, \mathbf{w}_Y, b_Y]$

A training set is **linearly separable** w.r.t. $\phi$ if there exists $\boldsymbol{\theta}$ such that $\boldsymbol{\theta}^\top \phi(\mathbf{x}_i, y_i) > \boldsymbol{\theta}^\top \phi(\mathbf{x}_i, y)$ for all $i$ and all $y \neq y_i$.

## Perceptron Algorithm

An online algorithm for finding a linear classifier with zero training error on separable data (source: ml-fundamentals/ls26_lecture_linear.pdf):

1. Initialize $\boldsymbol{\theta} \leftarrow \mathbf{0}$
2. Find a misclassified example $(\mathbf{x}_u, y_u)$ where $y_u \neq \arg\max_y \boldsymbol{\theta}^\top \phi(\mathbf{x}_u, y)$
3. If none exists, return $\boldsymbol{\theta}$. Otherwise update: $\boldsymbol{\theta} \leftarrow \boldsymbol{\theta} + \phi(\mathbf{x}_u, y_u) - \phi(\mathbf{x}_u, \hat{y}_u)$
4. Go to step 2

### Novikoff Theorem (Convergence Guarantee)

If the data is linearly separable, the perceptron terminates after at most (source: ml-fundamentals/ls26_lecture_linear.pdf):

$$\frac{R^2}{\gamma^2} \text{ updates}$$

where:
- $R = \max_i \|\mathbf{a}_i\|$ (maximum norm of the constraint vectors)
- $\gamma = \max_{\|\boldsymbol{\theta}\|=1} \min_i \boldsymbol{\theta}^\top \mathbf{a}_i$ (the margin of the best separator)

The number of iterations depends **only on $R$ and $\gamma$**, not on $m$ or $d$.

The textbook's perceptron analysis states the same margin dependence as $(R B)^2$, where $B$ is the norm of the smallest separator satisfying $y_i\langle \mathbf{w},\mathbf{x}_i\rangle \geq 1$; equivalently, this is controlled by the radius-to-margin ratio (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

### Exam relevance

The perceptron is important because: (1) it is the simplest online learning algorithm, (2) the Novikoff bound connects to margin theory and [[svm|SVMs]], (3) it only works for separable data — for non-separable data, use [[svm|soft-margin SVM]] or logistic regression.

## Logistic Regression

A discriminative probabilistic model for binary classification (source: ml-fundamentals/ls26_lecture_linear.pdf):

$$p(y \mid \mathbf{x}; \mathbf{w}, b) = \frac{1}{1 + \exp(-y(\mathbf{w}^\top \mathbf{x} + b))}$$

where $y \in \{-1, +1\}$. This is the **logistic (sigmoid) function** applied to the score $s(\mathbf{x}, y) = y(\mathbf{w}^\top \mathbf{x} + b)$.

### Learning via MLE

Maximize the conditional log-likelihood (source: ml-fundamentals/ls26_lecture_linear.pdf):

$$(\mathbf{w}_m, b_m) = \arg\max_{\mathbf{w}, b} \mathcal{L}(T_m, \mathbf{w}, b) = \arg\min_{\mathbf{w}, b} \sum_i \log\!\left(1 + \exp(-y_i(\mathbf{w}^\top \mathbf{x}_i + b))\right)$$

This is a **smooth convex** optimization problem (no closed-form solution; use gradient descent).

The loss function $\log(1 + \exp(-yt))$ is called the **logistic loss** (or cross-entropy loss). Like the hinge loss used in [[svm|SVMs]], it is a convex upper bound on the 0/1 loss.

Logistic regression can be read as a probabilistic halfspace: when $|\langle \mathbf{w},\mathbf{x}\rangle|$ is large it behaves like a confident hard classifier, while near the decision boundary it outputs probabilities close to $1/2$ (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

### Plug-in Bayes predictor

Under 0/1 loss, the MAP rule gives (source: ml-fundamentals/ls26_lecture_linear.pdf):

$$h(\mathbf{x}; \mathbf{w}_m, b_m) = \text{sign}(\mathbf{w}_m^\top \mathbf{x} + b_m)$$

The learned posterior $p(y=+1 \mid \mathbf{x})$ directly gives calibrated probability estimates, unlike SVMs.

## Comparison of Linear Methods

| Method | Loss | Separability required? | Output |
|--------|------|----------------------|--------|
| Perceptron | 0/1 | Yes | Hard labels |
| SVM (hard) | Hinge | Yes | Hard labels + margin |
| SVM (soft) | Hinge + regularizer | No | Hard labels + margin |
| Logistic regression | Logistic (cross-entropy) | No | Probabilities |

## Related pages

- [[ml-fundamentals/understanding-machine-learning]]
- [[svm]]
- [[empirical-risk-minimization]]
- [[generative-learning]]
- [[shared/bayes-classifier]]
