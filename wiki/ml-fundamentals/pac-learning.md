# PAC Learning

**Summary**: The Probably Approximately Correct (PAC) framework formalizes the question "how many training examples do we need to learn a good classifier with high confidence?" It provides sample complexity bounds for finite hypothesis classes.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_pac.pdf, ls26_seminar_pac_solutions.pdf

**Last updated**: 2026-05-03

---

## Definition

A learning algorithm $A$ is a **successful PAC learner** for hypothesis class $\mathcal{H}$ if for every $\varepsilon > 0$ and $\delta > 0$, there exists $m_0$ such that for all $m \geq m_0$ and all distributions $p(\mathbf{x},y)$:

$$P\!\left(R(p, h_m) - R(p, h_{\mathcal{H}}) \leq \varepsilon\right) \geq 1 - \delta$$

where $h_m = A(T_m)$ is the learned hypothesis, and $h_{\mathcal{H}} = \arg\min_{h \in \mathcal{H}} R(p, h)$ is the best in class.

- $\varepsilon$ = accuracy parameter (how close to the best in $\mathcal{H}$)
- $\delta$ = confidence parameter (probability of failure)
- $m_0(\varepsilon, \delta)$ = sample complexity

## Sample Complexity for Finite $\mathcal{H}$

For a finite hypothesis class with $|\mathcal{H}|$ hypotheses and 0/1-loss, the ERM algorithm is a successful PAC learner with sample complexity (source: ml-fundamentals/ls26_lecture_pac.pdf):

$$m \geq \frac{1}{2\varepsilon^2} \ln\!\frac{2|\mathcal{H}|}{\delta}$$

This follows from Hoeffding's inequality combined with the union bound (see [[empirical-risk-minimization]]).

### Key insight

The sample complexity grows **logarithmically** with $|\mathcal{H}|$. Even very large finite hypothesis classes are PAC-learnable with manageable sample sizes.

## Seminar Example: Decision Tree Leaf Classifiers

Consider a binary decision tree with $M$ leaves partitioning the input space into regions $R_1, \ldots, R_M$ (source: ml-fundamentals/ls26_seminar_pac_solutions.pdf):

- $\mathcal{H} = \{h(\mathbf{x}) = \sum c_r \mathbb{1}[\mathbf{x} \in R_r] \mid c \in \{-1,+1\}^M\}$ — assign a label to each leaf
- $|\mathcal{H}| = 2^M$ (binary label per leaf)
- ERM assigns each leaf the majority label of training points falling in it: $c_r = \mathrm{sign}\!\left(\sum_i \mathbb{1}[\mathbf{x}_i \in R_r]\, y_i\right)$

**Is this a PAC learner?** Yes — $\mathcal{H}$ is finite and the algorithm uses ERM, so ULLN holds.

**Approximation error**: Non-zero for Gaussian class-conditionals, because the Bayes classifier is quadratic and cannot in general be represented by a finite partition.

**Sample complexity**: $m \geq \frac{2}{\varepsilon^2}\left((M+1)\log 2 - \log \delta\right)$

For $M = 128$, $\varepsilon = 0.05$, $\delta = 0.1$: $m \approx 73{,}375$

**[[vc-dimension|VC dimension]]** of this class: $\mathrm{VCdim}(\mathcal{H}) = M$ (each leaf can be labeled independently).

## Seminar Example: LLM Prompt Selection as PAC Learning

Selecting the best among $P = 100$ candidate prompts for an LLM is ERM over a finite hypothesis class (source: ml-fundamentals/ls26_seminar_pac_solutions.pdf):

- Each prompt is a hypothesis
- Empirical accuracy on a benchmark = empirical risk
- Selecting the highest-accuracy prompt = ERM

For $\varepsilon = 0.05$, $\delta = 0.1$:

$$m \geq \frac{2}{0.05^2} \ln\!\frac{200}{0.1} \approx 6{,}081 \text{ benchmark examples}$$

## Conditions for PAC Learnability

A hypothesis class $\mathcal{H}$ is PAC-learnable if and only if:
1. The Uniform Law of Large Numbers (ULLN) holds for $\mathcal{H}$
2. $\mathcal{H}$ has finite [[vc-dimension|VC dimension]] (the Fundamental Theorem)
3. ERM is a successful learning algorithm for $\mathcal{H}$

These are all **equivalent** conditions — see [[vc-dimension]] for the full statement.

## Related pages

- [[empirical-risk-minimization]]
- [[vc-dimension]]
- [[prediction-evaluation]]
- [[shared/bayes-classifier]]
