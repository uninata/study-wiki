# Algorithmic Complexity Analysis

**Summary**: Asymptotic complexity notation (Big-O) with operation counting, empirical measurement, and understanding trade-offs between theoretical and practical performance.

**Course**: programming-for-engineers, deep-learning, ml-methods

**Sources**: l1_intro_strctures.qmd, l11_algorithmic_complexity.qmd

**Last updated**: 2026-04-19

---

## Big-O Notation

Describes how algorithm runtime scales with input size n, ignoring constants and lower-order terms.

### Common Complexity Classes

| Notation | Name | Example |
|----------|------|---------|
| O(1) | Constant | Array access, dict lookup |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Linear search, simple loop |
| O(n log n) | Linearithmic | Merge sort, quick sort (avg) |
| O(n²) | Quadratic | Bubble sort, nested loops |
| O(n³) | Cubic | Triple nested loops |
| O(2ⁿ) | Exponential | Brute-force subset generation |
| O(n!) | Factorial | All permutations |

**Growth rates**: O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(2ⁿ) < O(n!)

---

## Operation Counting

Count primitive operations (assignment, comparison, arithmetic) to derive formula, then simplify to asymptotic class.

### Examples

**Sum array** (O(n)):
```python
def sum_array(arr):
    n = len(arr)          # 2 ops (len + assign)
    total = 0             # 1 op
    for i in range(n):    # n iterations
        total += arr[i]   # 3 ops per iteration (access + add + assign)
    return total          # 1 op
```
Total: 2 + 1 + n×3 + 1 = 3n + 4 = O(n)

**Bubble sort** (O(n²)):
```python
def bubble_sort(arr):
    n = len(arr)                    # O(1)
    for i in range(n):              # n iterations
        for j in range(n - i - 1):  # (n-i) iterations
            if arr[j] > arr[j+1]:   # 3 ops
                swap(arr[j], arr[j+1])  # 4 ops
```
Total: Σᵢ₌₀ⁿ (n-i) × 7 ≈ n²/2 × 7 = O(n²)

**Binary search** (O(log n)):
```python
def binary_search(arr, target):
    # Each iteration halves search space
    # log₂(n) halvings until size 1
    # O(log n)
```

---

## Best, Average, Worst Case

Three scenarios matter:

### Linear Search
```python
def linear_search(arr, target):
    for i in range(len(arr)):
        if arr[i] == target:
            return i
    return -1
```

- **Best**: O(1) – target at position 0
- **Average**: O(n/2) = O(n) – target typically mid-array
- **Worst**: O(n) – target at end or not present

### Quick Sort
- **Best**: O(n log n) – balanced pivots
- **Average**: O(n log n) – typical random pivots
- **Worst**: O(n²) – sorted input, poor pivot choice

### Binary Search (on sorted array)
- **Best**: O(1) – target at middle
- **Average**: O(log n) – position independent
- **Worst**: O(log n) – position independent

---

## Measurement and Profiling

### Empirical Timing

```python
import time
import numpy as np

def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]

# Measure on increasing input sizes
for n in [100, 500, 1000, 5000]:
    arr = np.random.permutation(n)
    start = time.time()
    bubble_sort(arr.copy())
    elapsed = time.time() - start
    print(f"n={n}: {elapsed:.4f}s")
```

**Expect**: Time ∝ n²; doubling n → 4x time

### Python timeit Module

```python
import timeit

# Time a single operation
def sum_array(arr):
    return sum(arr)

arr = list(range(1000))
result = timeit.timeit(lambda: sum_array(arr), number=10000)
print(f"Average time: {result / 10000:.6f}s")
```

### Plotting for Verification

```python
import matplotlib.pyplot as plt

n_values = [10, 50, 100, 500, 1000]
times = []

for n in n_values:
    arr = np.random.permutation(n)
    start = time.time()
    bubble_sort(arr.copy())
    times.append(time.time() - start)

plt.plot(n_values, times, 'o-', label='Bubble sort')
plt.plot(n_values, [n**2 / 50000 for n in n_values], '--', label='O(n²)')
plt.xlabel('Input size (n)')
plt.ylabel('Time (seconds)')
plt.legend()
plt.show()
```

---

## Space Complexity

Memory usage in terms of input size:

| Algorithm | Space |
|-----------|-------|
| Bubble sort (in-place) | O(1) |
| Merge sort | O(n) |
| Quick sort (in-place) | O(log n) – recursion depth |
| Tree storage | O(n) – n nodes |
| Hash table | O(n) – n entries |

---

## Practical Considerations

1. **Constants matter**: O(n) with coefficient 0.1 faster than O(n log n) with coefficient 10 for small-medium n
2. **Cache behavior**: Theoretical O(n) can be slower than O(n log n) due to cache misses
3. **Implementation**: Python loops slower than NumPy vectorization even with same Big-O
4. **Problem size**: O(n²) acceptable for n < 10K; infeasible for n > 1M
5. **Trade-offs**: Sometimes O(n²) space saves O(n log n) time (memoization, lookup tables)

---

## Key Insight

Big-O describes asymptotic behavior (large n). For actual performance:
1. Profile your code (don't guess bottlenecks)
2. Understand theoretical complexity
3. Test on realistic input sizes
4. Consider constants and implementation details
5. Optimize where it matters most

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/array-searching]]
- [[programming-for-engineers/sorting-algorithms]]
- [[shared/array-operations]]
- [[deep-learning/course-overview]]
