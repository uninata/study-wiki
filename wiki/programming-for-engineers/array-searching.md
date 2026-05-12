# Array Searching Algorithms

**Summary**: Search algorithms with different complexity trade-offs: linear O(n), binary O(log n), interpolation O(log log n) for specialized cases.

**Course**: programming-for-engineers

**Sources**: l2_matrices_processing.qmd

**Last updated**: 2026-04-19

---

## Linear Search

**Complexity**: O(n) worst-case, O(1) best-case (first element), O(n/2) average.

**Property**: Works on unsorted arrays; position-dependent runtime.

```python
def linear_search(arr, target):
    for i in range(len(arr)):
        if arr[i] == target:
            return i
    return None
```

**Performance variance**: Finding element at position 0 is fast (1 operation); finding at end or non-existent takes n operations.

---

## Binary Search

**Complexity**: O(log n) regardless of position; stable and predictable.

**Requirement**: Array must be sorted.

**Mechanism**: Divide-and-conquer by repeatedly halving search space.

### Iterative Implementation

```python
def binary_search_loop(arr, target):
    low, high = 0, len(arr) - 1
    while low <= high:
        mid = (low + high) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            low = mid + 1      # Search right half
        else:
            high = mid - 1     # Search left half
    return None
```

### Recursive Implementation

```python
def binary_search_recursive(arr, target):
    if len(arr) == 0:
        return None
    mid = len(arr) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        return binary_search_recursive(arr[mid + 1:], target)
    else:
        return binary_search_recursive(arr[:mid], target)
```

**Note**: Recursive version is slower due to array slicing (O(n) copies). Iterative version with index pointers preferred.

### Performance Comparison

For unsorted array (must sort first):
- **Binary search alone**: O(log n)
- **Sorting + binary search**: O(n log n) – slower than linear for single search
- **Many searches**: Sorting worth it if queries >> 1

---

## Interpolation Search

**Complexity**: O(log log n) on uniformly distributed arrays; O(n) worst-case on non-uniform.

**Requirement**: Sorted array with uniform value distribution.

**Mechanism**: Instead of splitting at midpoint, estimate position using linear interpolation: `mid = low + (target - arr[low]) * (high - low) / (arr[high] - arr[low])`

```python
def interpolation_search(arr, target):
    low, high = 0, len(arr) - 1
    while low <= high and arr[low] <= target <= arr[high]:
        if arr[high] == arr[low]:  # All remaining values identical
            return low if arr[low] == target else None
        
        # Educated guess of position
        mid = low + int(((target - arr[low]) * (high - low)) / 
                       (arr[high] - arr[low]))
        
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            low = mid + 1
        else:
            high = mid - 1
    return None
```

**Example**: In uniformly distributed [0, 1, 2, ..., 99], target 50 is guessed at position ~50 (1 step). In exponential [1, 2, 4, 8, 16, ...], same guess fails (O(n) iterations).

---

## Find All Occurrences

Return list of all indices matching target, not just first.

```python
def find_all(arr, target):
    indices = []
    for i in range(len(arr)):
        if arr[i] == target:
            indices.append(i)
    return indices

# Example
arr = [1, 2, 5, 2, 8, 2, 9]
occurrences = find_all(arr, 2)  # [1, 3, 5]
```

**Complexity**: Always O(n) regardless of search method, since every element must be examined.

---

## Key Insights

1. **Linear search**: Simple, O(n); fine for small arrays or unsorted data
2. **Binary search**: O(log n) but requires sorted array; worth it for many queries
3. **Interpolation search**: O(log log n) but only for uniformly distributed data; exponential/power-law distributions degrade to O(n)
4. **Position matters**: Linear search performance depends on target location
5. **Trade-off**: Sorting cost (O(n log n)) vs search benefit (O(log n) repeated)

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/matrices-and-arrays]]
- [[programming-for-engineers/array-processing-statistics]]
- [[shared/algorithmic-complexity]]
