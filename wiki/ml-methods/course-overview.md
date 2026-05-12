# ML Methods Course Overview

**Summary**: Machine Learning Methods spans classical supervised learning foundations (weeks 1–4) and advanced structured data learning (weeks 5–7), culminating in reinforcement learning. The course narrative is "Increasing Structure → Increasing Inductive Bias → Increasing Reasoning Power."

**Course**: ml-methods

**Sources**: mlm_week_1.pdf, mlm_week_2.pdf, mlm_week_3.pdf, mlm_week_4.pdf, mlm_week_5.pdf, mlm_week_6.pdf, mlm_week_7.pdf, lecture_8_presentation_2026.pdf

**Last updated**: 2026-04-16

---

## Course Structure

### Part 1: Classical ML Foundations (Weeks 1–4)

The course opens with fundamental supervised learning techniques, building from loss functions and empirical risk minimization through to modern ensemble and kernel methods.

- **Week 1**: Supervised learning recap — empirical risk, bias-variance tradeoff, cross-validation, regularization (L1, L2, Elastic Net), Ridge, Lasso
- **Week 2**: Decision trees and forest methods — CART splitting, random forests, bagging, out-of-bag error
- **Week 3**: Boosting and ensemble methods — AdaBoost, gradient boosting, XGBoost, stacking
- **Week 4**: Support vector machines — hard/soft margin, kernel trick, multiclass extensions, SVR

### Part 2: Learning from Structured Data (Weeks 5–7)

A distinct module introducing learnable inductive biases for non-tabular data, progressing from logic-aware models through modern LLMs to interpretability.

- **Week 5**: Neuro-symbolic learning — injecting discrete logic into continuous pipelines; knowledge graph embeddings (TransE); the multiset problem and permutation-invariant aggregation
- **Week 6**: Learning with LLMs — paradigm shift from architectural priors to data-induced priors; transformers and self-attention; tabular foundation models; in-context learning; structured output generation
- **Week 7**: Opening the black box — interpretability and explainability (XAI); post-hoc methods (LIME, SHAP); inherently interpretable models; concept-based interpretability

### Part 3: Reinforcement Learning (Week 8)

From Markov chains to optimal control: formal foundations of sequential decision-making under uncertainty, solved via dynamic programming and convergence-guaranteed iterative algorithms.

- Markov decision processes (MDPs), Bellman equations, state/action value functions
- Policy evaluation, policy iteration, value iteration
- Convergence proofs (Banach fixed-point theorem)
- Finite-horizon problems, non-stationary policies
- Applications: OpenAI Hide and Seek, DeepMind Atari, robotics

---

## Key Themes

**Progressive Structuring**: The course builds from unstructured tabular features (Week 1) through explicitly structured data (relational, graph, symbolic) to the implicit structure learned by LLMs (Week 6).

**Inductive Bias**: Early weeks rely on human-engineered features and explicit model structure (decision boundaries, kernels). Weeks 5–6 inject logical and neural priors. Week 8 formalizes the structure of sequential decision problems.

**Reasoning Power**: From decision trees (local thresholds) through SVMs (global decision surfaces) to neuro-symbolic (discrete + continuous) to transformers (attention-based reasoning) to RL (credit assignment over time).

---

## Related Pages

- [[ml-methods/supervised-learning-recap]]
- [[ml-methods/decision-trees-ensembles]]
- [[ml-methods/svm]]
- [[ml-methods/neuro-symbolic-learning]]
- [[ml-methods/llm-structured-data]]
- [[ml-methods/interpretability-xai]]
- [[ml-methods/reinforcement-learning]]
- [[shared/svm]]
- [[shared/kernel-functions]]
