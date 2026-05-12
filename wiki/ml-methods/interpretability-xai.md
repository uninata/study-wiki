# Interpretability and Explainable AI (XAI)

**Summary**: Methods to understand model predictions and decision-making. Covers the fidelity-plausibility paradox, post-hoc explanation methods (LIME, SHAP), inherently interpretable models, and concept-based interpretability.

**Course**: ml-methods

**Sources**: mlm_week_7.pdf

**Last updated**: 2026-04-16

---

## Motivation and Definitions

**Interpretability** (degree): How much a human can understand the cause of a model's decision (source: mlm_week_7.pdf).

**Trustworthiness**: Does the explanation reflect the model's actual internal computation, or is it post-hoc storytelling?

### High-Accuracy Paradox
High accuracy ≠ correct reasoning. Classic example: Model trained to detect wolves predicts "wolf" based on snow in the background (spurious correlation), not the actual wolf. The Clever Hans effect — the model learns a shortcut, not the intended concept.

## The Fidelity-Plausibility Paradox

**Fidelity** (faithfulness): How accurately does an explanation reflect the model's internal logic?

**Plausibility**: Does the explanation look reasonable to humans?

**The paradox** (source: mlm_week_7.pdf):
- If an AI denies a loan and says it's because "the applicant's name is rare," that's **faithful but implausible** (humans find it unreasonable).
- If the same AI claims "credit history is poor," that's **plausible but potentially unfaithful** (the model never saw credit history; it's a confabulation).

**Implication**: Optimizing purely for plausibility destroys fidelity; optimizing for fidelity may produce explanations humans distrust.

## Post-Hoc Model-Agnostic Methods

Explain any model without modifying it; apply after training.

### LIME (Local Interpretable Model-agnostic Explanations)

Approximate the model locally with a simple, interpretable model (linear regression) (source: mlm_week_7.pdf):

1. Pick an instance to explain
2. Sample perturbed versions of the instance (add noise to features)
3. Get predictions from the black-box model
4. Fit a linear model (or decision tree) to the (perturbation, prediction) pairs, weighted by proximity to the original instance
5. Interpret the linear model's coefficients as feature importance

**Pros**: Model-agnostic, produces local explanations, intuitive
**Cons**: Faithful only locally; depends on sampling distribution; can be unstable

### SHAP (SHapley Additive exPlanations)

Use coalition game theory to assign feature importance: How much does each feature contribute to moving from a baseline (average) prediction to the actual prediction? (source: mlm_week_7.pdf)

For feature $i$:

$$\text{SHAP}_i = \sum_S \frac{|S|!(n - |S| - 1)!}{n!} \left[ f(S \cup \{i\}) - f(S) \right]$$

where $S$ is a coalition of features, and $f(S)$ is the model prediction using only features in $S$ (others use baseline values).

**Properties** (axioms):
- **Local accuracy**: Feature contributions sum to the gap between prediction and baseline
- **Symmetry**: Equivalent features get equal importance
- **Dummy**: Irrelevant features get zero importance

**Pros**: Theoretically grounded, unique solution satisfying axioms
**Cons**: Computationally expensive; requires model access

## Post-Hoc Model-Specific Methods

Exploit internal structure of particular model classes.

### For Tree-Based Models
- **Feature importance**: How often a feature is used for splitting; weight by information gain
- **Decision path**: Trace the splits leading to a prediction; each split is an interpretable constraint

### For Neural Networks
- **Saliency maps**: Gradient of output w.r.t. input pixels; highlight which pixels most affect prediction
- **Attention visualization**: Show which input positions attend to which (if using attention)

## Inherently Interpretable Models (Glass Box)

Design models to be interpretable by construction (source: mlm_week_7.pdf).

### Linear Models
- Feature weights directly interpretable as marginal effect on output
- Tradeoff: Limited expressiveness

### Decision Trees
- Decision path is human-readable rules
- Shallow trees are interpretable; deep trees lose interpretability

### Generalized Additive Models (GAMs)
$$f(x) = \beta_0 + \sum_i g_i(x_i)$$

Learn univariate functions $g_i$ for each feature; each is interpretable in isolation. Main limitation: Assumes feature independence (no interaction terms).

## Concept-Based and Mechanistic Interpretability

Go beyond feature importance to understand learned concepts and circuits.

### Concept Bottleneck Models

Force intermediate representations to be human-interpretable (source: mlm_week_7.pdf):

1. Learn a mapping from input to high-level human concepts (e.g., "has stripes", "is furry")
2. Predict output from concepts
3. Interpretable at both stages: concept-level and concept-to-output

**Advantage**: Concepts are human-defined and understandable
**Disadvantage**: Requires manual concept annotation; may miss task-relevant concepts

### Mechanistic Interpretability

Study the exact circuits and attention patterns that implement a behavior (source: mlm_week_7.pdf):

- Decompose a model into interpretable subcomponents
- Trace information flow through attention layers
- Identify which neurons/heads implement specific functions

**Example**: In a transformer trained on in-context learning, specific attention heads learn to copy relevant examples to the output.

---

## Applications

### Trust & Verification
In high-stakes domains (medicine, finance, law), explainability is essential for regulatory compliance and user trust.

### Model Debugging
Explanations reveal spurious correlations, data distribution shifts, and hidden biases.

### Feature Engineering
Feature importance guides which features matter most; helps reduce dimensionality.

---

## Related Pages

- [[ml-methods/course-overview]]
- [[ml-methods/llm-structured-data]]
- [[ml-methods/neuro-symbolic-learning]]
