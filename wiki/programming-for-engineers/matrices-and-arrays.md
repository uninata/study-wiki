# Matrices and Arrays

**Summary**: 2D array operations in Python including creation, manipulation, and basic matrix algebra (addition, transposition, multiplication).

**Course**: programming-for-engineers

**Sources**: l2_matrices_processing.qmd

**Last updated**: 2026-04-19

---

## Matrices as 2D Lists

A matrix is a 2D array stored as nested lists (list of rows). Access via `matrix[row][col]`.

### Creation

```python
# Direct initialization
m = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# List comprehension
m = [[j + i*3 for j in range(1, 4)] for i in range(3)]

# Pre-allocation (initialize with zeros)
rows, cols = 3, 4
m = [[0] * cols for _ in range(rows)]
```

### Printing Matrices

```python
# Format-aware printing for readability
def formatted_print_matrix(m):
    n_rows = len(m)
    max_digits = len(str(max(max(row) for row in m)))
    for ri, row in enumerate(m):
        row_str = '[' + ', '.join(f"{x:>{max_digits}d}" for x in row)
        row_str += ']' + (',' if ri < n_rows - 1 else '')
        print(row_str)
```

---

## Matrix Operations

### Filling with Values

```python
def fill_with_constant(matrix, value):
    for r in range(len(matrix)):
        for c in range(len(matrix[0])):
            matrix[r][c] = value

def fill_with_random(matrix, min_val=0, max_val=100):
    import random
    for r in range(len(matrix)):
        for c in range(len(matrix[0])):
            matrix[r][c] = random.randint(min_val, max_val)
```

### Addition

Element-wise: C[i][j] = A[i][j] + B[i][j]. Same-shaped matrices required.

```python
def add_matrices(A, B):
    rows, cols = len(A), len(A[0])
    C = [[0] * cols for _ in range(rows)]
    for r in range(rows):
        for c in range(cols):
            C[r][c] = A[r][c] + B[r][c]
    return C
```

### Transposition

Swap rows and columns: A^T[i][j] = A[j][i].

```python
def transpose(matrix):
    rows, cols = len(matrix), len(matrix[0])
    trans = [[0] * rows for _ in range(cols)]
    for r in range(rows):
        for c in range(cols):
            trans[c][r] = matrix[r][c]
    return trans
```

### Multiplication

Matrix multiplication: C = A × B where C[i][k] = Σ_j A[i][j] × B[j][k].
- **Requirement**: Columns of A must equal rows of B
- **Result shape**: If A is m×n and B is n×p, then C is m×p
- **Complexity**: O(m × n × p)

```python
def multiply_matrices(A, B):
    rows_A, cols_A = len(A), len(A[0])
    rows_B, cols_B = len(B), len(B[0])
    
    if cols_A != rows_B:
        raise ValueError(f"Shape mismatch: A is {rows_A}×{cols_A}, "
                        f"B is {rows_B}×{cols_B}")
    
    C = [[0] * cols_B for _ in range(rows_A)]
    for r in range(rows_A):
        for c in range(cols_B):
            for k in range(cols_A):
                C[r][c] += A[r][k] * B[k][c]
    return C
```

### Comparison

```python
def matrices_equal(A, B):
    rows, cols = len(A), len(A[0])
    if len(B) != rows or len(B[0]) != cols:
        return False
    for r in range(rows):
        for c in range(cols):
            if A[r][c] != B[r][c]:
                return False
    return True
```

---

## Graphical Matrices (Images)

Grayscale images are matrices where each element represents pixel intensity (0-255 or 0.0-1.0).

```python
from matplotlib import pyplot as plt

# Image as matrix (0-255 intensity)
img = np.random.randint(0, 256, size=(100, 100))
plt.imshow(img, cmap='gray')
plt.axis('off')
plt.show()
```

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/basic-data-structures]]
- [[programming-for-engineers/array-searching]]
- [[programming-for-engineers/array-processing-statistics]]
- [[shared/array-operations]]
- [[shared/algorithmic-complexity]]
