# VC Dimension

**Summary**: The Vapnik-Chervonenkis dimension measures the capacity of a hypothesis class by the largest set of points it can shatter (classify in all 2^m possible ways). It is the key quantity that determines PAC learnability for infinite hypothesis classes.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_vcdim.pdf, ls26_seminar_vc_dim_solutions.pdf

**Last updated**: 2026-04-14

---

## Definition: Shattering

A hypothesis class $\mathcal{H}$ **shatters** a set of points $\{x_1, \ldots, x_d\}$ if for every possible labeling $(y_1, \ldots, y_d) \in \{-1, +1\}^d$, there exists $h \in \mathcal{H}$ such that $h(x_i) = y_i$ for all $i$.

**VC dimension** = the largest $d$ such that there **exists** a set of $d$ points shattered by $\mathcal{H}$.

$$\text{VCdim}(\mathcal{H}) = \max\{d : \exists \{x_1, \ldots, x_d\} \text{ that } \mathcal{H} \text{ shatters}\}$$

Important: we only need to find **one** set of size $d$ that can be shattered, but we must show that **no** set of size $d+1$ can be shattered.

## Examples of VC Dimension

### Signed threshold classifiers on $\mathbb{R}$ (source: ml-fundamentals/ls26_seminar_vc_dim_solutions.pdf)

$$\mathcal{H} = \{\text{sign}(x - \theta) \mid \theta \in \mathbb{R}\} \cup \{\text{sign}(\theta - x) \mid \theta \in \mathbb{R}\}$$

**$\text{VCdim} = 2$**. Can shatter any two points $x_1 < x_2$ (all four labelings achievable). Cannot shatter three points: labelings $(+, -, +)$ and $(-, +, -)$ require two sign changes, but each hypothesis changes sign at most once.

### Linear classifiers in $\mathbb{R}^d$ (source: ml-fundamentals/ls26_seminar_vc_dim_solutions.pdf)

$$\mathcal{H} = \{\text{sign}(\langle \mathbf{w}, \mathbf{x} \rangle + b) \mid \mathbf{w} \in \mathbb{R}^d, b \in \mathbb{R}\}$$

**$\text{VCdim} = d + 1$**

**Lower bound**: The $d+1$ points $\{\mathbf{e}_1, \ldots, \mathbf{e}_d, \mathbf{0}\}$ (standard basis + origin) can be shattered. For any labeling $(y_1, \ldots, y_{d+1})$, set $\mathbf{w} = (y_1, \ldots, y_d)$ and $b = \frac{1}{2}y_{d+1}$.

**Upper bound**: Any $d+2$ points in $\mathbb{R}^d$, when lifted to $\mathbb{R}^{d+1}$ as $\mathbf{z}_i = [\mathbf{x}_i, 1]$, are linearly dependent. This forces $\mathbf{z}_1 = \sum a_i' \mathbf{z}_i$, which means one point's classification is determined by the others — so the labeling $y_1 = -1$ with $y_i = \text{sign}(a_i')$ for $i \geq 2$ cannot be realized.

### Finite hypothesis class

$$\text{VCdim}(\mathcal{H}) \leq \log_2 |\mathcal{H}|$$

Proof: shattering $d$ points requires $|\mathcal{H}| \geq 2^d$ distinct hypotheses.

### $\mathcal{H} = \{\text{sign}(\sin(ax)) \mid a \in \mathbb{R}\}$

**$\text{VCdim} = \infty$** despite having only one parameter!

Use points $x_i = 10^{-i}$. For any labeling, construct $a = \pi(1 + \sum \hat{y}_i 10^i)$ where $\hat{y}_i = \frac{1-y_i}{2}$. The key identity $\sin(\pi(k+t)) = (-1)^k \sin(\pi t)$ ensures correct classification at each point.

**Takeaway**: VC dimension measures the effective complexity of a hypothesis class, which is not simply the number of parameters.

## The Fundamental Theorem of Statistical Learning

For a binary classification problem with 0/1-loss, the following are **equivalent** (source: ml-fundamentals/ls26_lecture_vcdim.pdf):

1. $\mathcal{H}$ is PAC-learnable (see [[ml-fundamentals/pac-learning]])
2. The Uniform Law of Large Numbers holds for $\mathcal{H}$
3. ERM is a successful learning algorithm for $\mathcal{H}$
4. **$\text{VCdim}(\mathcal{H}) < \infty$**

### Sample complexity for infinite $\mathcal{H}$

When $\text{VCdim}(\mathcal{H}) = d < \infty$, the sample complexity is:

$$m = \mathcal{O}\left(\frac{d + \ln(1/\delta)}{\varepsilon^2}\right)$$

Compare with finite $\mathcal{H}$: $m = \mathcal{O}\left(\frac{\ln(|\mathcal{H}|/\delta)}{\varepsilon^2}\right)$. The VC dimension replaces $\ln |\mathcal{H}|$ as the measure of complexity.

## Structural Risk Minimization (SRM)

The bias-complexity tradeoff: larger $\mathcal{H}$ → smaller approximation error but larger estimation error. SRM resolves this by (source: ml-fundamentals/ls26_lecture_vcdim.pdf):

1. Define a nested sequence $\mathcal{H}_1 \subset \mathcal{H}_2 \subset \cdots$ with increasing VCdim
2. For each $\mathcal{H}_k$, compute the ERM solution and its generalization bound
3. Choose the $\mathcal{H}_k$ that minimizes the bound (empirical risk + complexity penalty)

This is the theoretical motivation for [[ml-fundamentals/svm|SVM's]] maximum margin principle.

## Related pages

- [[pac-learning]]
- [[empirical-risk-minimization]]
- [[svm]]
- [[linear-classifiers]]
