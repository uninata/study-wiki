# Sorting Algorithms

**Summary**: Comparison-based sorting with varying complexity trade-offs: bubble O(n²), quick O(n log n) average, merge O(n log n) guaranteed; visualization and algorithm analysis.

**Course**: programming-for-engineers

**Sources**: l5_array_processing.qmd

**Last updated**: 2026-04-19

---

## Overview

Sorting is a fundamental algorithm with practical importance. Different algorithms excel under different conditions:
- **Bubble sort**: Simple, O(n²), in-place
- **Quick sort**: Fast average O(n log n), O(n²) worst-case, in-place
- **Merge sort**: Guaranteed O(n log n), stable, requires O(n) extra space

---

## Bubble Sort

**Mechanism**: Repeatedly swap adjacent out-of-order elements until sorted.

**Complexity**: O(n²) best, average, and worst-case (always n² comparisons).

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(n - i - 1):  # Shrink search range
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
    return arr
```

**Why it's slow**: Every element must bubble to correct position; no mechanism to skip already-sorted regions.

---

## Quick Sort

**Mechanism**: Divide-and-conquer via partitioning around pivot.

**Complexity**: O(n log n) average, O(n²) worst-case (unbalanced partitions).

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    
    pivot = arr[0]
    left = [x for x in arr[1:] if x < pivot]
    right = [x for x in arr[1:] if x >= pivot]
    
    return quick_sort(left) + [pivot] + quick_sort(right)
```

**Advantage**: Fast in practice; average case dominates; good cache locality.

**Disadvantage**: Quadratic worst-case; not stable; recursive overhead.

**Optimization**: Randomize pivot selection to avoid O(n²) on sorted input.

---

## Merge Sort

**Mechanism**: Divide array in half, recursively sort, merge sorted halves.

**Complexity**: O(n log n) guaranteed (best, average, worst).

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

**Advantage**: Guaranteed O(n log n); stable; predictable performance.

**Disadvantage**: O(n) extra space; not in-place.

---

## Complexity Comparison

| Algorithm | Best | Average | Worst | Space | Stable |
|-----------|------|---------|-------|-------|--------|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |

---

## Algorithm Visualization

Using `yield` for step-by-step observation:

```python
def bubble_sort_visualized(arr):
    n = len(arr)
    for i in range(n):
        for j in range(n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
            yield arr.copy()  # Show state after each swap
```

This enables animation and analysis of algorithm behavior on specific inputs.

---

## Practical Considerations

1. **Python built-in**: `sorted()` uses Timsort (hybrid of merge + insertion sort); optimized for real data
2. **In-place vs copy**: Quick sort is in-place; merge sort requires copy
3. **Stability**: Preserving relative order of equal elements (matters for multi-key sorts)
4. **Small inputs**: Insertion sort faster for n < 50 due to low overhead
5. **Nearly sorted**: Bubble with early termination, insertion sort effective

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/array-searching]]
- [[shared/algorithmic-complexity]]
- [[programming-for-engineers/scientific-python-libraries]]
