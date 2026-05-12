# Array Processing: Statistics and Optimization

**Summary**: Statistical operations (min, max, mean, sum) and range query optimization using cumulative sum and integral images for 2D.

**Course**: programming-for-engineers

**Sources**: l2_matrices_processing.qmd

**Last updated**: 2026-04-19

---

## Simple Statistical Operations

### Minimum and Maximum

```python
def minimum(arr):
    if not arr:
        raise ValueError("Empty array")
    min_val = arr[0]
    for i in range(1, len(arr)):
        if arr[i] < min_val:
            min_val = arr[i]
    return min_val

def maximum(arr):
    if not arr:
        raise ValueError("Empty array")
    max_val = arr[0]
    for i in range(1, len(arr)):
        if arr[i] > max_val:
            max_val = arr[i]
    return max_val
```

**Complexity**: O(n) – must examine every element.

### Sum and Mean

```python
def arr_sum(arr):
    s = 0
    for val in arr:
        s += val
    return s

def mean(arr):
    return arr_sum(arr) / len(arr)
```

**Complexity**: O(n).

---

## Cumulative Sum (Prefix Sum)

**Problem**: Computing range sums repeatedly is expensive. `sum(arr[a:b+1])` is O(n) per query.

**Solution**: Precompute cumulative sums once (O(n)), then answer any range query in O(1).

### Concept

`cumsum[i]` = sum of all elements from index 0 to i (inclusive).

| index | 0 | 1 | 2 | 3 | 4 |
|-------|---|---|---|---|---|
| arr   | 4 | 2 | 0 | 8 | 1 |
| cumsum| 4 | 6 | 6 | 14| 15|

`cumsum[i] = arr[i] + cumsum[i-1]`

### Implementation

```python
def cumulative_sum(arr):
    if not arr:
        return []
    cs = [0] * len(arr)
    cs[0] = arr[0]
    for i in range(1, len(cs)):
        cs[i] = arr[i] + cs[i - 1]
    return cs
```

**Complexity**: O(n) to build, O(1) per query.

### Range Query

To get sum from index `a` to `b` (inclusive):
- **With cumsum**: `cumsum[b] - cumsum[a-1]` (O(1))
- **Without cumsum**: `sum(arr[a:b+1])` (O(n))

```python
def range_sum(cumsum_arr, start, end):
    if start == 0:
        return cumsum_arr[end]
    return cumsum_arr[end] - cumsum_arr[start - 1]

# Example
cs = cumulative_sum([4, 2, 0, 8, 1])
# Sum from index 2 to 4: 0 + 8 + 1 = 9
print(range_sum(cs, 2, 4))  # Output: 9
```

### Use Case: When Is It Worth It?

- **Few queries (1-10)**: Linear sum O(n) faster than building cumsum O(n)
- **Many queries (100+)**: Cumsum O(n) build + O(q) queries = O(n+q) << O(n*q)

### Range Average

```python
def range_average(cumsum_arr, start, end):
    return range_sum(cumsum_arr, start, end) / (end - start + 1)
```

---

## Integral Image (Summed-Area Table) – 2D Cumulative Sum

**Application**: Computer vision; compute rectangular sums in O(1) after O(n²) preprocessing.

### Building Integral Image

```python
def integral_image(matrix):
    rows, cols = len(matrix), len(matrix[0])
    ii = [[0] * cols for _ in range(rows)]
    
    for i in range(rows):
        for j in range(cols):
            ii[i][j] = matrix[i][j]
            if i > 0:
                ii[i][j] += ii[i-1][j]      # Add column cumsum
            if j > 0:
                ii[i][j] += ii[i][j-1]      # Add row cumsum
            if i > 0 and j > 0:
                ii[i][j] -= ii[i-1][j-1]    # Subtract overlap (counted twice)
    
    return ii
```

### Rectangle Sum Query

To sum rectangle from top-left `(a, b)` to bottom-right `(c, d)`:

$$\text{sum} = ii[c][d] - ii[a-1][d] - ii[c][b-1] + ii[a-1][b-1]$$

**Intuition**: 
1. Start with total sum to (c, d): `ii[c][d]`
2. Subtract everything above rectangle: `- ii[a-1][d]`
3. Subtract everything left of rectangle: `- ii[c][b-1]`
4. Add back overlap (subtracted twice): `+ ii[a-1][b-1]`

```python
def rectangle_sum(ii, top_left, bottom_right):
    a, b = top_left
    c, d = bottom_right
    
    result = ii[c][d]
    if a > 0:
        result -= ii[a-1][d]
    if b > 0:
        result -= ii[c][b-1]
    if a > 0 and b > 0:
        result += ii[a-1][b-1]
    
    return result
```

**Complexity**: O(1) per query after O(n²) preprocessing.

---

## Key Insights

1. **Trade-off**: Upfront preprocessing (cumsum, integral image) vs query efficiency
2. **Batch queries**: Only worth preprocessing if many queries on same data
3. **2D extension**: Integral images critical for image processing (Haar features, etc.)
4. **Generalization**: Cumsum/integral image principle applies to any associative operation (min, max, product via log)

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/matrices-and-arrays]]
- [[programming-for-engineers/array-searching]]
- [[shared/algorithmic-complexity]]
- [[shared/array-operations]]
