# Understanding Machine Learning

**Summary**: Source summary for Shalev-Shwartz and Ben-David's theory-first machine learning textbook, mapped to the ML Fundamentals lecture sequence. The most relevant chapters reinforce ERM, PAC learning, VC dimension, model selection, SVMs, kernels, neural networks, generative learning, and EM.

**Course**: ml-fundamentals

**Sources**: understanding-machine-learning-theory-algorithms.pdf

**Last updated**: 2026-05-16

---

## Role in the Course

*Understanding Machine Learning: From Theory to Algorithms* gives the theoretical backbone behind much of ML Fundamentals: it starts from the statistical learning framework, explains why unrestricted ERM can overfit, and then builds PAC, agnostic PAC, uniform convergence, VC dimension, SRM, and margin-based learning as progressively sharper answers to the question of when learning is possible (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

For this wiki, the book is best used as a supporting reference rather than a replacement for the lecture PDFs. The lecture pages keep the course notation and exam examples; this source adds the textbook framing, stronger conceptual bridges, and additional sample-complexity/algorithmic context (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Relevant Chapter Map

| Book chapter | Main course pages enhanced |
|--------------|----------------------------|
| Ch. 2, A Gentle Start | [[ml-fundamentals/empirical-risk-minimization]], [[ml-fundamentals/pac-learning]] |
| Ch. 3-4, PAC and uniform convergence | [[ml-fundamentals/pac-learning]], [[ml-fundamentals/prediction-evaluation]] |
| Ch. 5, Bias-complexity tradeoff | [[ml-fundamentals/empirical-risk-minimization]], [[ml-fundamentals/model-selection]] |
| Ch. 6, VC dimension | [[ml-fundamentals/vc-dimension]] |
| Ch. 7, Nonuniform learnability and SRM | [[ml-fundamentals/vc-dimension]], [[ml-fundamentals/model-selection]] |
| Ch. 9, Linear predictors | [[ml-fundamentals/linear-classifiers]] |
| Ch. 11, Model selection and validation | [[ml-fundamentals/model-selection]], [[ml-fundamentals/prediction-evaluation]] |
| Ch. 15-16, SVMs and kernels | [[ml-fundamentals/svm]], [[shared/kernel-functions]] |
| Ch. 20, Neural networks | [[ml-fundamentals/deep-learning-generalization]] |
| Ch. 24, Generative models | [[ml-fundamentals/generative-learning]], [[ml-fundamentals/unsupervised-learning]] |

## Core Additions

The book makes the role of **inductive bias** explicit: ERM over all possible predictors can fit the training data perfectly and still fail on the true distribution, so the hypothesis class must encode prior knowledge before seeing the sample (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf). This directly supports [[ml-fundamentals/empirical-risk-minimization]] and [[ml-fundamentals/pac-learning]].

Its [[ml-fundamentals/vc-dimension|VC dimension]] chapter sharpens the learnability story: infinite hypothesis classes can be learnable, but only when their effective behavior on finite samples is controlled by finite VC dimension and the growth function (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

The model-selection chapter gives a practical diagnostic layer: validation is useful because the validation set is independent of the trained predictor, and learning curves help distinguish underfitting from overfitting when a selected model performs badly (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

The algorithm chapters connect lecture topics to broader patterns: linear predictors are affine functions composed with task-specific output maps, SVMs replace plain ERM among separating hyperplanes with maximum-margin inductive bias, kernels express prior knowledge through similarity functions, and EM handles latent-variable MLE by alternating posterior inference and parameter fitting (source: ml-fundamentals/understanding-machine-learning-theory-algorithms.pdf).

## Related pages

- [[ml-fundamentals/empirical-risk-minimization]]
- [[ml-fundamentals/pac-learning]]
- [[ml-fundamentals/vc-dimension]]
- [[ml-fundamentals/model-selection]]
- [[ml-fundamentals/linear-classifiers]]
- [[ml-fundamentals/svm]]
- [[ml-fundamentals/prediction-evaluation]]
- [[ml-fundamentals/generative-learning]]
- [[ml-fundamentals/unsupervised-learning]]
- [[ml-fundamentals/deep-learning-generalization]]
- [[shared/kernel-functions]]
