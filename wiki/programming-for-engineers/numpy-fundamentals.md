# NumPy Fundamentals

**Summary**: NumPy arrays for numerical computing with efficient creation, indexing, reshaping, broadcasting, and aggregation operations.

**Course**: programming-for-engineers

**Sources**: l4_numpy_arrays.qmd, py_cheatsheet.pdf

**Last updated**: 2026-04-19

---

## Overview

NumPy (Numerical Python) is the foundational scientific computing library. Arrays are typed, homogeneous, and implemented in C for performance. Much faster than Python lists for numerical operations; enables vectorization and parallelization.

```python
import numpy as np
```

---

## Array Creation

### From Data

```python
a = np.array([1, 2, 3, 4, 5])          # 1D array
b = np.array([[1, 2], [3, 4]])         # 2D array
c = np.array([1, 2, 3], dtype=float)   # Specify dtype
```

### Ranges and Sequences

```python
np.arange(0, 10, 2)      # [0, 2, 4, 6, 8] (step-based, like range)
np.linspace(0, 1, 5)     # [0.0, 0.25, 0.5, 0.75, 1.0] (count-based)
np.r_[1:10:2]            # [1, 3, 5, 7, 9] (r_ syntax)
np.r_[0:1:5j]            # 5 evenly-spaced values (5j = 5 points)
```

### Pre-filled Arrays

```python
np.zeros((3, 3))         # 3×3 matrix of zeros
np.ones((2, 4))          # 2×4 matrix of ones
np.eye(5)                # 5×5 identity matrix
np.full((3, 3), 7)       # 3×3 matrix filled with 7
```

### Random Arrays

```python
np.random.rand(3, 3)                 # Uniform [0, 1)
np.random.randn(3, 3)                # Standard normal distribution
np.random.randint(0, 10, (2, 3))     # 2×3 ints in [0, 10)
np.random.seed(42)                   # Reproducibility
```

---

## Array Properties

```python
arr = np.arange(12).reshape(3, 4)

arr.shape       # (3, 4) – dimensions
arr.size        # 12 – total elements
arr.ndim        # 2 – number of dimensions
arr.dtype       # dtype('int64') – element type
arr.T           # Transpose
```

---

## Indexing and Slicing

### 1D Indexing

```python
arr = np.array([10, 20, 30, 40, 50])
arr[0]          # 10
arr[-1]         # 50
arr[1:4]        # [20, 30, 40]
arr[::2]        # [10, 30, 50] (every 2nd element)
```

### 2D Indexing

```python
arr = np.arange(12).reshape(3, 4)  # 3×4 matrix
arr[0]          # [0, 1, 2, 3] (first row)
arr[0, 2]       # 2 (element at row 0, col 2)
arr[1:3, 0:2]   # Rows 1-2, cols 0-1 (submatrix)
arr[:, -1]      # [3, 7, 11] (last column)
```

### Boolean Indexing

```python
arr = np.array([1, 2, 3, 4, 5])
mask = arr > 2
arr[mask]       # [3, 4, 5] (elements where mask is True)

# Multi-condition (use | and &, not 'or'/'and')
mask = (arr > 2) & (arr < 5)
arr[mask]       # [3, 4]
```

### Fancy Indexing

```python
arr = np.array([10, 20, 30, 40, 50])
indices = [1, 3]
arr[indices]    # [20, 40] (elements at indices 1 and 3)
```

---

## Reshaping

```python
arr = np.arange(12)

arr.reshape(3, 4)       # View (same data, different shape)
arr.reshape(-1, 4)      # -1 infers dimension: 3×4
arr.flatten()           # Copy flattened to 1D
arr.ravel()             # View flattened to 1D

arr.T                   # Transpose
np.expand_dims(arr, 0)  # Add new axis
np.squeeze(arr)         # Remove singleton dimensions
```

---

## Broadcasting

Automatically expand dimensions for element-wise operations:

```python
a = np.ones((3, 4))
b = np.ones((4,))
c = a + b               # b broadcasts to (3, 4), then added

# Rule: trailing dimensions must match or be 1
a = np.ones((3, 1))     # (3, 1)
b = np.ones((1, 4))     # (1, 4)
c = a + b               # Broadcasts to (3, 4)
```

---

## Operations

### Element-wise Arithmetic

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

a + b           # [5, 7, 9]
a * b           # [4, 10, 18]
a ** 2          # [1, 4, 9]
np.sqrt(a)      # [1.0, 1.41..., 1.73...]
```

### Aggregation

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])

arr.sum()               # 21 (sum all elements)
arr.sum(axis=0)         # [5, 7, 9] (sum along columns)
arr.sum(axis=1)         # [6, 15] (sum along rows)

arr.mean(axis=0)        # Mean along axis 0
np.max(arr)             # 6 (maximum element)
np.min(arr, axis=1)     # [1, 4] (min per row)
```

### Linear Algebra

```python
a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])

np.dot(a, b)            # Matrix multiplication
a @ b                   # Same (@ operator, Python 3.5+)
np.linalg.inv(a)        # Matrix inverse
np.linalg.det(a)        # Determinant
np.linalg.eig(a)        # Eigenvalues and eigenvectors
```

---

## Iteration

```python
arr = np.arange(6).reshape(2, 3)

# 1D iteration
for val in arr.flat:
    print(val)

# 2D with indices
for idx, val in np.ndenumerate(arr):
    print(f"Index {idx}: {val}")

# Row iteration
for row in arr:
    print(row)
```

---

## Common Pitfalls

1. **View vs Copy**: Slicing often returns view (modifications affect original); use `.copy()` for independence
2. **Integer indexing**: `arr[1.0]` raises TypeError; indices must be integers
3. **Axis confusion**: `axis=0` sums rows (collapses rows); `axis=1` sums columns
4. **Empty arrays**: `np.array([]).mean()` returns NaN; check size first
5. **Type mixing**: `np.array([1, "2"])` converts all to strings

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/basic-data-structures]]
- [[shared/array-operations]]
- [[shared/algorithmic-complexity]]
- [[programming-for-engineers/scientific-python-libraries]]
