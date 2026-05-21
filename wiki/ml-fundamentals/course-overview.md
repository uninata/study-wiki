# ML Fundamentals — Course Overview

**Summary**: Statistical learning theory course covering the mathematical foundations of machine learning: how to formalize learning from data, what guarantees we can provide, and how to build and evaluate classifiers.

**Course**: ml-fundamentals

**Sources**: ls26_lecture_erm.pdf, ls26_lecture_pac.pdf, ls26_lecture_genlearn.pdf, ls26_lecture_vcdim.pdf, ls26_lecture_svm.pdf, ls26_lecture_linear.pdf, ls26-lecture-predeval.pdf, all seminar solutions, understanding-machine-learning-theory-algorithms.pdf

**Last updated**: 2026-05-16

---

## Logical Structure

The course builds a rigorous chain from the learning problem formulation to practical algorithms:

The supplementary textbook [[ml-fundamentals/understanding-machine-learning]] reinforces this chain by making the inductive-bias story explicit: ERM alone can overfit, PAC/VC theory identifies when ERM generalizes, and algorithms such as SVMs and regularized models encode useful prior assumptions (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

1. **[[empirical-risk-minimization]]** — Formalize learning as optimization. Decompose error into estimation + approximation + Bayes risk. Show that minimizing empirical risk on training data approximates the true risk via Hoeffding's inequality + union bound.

2. **[[generative-learning]]** — An alternative to discriminative ERM: model the joint distribution $p(x,y)$ via MLE, then derive a classifier using the [[shared/bayes-classifier|plug-in Bayes rule]]. Covers Fisher information and Cramér-Rao bound for estimator quality.

3. **[[pac-learning]]** — Formalize "how many samples do we need?" Sample complexity bounds for finite hypothesis classes. The PAC framework gives precise (ε, δ)-guarantees.

4. **[[vc-dimension]]** — Extend PAC theory to infinite hypothesis classes via the VC dimension. The Fundamental Theorem of Statistical Learning: a class is PAC-learnable ⟺ it has finite VC dimension. Structural Risk Minimization balances model complexity vs. fit.

5. **[[svm]]** — Maximum-margin classifiers as a realization of SRM. Hard-margin → soft-margin → dual formulation → [[shared/kernel-functions|kernel trick]] for nonlinear boundaries.

6. **[[linear-classifiers]]** — Practical linear classification methods: perceptron, logistic regression, multi-class extensions. The workhorses that connect theory to implementation.

7. **[[prediction-evaluation]]** — After training, how do we evaluate? Confidence intervals for the true error using Hoeffding's inequality. How many test samples do we need?

## Key Themes for Exam Prep

- **Error decomposition**: $R(h)$ = Bayes risk + approximation error + estimation error. Know which term each technique targets.
- **Hoeffding's inequality** appears everywhere: ERM generalization bounds, PAC sample complexity, confidence intervals. Master the formula and how to apply it with the union bound.
- **VC dimension** is the bridge between finite and infinite hypothesis classes. Know the definition (shattering), how to prove $\mathrm{VCdim}$ for specific classes, and its role in the Fundamental Theorem.
- **Bayes classifier** is the gold standard. Many problems ask "what is the approximation error?" — this means: does the Bayes classifier lie in the hypothesis class?
- **Seminar problems** are the best exam preparation. They require applying the theory to concrete setups (Gaussian mixtures, decision trees, CNN model selection, LLM prompt selection).

## Related pages

- [[ml-fundamentals/understanding-machine-learning]]
- [[empirical-risk-minimization]]
- [[pac-learning]]
- [[vc-dimension]]
- [[generative-learning]]
- [[svm]]
- [[linear-classifiers]]
- [[prediction-evaluation]]
- [[shared/bayes-classifier]]
- [[shared/kernel-functions]]
