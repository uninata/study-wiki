# Array Operations: Indexing, Broadcasting, Aggregation

**Summary**: Core NumPy array operations used across scientific computing: indexing/slicing patterns, broadcasting rules, aggregation functions, and view vs copy semantics.

**Course**: programming-for-engineers, deep-learning, computer-vision

**Sources**: l4_numpy_arrays.qmd, py_cheatsheet.pdf

**Last updated**: 2026-04-19

---

## Array Basics

Arrays are typed, homogeneous collections enabling vectorized (parallel) operations.

```python
import numpy as np

# Creation
arr = np.array([1, 2, 3, 4, 5])
arr.shape        # (5,) – 1D
arr.dtype        # dtype('int64')
arr.size         # 5

# 2D array
mat = np.array([[1, 2], [3, 4]])
mat.shape        # (2, 2)
```

---

## Indexing and Slicing

### 1D Indexing

```python
arr = np.arange(10)  # [0, 1, 2, ..., 9]

arr[0]           # 0 (first element)
arr[-1]          # 9 (last element)
arr[2:5]         # [2, 3, 4] (slice from 2 to 5 exclusive)
arr[::2]         # [0, 2, 4, 6, 8] (every 2nd element)
arr[::-1]        # [9, 8, 7, ..., 0] (reversed)
```

### 2D Indexing

```python
mat = np.arange(12).reshape(3, 4)
# [[0  1  2  3]
#  [4  5  6  7]
#  [8  9  10 11]]

mat[0]           # [0, 1, 2, 3] (first row)
mat[0, 2]        # 2 (row 0, col 2)
mat[1:3, 0:2]    # [[4, 5], [8, 9]] (rows 1-2, cols 0-1)
mat[:, -1]       # [3, 7, 11] (last column)
mat[0, :]        # [0, 1, 2, 3] (first row, explicit)
```

### Boolean Indexing

```python
arr = np.array([1, 2, 3, 4, 5])

mask = arr > 2
arr[mask]        # [3, 4, 5]

# Multi-condition (use | and &, NOT or/and)
mask = (arr > 2) & (arr < 5)
arr[mask]        # [3, 4]

mask = (arr < 2) | (arr > 4)
arr[mask]        # [1, 5]
```

### Fancy Indexing

```python
arr = np.array([10, 20, 30, 40, 50])
indices = [1, 3, 0]
arr[indices]     # [20, 40, 10]
```

---

## Broadcasting

Automatically expand dimensions for compatible operations.

### Broadcasting Rules

1. Arrays aligned from rightmost (trailing) dimension
2. Dimensions must either match or one must be 1
3. Singleton dimension 1 stretches to match the other

```python
a = np.ones((3, 4))     # (3, 4)
b = np.ones((4,))       # (4,)
# b broadcasts to (1, 4), then (3, 4)
c = a + b               # (3, 4)

# Explicit alignment
a = np.ones((3, 1))     # (3, 1)
b = np.ones((1, 4))     # (1, 4)
c = a + b               # (3, 4) – both broadcast
```

### Common Broadcasting Patterns

```python
# Add scalar to array
arr = np.array([1, 2, 3])
result = arr + 5        # [6, 7, 8]

# Column + Row
col = np.array([[1], [2], [3]])     # (3, 1)
row = np.array([[10, 20, 30]])      # (1, 3)
mat = col + row         # (3, 3)
# [[11, 21, 31],
#  [12, 22, 32],
#  [13, 23, 33]]
```

---

## Aggregation Operations

Reduce arrays along axes.

### Axis Convention

- **axis=0**: Along rows (collapse rows, result per column)
- **axis=1**: Along columns (collapse columns, result per row)

```python
mat = np.array([[1, 2, 3],
                [4, 5, 6]])  # (2, 3)

mat.sum()           # 21 (all elements)
mat.sum(axis=0)     # [5, 7, 9] (sum per column)
mat.sum(axis=1)     # [6, 15] (sum per row)

mat.mean(axis=0)    # [2.5, 3.5, 4.5]
np.max(mat, axis=1) # [3, 6]
np.min(mat)         # 1
```

---

## View vs Copy

**View**: Reference to original data; modifications affect original.
**Copy**: Independent data; modifications don't affect original.

### Slicing (usually view)

```python
arr = np.arange(10)
b = arr[2:5]        # View (modifications affect arr)
b[0] = 999
print(arr)          # [0, 1, 999, 3, 4, 5, ...]
```

### Copy (independent)

```python
c = arr[2:5].copy()  # Independent copy
c[0] = 999
print(arr)           # arr unchanged
```

### Reshape (depends)

```python
arr = np.arange(12)
mat = arr.reshape(3, 4)  # View if possible
mat[0, 0] = 999
print(arr)               # arr[0] also changed

mat2 = arr.reshape(3, 4).copy()  # Force copy
mat2[0, 0] = 999
print(arr)               # arr unchanged
```

---

## Common Operations

### Element-wise Math

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

a + b               # [5, 7, 9]
a * b               # [4, 10, 18]
a ** 2              # [1, 4, 9]
np.sqrt(a)          # [1.0, 1.41..., 1.73...]
np.exp(a)           # [e, e², e³]
np.log(a)           # [0, 0.69..., 1.09...]
```

### Matrix Operations

```python
a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])

np.dot(a, b)        # Matrix multiplication
a @ b               # Same (@ operator)
a * b               # Element-wise (not matrix mult)

np.linalg.inv(a)    # Matrix inverse
np.linalg.det(a)    # Determinant
```

---

## Common Pitfalls

1. **Axis confusion**: axis=0 is rows dimension (sums down columns)
2. **View modifications**: Slice edits affect original; use .copy() for independence
3. **Boolean indexing**: Use `|` and `&`, not `or`/`and` (requires parentheses)
4. **Mixed dtypes**: `np.array([1, "2"])` converts all to strings
5. **Empty arrays**: `np.array([]).mean()` returns NaN; check size
6. **Integer index casting**: `arr[1.0]` raises TypeError
7. **Broadcasting mismatch**: Trailing dimensions must align

---

## Performance Tips

1. **Vectorize**: Replace loops with NumPy (10-100x faster)
2. **Use views when safe**: Avoid unnecessary copies for large arrays
3. **Contiguous memory**: Row-major (C) order faster for row iteration
4. **Type selection**: `float32` smaller/faster than `float64` if precision allows
5. **In-place operations**: `arr += 1` faster than `arr = arr + 1`

---

## Related pages

- [[programming-for-engineers/numpy-fundamentals]]
- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/scientific-python-libraries]]
- [[shared/algorithmic-complexity]]
- [[shared/visualization-techniques]]
