# Neuro-Symbolic Learning

**Summary**: Combining discrete symbolic logic with continuous neural network optimization. Covers the fundamental limits of graph neural networks (1-WL test, multiset problem), knowledge graph embeddings (TransE), and techniques for injecting logic as learnable loss functions.

**Course**: ml-methods

**Sources**: mlm_week_5.pdf

**Last updated**: 2026-04-16

---

## Motivation: Bridging Logic and Learning

Classical symbolic AI excels at structured reasoning but struggles with continuous noisy data. Deep learning learns from data but lacks interpretability and often fails on structured tasks (source: mlm_week_5.pdf).

**Neuro-symbolic learning**: Inject discrete logical constraints into continuous pipelines as loss functions, enabling end-to-end differentiable optimization of symbolic predicates.

## The Limitations of Graph Neural Networks

### The 1-WL Test

The 1-Weisfeiler-Lehman test: A standard graph isomorphism heuristic that counts colored node pairs and updates labels by aggregating neighbor colors (source: mlm_week_5.pdf).

**Key insight**: GNNs based on permutation-invariant aggregation (sum, mean, max) are bounded by the expressiveness of the 1-WL test.

### The Multiset Problem

Consider two graphs: a star (one central node connected to $n$ leaves) and a path (linear chain). Both have identical 1-WL color sequences but different graph properties. A 1-WL GNN cannot distinguish them (source: mlm_week_5.pdf).

**Why it matters**: Graphs encoding relational structures (logic puzzles, constraint problems) often require reasoning beyond 1-WL expressiveness.

### The Fundamental Barrier

To go beyond 1-WL, we need to:
1. Compute permutation-invariant aggregates of **variable-sized multisets** (e.g., tuples of neighbors)
2. Distinguish cases where aggregates differ but 1-WL colors don't

**Solutions**:
- Higher-order GNNs: Aggregate over $k$-tuples of neighbors (expensive)
- Relational inductive biases: Inject structural knowledge (e.g., attention mechanisms, message types)
- **Neuro-symbolic methods**: Explicitly represent and reason about logical constraints

## Knowledge Graph Embeddings

Represent entities and relations as vectors in $\mathbb{R}^d$; learn embeddings by scoring triples (source: mlm_week_5.pdf).

### TransE (Translation Model)

For a triple $(h, r, t)$ (head entity, relation, tail entity):

$$\text{Score}(h, r, t) = -\|h + r - t\|_2^2$$

- Intuition: Relation $r$ acts as a translation from head to tail in embedding space
- Simple yet effective; captures hierarchical structures, hierarchies well
- Loss: Margin-based ranking loss on observed vs. negative triples

## Injecting Logic as Loss

Make logic constraints differentiable by converting them to soft penalties (source: mlm_week_5.pdf):

**Example**: Enforce that relation $r$ is transitive:
- For all triples $(h, r, m)$ and $(m, r, t)$, encourage $(h, r, t)$ to score high
- Add loss term: $\sum_{h, m, t} \text{ReLU}(1 - \text{Score}(h, r, t) + \text{Score}(h, r, m) + \text{Score}(m, r, t))$

**Benefits**:
- Combines learning from data (supervised) with logical structure (unsupervised)
- End-to-end differentiable; compatible with standard optimizers
- Interpretable: Logical penalties are human-readable

## Relational Synthesis

Building complex predicates from simpler learned components (source: mlm_week_5.pdf):

- Learn base relations (e.g., "parent", "sibling")
- Compose via logical operators: $\text{ancestor}(x, y) = \text{parent}(x, y) \lor \exists z. \text{parent}(x, z) \land \text{ancestor}(z, y)$
- Optimize component embeddings jointly

This contrasts with pure symbolic approaches (no learning from examples) and pure neural approaches (no explicit reasoning).

---

## Related Pages

- [[ml-methods/course-overview]]
- [[ml-methods/llm-structured-data]]
- [[ml-methods/interpretability-xai]]
- [[shared/cnn-fundamentals]]
