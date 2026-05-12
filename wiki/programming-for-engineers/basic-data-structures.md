# Basic Data Structures

**Summary**: Python's built-in data structures (lists, dictionaries, tuples, sets) with creation, operations, and performance characteristics.

**Course**: programming-for-engineers

**Sources**: l1_intro_strctures.qmd

**Last updated**: 2026-04-19

---

## Overview

Python provides four fundamental collection types with different performance trade-offs. Choosing the right structure dramatically affects algorithm efficiency (source: programming-for-engineers/l1_intro_strctures.qmd).

---

## Lists

**Definition**: Ordered, mutable, untyped sequences. Implemented as dynamic arrays with O(1) append amortized, O(n) insertion/deletion.

### Creation & Initialization

```python
my_list = []                    # Empty list
my_list = [1, 2, 3]           # With values
my_list = [0] * 10            # Repeated element (initialization)
my_list = list(range(10))     # From iterable
my_list = [x*2 for x in range(5)]  # List comprehension
```

### Indexing & Slicing

- **Index**: Zero-based; negative indices count from end (`arr[-1]` is last element)
- **Slice syntax**: `list[start:end:step]` (end exclusive); omit start/end/step as needed
- **Reverse**: `list[::-1]` (slice with step -1)

### Operations

| Operation | Syntax | Complexity | Notes |
|-----------|--------|-----------|-------|
| Append | `list.append(x)` | O(1) amortized | Add to end |
| Insert | `list.insert(i, x)` | O(n) | Insert at position; shifts elements |
| Delete | `del list[i]` or `list.pop(i)` | O(n) | Remove at position |
| Pop end | `list.pop()` | O(1) | Remove last element |
| Extend | `list.extend(other)` | O(k) | Concatenate k elements |
| Index find | `list.index(x)` | O(n) | First occurrence |
| Count | `list.count(x)` | O(n) | Occurrences of x |
| Sort | `list.sort()` | O(n log n) | In-place sort |
| Reverse | `list.reverse()` | O(n) | In-place reverse |
| Copy | `list.copy()` | O(n) | Shallow copy |
| Membership | `x in list` | O(n) | Check if element exists |

### Shallow vs Deep Copy

```python
a = [[1, 2], [3, 4]]
b = a.copy()        # Shallow: inner lists still referenced
b[0][0] = 99        # Modifies original a!
from copy import deepcopy
c = deepcopy(a)     # Deep: fully independent
```

### Jagged and Heterogeneous Lists

Avoid when possible. Jagged lists (varying row lengths) complicate operations; multi-typed lists lose type information benefits.

---

## Dictionaries

**Definition**: Unordered key-value mappings. Hash table implementation enables O(1) average-case lookup, insertion, deletion.

### Creation & Access

```python
my_dict = {}                    # Empty
my_dict = {"a": 1, "b": 2}     # Initialization
my_dict["c"] = 3               # Insert/update
value = my_dict["a"]           # Direct access (KeyError if missing)
value = my_dict.get("a")       # Safe access (returns None if missing)
value = my_dict.get("a", -1)   # With default
```

### Operations

| Operation | Syntax | Complexity | Notes |
|-----------|--------|-----------|-------|
| Access | `dict[key]` | O(1) avg | Raises KeyError if missing |
| Safe access | `dict.get(key, default)` | O(1) avg | Returns default if missing |
| Insert/update | `dict[key] = value` | O(1) avg | |
| Delete | `del dict[key]` | O(1) avg | Raises KeyError if missing |
| Membership | `key in dict` | O(1) avg | Check key existence |
| Iteration | `for k, v in dict.items()` | O(n) | |

### Hash Implementation Details

- **Hash function**: Converts key to index via `hash(key) % table_size`
- **Collisions**: Multiple keys hash to same index; resolved via chaining or probing
- **Consistency**: Hash is consistent within session but not across Python runs (security feature)
- **Hashability**: Only immutable types hashable (int, str, tuple); lists, dicts not hashable

```python
my_dict = {(1, 2): "tuple_key", "str_key": 42}  # Tuple keys work
# my_dict[[1, 2]] = "fail"  # TypeError: unhashable type
```

---

## Tuples

**Definition**: Ordered, immutable sequences. Enable use as dict keys and safe parameter passing.

### Creation

```python
my_tuple = (1, 2, 3)       # With parentheses
my_tuple = 1, 2, 3         # Without parentheses
single = (1,)              # Single element: note comma!
single = 1,                # Also works
empty = ()                 # Empty tuple
```

### Key Differences from Lists

- **Immutable**: Cannot modify after creation; enables use as dict keys
- **Faster**: Slightly faster for iteration due to fixed size
- **Unpacking**: `a, b = (1, 2)` assigns values
- **Function returns**: Used for returning multiple values

```python
def return_multiple():
    return 1, 2, 3  # Returns tuple

a, b, c = return_multiple()  # Unpacking
```

---

## Sets

**Definition**: Unordered collections of unique elements. Hash-set implementation enables O(1) membership testing.

### Creation

```python
my_set = {1, 2, 3}         # With braces (not {})
my_set = set([1, 2, 3])    # From iterable
empty = set()              # Empty set (not {})
```

### Key Properties

- **Uniqueness**: Idempotent addition; `set.add(x)` repeated doesn't duplicate
- **Unordered**: No indexing; iteration order undefined
- **Fast membership**: O(1) average case vs O(n) for lists

### Operations

| Operation | Syntax | Result |
|-----------|--------|--------|
| Union | `set_a \| set_b` | All elements from both |
| Intersection | `set_a & set_b` | Elements in both |
| Difference | `set_a - set_b` | Elements in a but not b |
| Symmetric diff | `set_a ^ set_b` | Elements in either but not both |

---

## Type Checking

### Functions

```python
type(obj)              # Returns type class
isinstance(obj, int)   # Check if instance of type (preferred)
issubclass(MyClass, BaseClass)  # Check inheritance
```

### Assertions & Validation

```python
# For debugging (not production):
assert isinstance(x, (int, float)), f"x must be number, got {type(x)}"

# For production:
def safe_divide(a, b):
    if not isinstance(a, (int, float)) or not isinstance(b, (int, float)):
        raise TypeError(f"Numbers required, got {type(a)}, {type(b)}")
    if b == 0:
        raise ValueError("Division by zero")
    return a / b
```

---

## Performance Comparison

| Operation | List | Dict | Set | Tuple |
|-----------|------|------|-----|-------|
| Access element | O(n) | O(1) | — | O(n) |
| Membership test | O(n) | O(1) | O(1) | O(n) |
| Insert element | O(n) | O(1) | O(1) | — |
| Delete element | O(n) | O(1) | O(1) | — |
| Iteration | O(n) | O(n) | O(n) | O(n) |
| Space | O(n) | O(n) | O(n) | O(n) |

**Rule of thumb**: Use dicts/sets for O(1) lookup; lists for ordered sequences; tuples for immutable/hashable collections.

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/matrices-and-arrays]]
- [[shared/algorithmic-complexity]]
- [[shared/array-operations]]
