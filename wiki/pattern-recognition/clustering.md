# Clustering

**Summary**: Unsupervised partitioning of data into groups. Covers K-means (Lloyd's algorithm), convergence properties, local minima, K-means++ initialization with approximation guarantees, and generalizations (K-medians, K-medoids, string clustering, Euclidean clustering with Weiszfeld's algorithm).

**Course**: pattern-recognition

**Sources**: pr_10_k_means_2015_12_04.pdf

**Last updated**: 2026-04-15

---

## K-Means Algorithm (Lloyd's)

Given data {x_1, ..., x_n} and desired number of clusters K (source: pattern-recognition/pr_10_k_means_2015_12_04.pdf):

1. **Initialize** cluster centers {c_1, ..., c_K} (e.g., random selection from data)
2. **Assignment step**: assign each x_i to the nearest center: T_k = {x : ||x - c_k|| <= ||x - c_j|| for all j}
3. **Update step**: recompute centers: c_k = (1/|T_k|) sum_{x in T_k} x (mean of assigned points)
4. **Terminate** if assignments don't change, otherwise go to 2

### Objective Function
K-means minimizes the **within-cluster sum of squares** (source: pattern-recognition/pr_10_k_means_2015_12_04.pdf):

**J = sum_{k=1}^{K} sum_{x in T_k} ||x - c_k||^2**

Each step (assignment and update) can only decrease or maintain J, guaranteeing convergence to a **local minimum**.

### Convergence Properties
- J decreases monotonically at each iteration
- Convergence is guaranteed (finite number of possible partitions)
- The solution depends on initialization — **not guaranteed to find the global optimum**
- Multiple random restarts are standard practice

## Local Minima

K-means can converge to different local minima depending on initialization. Example with 4 points A=(-3,1), B=(-3,-1), C=(3,1), D=(3,-1), K=2 (source: pattern-recognition/pr_10_k_means_2015_12_04.pdf):
- Minimum 1: J = 36 (vertical split — suboptimal)
- Minimum 2: J = 4 (horizontal split — global optimum)
- Which minimum is reached depends entirely on which points are chosen as initial centers

## K-Means++ Initialization

A smarter initialization that selects centers proportional to their squared distance from existing centers (source: pattern-recognition/pr_10_k_means_2015_12_04.pdf):

1. Choose c_1 uniformly at random from data
2. For each point x_l, compute distance d_l to nearest existing center
3. Select next center with probability **p(l) = d_l^2 / sum_l d_l^2**
4. Repeat until K centers are chosen
5. Run standard K-means with these initial centers

### Approximation Guarantee
K-means++ satisfies (source: pattern-recognition/pr_10_k_means_2015_12_04.pdf):

**E[J] <= 8(ln K + 2) J_opt**

This is O(log K)-competitive with the optimal solution — no such bound exists for standard random initialization.

### Empirical Effect
Experiments show K-means++ achieves (source: pattern-recognition/pr_10_k_means_2015_12_04.pdf):
- Much lower J_mean across multiple runs
- Much lower J_max (worst case)
- Both methods find J_opt = J_min, but K-means++ does so much more consistently
- As cluster separation increases, K-means++ advantage grows dramatically

## K-Means Generalizations

The K-means framework generalizes by changing the distance function d(.,.) and the prototype optimization (source: pattern-recognition/pr_10_k_means_2015_12_04.pdf):

### K-Medians
- Distance: L1 metric ||c - x||_1
- Prototype: component-wise **median** of assigned points
- More robust to outliers than K-means

### K-Medoids
- Distance: any dissimilarity measure
- Prototype: restricted to be an actual data point (the medoid)
- c_k = argmin_{x in T_k} sum_{x' in T_k} d(x, x')
- Works with non-Euclidean distances

### Euclidean Clustering (L2 metric)
- Distance: Euclidean norm ||c - x||
- Prototype: **geometric median** (no closed-form solution)
- Computed iteratively using **Weiszfeld's algorithm**: w_i = 1/||x_i - m^(t)||, then m^(t+1) = weighted mean with weights w_i

### String Clustering
- Distance: **Levenshtein distance** (edit distance)
- Prototype optimization may be restricted to data points (NP-hard in general)
- Convergence not always guaranteed since the prototype step may only improve J, not minimize it

## Related pages

- [[pattern-recognition/em-algorithm]]
- [[pattern-recognition/pca]]
- [[pattern-recognition/non-parametric-methods]]
