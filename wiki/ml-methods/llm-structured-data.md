# Learning with LLMs and Structured Data

**Summary**: The paradigm shift from explicit architectural priors to data-induced priors. Covers transformers and self-attention, in-context learning, tabular foundation models, Graphormer, constrained generation, and applications (Text-to-SQL, GraphRAG).

**Course**: ml-methods

**Sources**: mlm_week_6.pdf

**Last updated**: 2026-04-16

---

## The Paradigm Shift: Architectural vs. Data-Induced Priors

**Weeks 1–5 narrative**: Increasing explicit structure (from tabular to relational to graph) and explicit inductive biases (axis-aligned cuts in trees, kernels in SVMs, aggregation functions in GNNs) (source: mlm_week_6.pdf).

**Week 6 shift**: Move away from hand-crafted architecture. Instead, **learn structure from massive unstructured datasets** via self-supervised pre-training (masked language modeling, next-token prediction).

- **Architectural priors**: Hard-coded inductive biases (convolutions enforce locality, GNNs enforce graph structure)
- **Data-induced priors**: Implicit structure learned from internet-scale data (transformers = universal sequence model)

The transformer is "almost universal" — minimal assumptions, learns structure from data.

## Transformers and Self-Attention

### Self-Attention Mechanism

Given input sequence $x = [x_1, \ldots, x_n]$, compute representations via scaled dot-product attention (source: mlm_week_6.pdf):

$$\text{Attention}(Q, K, V) = \text{softmax}\left( \frac{QK^T}{\sqrt{d_k}} \right) V$$

where:
- $Q = XW^Q$ (queries)
- $K = XW^K$ (keys)
- $V = XW^V$ (values)

**Each token attends to all others** (O(1) maximum interaction distance) vs. RNNs/LSTMs (O(n) sequential bottleneck).

### Multi-Head Attention

Project queries, keys, values into multiple subspaces; concatenate outputs:

$$\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, \ldots, \text{head}_h) W^O$$

Different heads capture different relationships (syntactic, semantic, positional, etc.).

### Transformer Block

Stack of multi-head attention + feedforward + layer norm + residual connections:

1. Self-attention over sequence
2. Feed-forward network (point-wise)
3. Layer normalization and residual connections throughout

**Key properties**: Parallelizable (no sequential dependencies), expressive (universal approximator), learnable position embeddings.

## In-Context Learning (ICL)

**Surprising phenomenon**: GPT-style transformers, trained only on next-token prediction, learn to perform tasks from a few in-context examples without fine-tuning (source: mlm_week_6.pdf).

Example (from data): `"Translate English to French: cat -> chat. dog -> chien. apple -> "` → model predicts `pomme`.

- No explicit supervised training on translation
- Model learns to recognize and mimic patterns from examples
- Works even for novel tasks (hypothetical math, symbolic reasoning)

**Mechanism**: Attention patterns adapt to extract task structure from examples.

## Tabular Foundation Models

Extend transformers to non-sequential tabular data by treating each feature as a "token" (source: mlm_week_6.pdf):

- Embed each feature value (possibly discretized)
- Apply transformer blocks
- Decode to predict target or masked features

**Advantages over classical ML**:
- Transfer learning: Pre-train on large heterogeneous datasets
- In-context learning: Few-shot learning via examples in prompt
- Unified framework: Same architecture for text, tabular, multimodal

**Challenges**: Discretization choices, computational cost, comparison benchmarks.

## Graphormer: Transformers for Graphs

Extend transformers to arbitrary graph structures by encoding graph topology in attention (source: mlm_week_6.pdf):

1. **Spatial encoding**: Add edge features to attention edges
2. **Spectral encoding**: Positional encodings from graph Laplacian eigenvalues
3. **Centrality encoding**: Node importance (degree, betweenness, etc.)

Result: Transformer that respects graph structure while maintaining expressive power.

## Constrained Generation and Text-to-SQL

### Structured Output Constraints

Generate structured outputs (JSON, SQL, formal logic) by constraining token sequences (source: mlm_week_6.pdf):

- **Grammar constraints**: Only emit tokens that lead to valid syntax
- **Semantic constraints**: Enforce type-checking, domain-specific rules
- Combine LLM likelihood with constraint satisfaction

### Text-to-SQL

Example: "Show me products over $50 sold in 2024" → `SELECT * FROM products WHERE price > 50 AND year = 2024`

**Approach**:
1. Fine-tune transformer on (question, SQL) pairs
2. Constrain decoding to valid SQL syntax
3. Optionally rank by semantic plausibility (does the SQL match the question intent?)

## GraphRAG: Retrieval-Augmented Generation on Knowledge Graphs

Combine LLMs with structured knowledge (source: mlm_week_6.pdf):

1. Index a knowledge graph (entities and relations)
2. For a query, retrieve relevant subgraph (entities, relations)
3. Augment prompt with retrieved facts
4. Generate response conditioned on facts

**Advantages**: Grounded answers (citations), factual consistency, domain-specific knowledge injection without fine-tuning.

---

## Related Pages

- [[ml-methods/course-overview]]
- [[ml-methods/neuro-symbolic-learning]]
- [[ml-methods/interpretability-xai]]
- [[deep-learning/neural-networks]]
- [[shared/cnn-fundamentals]]
