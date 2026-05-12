# Recursion and Trees

**Summary**: Recursion fundamentals with base cases, recursive structures, and tree traversal patterns; binary search trees and specialized tree types.

**Course**: programming-for-engineers

**Sources**: l7_recursion.qmd, l8_recursion.qmd

**Last updated**: 2026-04-19

---

## Recursion Fundamentals

**Definition**: Function that calls itself, reducing problem toward base case.

### Components

**Base case**: Trivial case with known solution; stops recursion.

**Recursive case**: Reduces problem size and calls itself.

```python
def factorial(n):
    if n == 0:              # Base case
        return 1
    return n * factorial(n - 1)  # Recursive case
```

### Simple Examples

**Countdown**:
```python
def countdown(n):
    if n == 0:
        return
    print(n)
    countdown(n - 1)

countdown(5)  # Prints 5, 4, 3, 2, 1
```

**Sum**:
```python
def sum_to_n(n):
    if n == 0:
        return 0
    return n + sum_to_n(n - 1)

sum_to_n(5)  # Returns 15 (5+4+3+2+1+0)
```

### Call Stack Visualization

```python
sum_to_n(3)
  3 + sum_to_n(2)
    3 + 2 + sum_to_n(1)
      3 + 2 + 1 + sum_to_n(0)
        3 + 2 + 1 + 0  # Base case returns 0
      3 + 2 + 1 + 0 = 6
    3 + 2 + 6 = 11
  3 + 11 = 14
```

Each function call takes memory (stack space). Deep recursion can cause stack overflow.

---

## Recursion Order: Pre-order vs Post-order

### Pre-order (before recursive call)

```python
def preorder_print(n, depth=""):
    if n == 0:
        return
    print(f"{depth}{n}")  # Do work BEFORE recursing
    preorder_print(n - 1, depth + "  ")

preorder_print(3)
# Output:
# 3
#   2
#     1
```

### Post-order (after recursive call)

```python
def postorder_print(n, depth=""):
    if n == 0:
        return
    postorder_print(n - 1, depth + "  ")
    print(f"{depth}{n}")  # Do work AFTER recursing

postorder_print(3)
# Output:
#     1
#   2
# 3
```

---

## Tree Traversal Patterns

### Preorder (Root, Left, Right)

Visit node, then recurse left, then right.

```python
def preorder(node):
    if node is None:
        return
    print(node.value)      # Process node
    preorder(node.left)    # Left subtree
    preorder(node.right)   # Right subtree
```

**Use case**: Copy tree, prefix notation.

### Inorder (Left, Root, Right)

Recurse left, visit node, then recurse right.

```python
def inorder(node):
    if node is None:
        return
    inorder(node.left)     # Left subtree
    print(node.value)      # Process node
    inorder(node.right)    # Right subtree
```

**Use case**: BST inorder gives sorted sequence; infix notation.

### Postorder (Left, Right, Root)

Recurse left, recurse right, then visit node.

```python
def postorder(node):
    if node is None:
        return
    postorder(node.left)   # Left subtree
    postorder(node.right)  # Right subtree
    print(node.value)      # Process node
```

**Use case**: Delete tree (must delete children before parent); postfix notation.

---

## Binary Search Trees

**Property**: Left < Node < Right.

**Complexity**:
- **Balanced tree**: O(log n) search, insert, delete
- **Sorted list (degenerate BST)**: O(n) search

### Search

```python
def bst_search(node, target):
    if node is None:
        return False
    if node.value == target:
        return True
    elif target < node.value:
        return bst_search(node.left, target)
    else:
        return bst_search(node.right, target)
```

### Insertion

```python
def bst_insert(node, value):
    if node is None:
        return BSTNode(value)
    if value < node.value:
        node.left = bst_insert(node.left, value)
    elif value > node.value:
        node.right = bst_insert(node.right, value)
    return node
```

### Deletion

```python
def bst_delete(node, value):
    if node is None:
        return None
    
    if value < node.value:
        node.left = bst_delete(node.left, value)
    elif value > node.value:
        node.right = bst_delete(node.right, value)
    else:  # Found node to delete
        # No children
        if node.left is None and node.right is None:
            return None
        # One child
        if node.left is None:
            return node.right
        if node.right is None:
            return node.left
        # Two children: find in-order successor
        successor = find_min(node.right)
        node.value = successor.value
        node.right = bst_delete(node.right, successor.value)
    
    return node
```

---

## Tree Variants

### AVL Tree
Self-balancing; height difference ≤ 1; O(log n) guaranteed operations via rotations.

### Red-Black Tree
Self-balancing; weaker invariant than AVL but faster insertions.

### B-Tree
Multiple children per node (m-way tree); used in databases and file systems for I/O efficiency.

### Trie (Prefix Tree)
String-specific tree; each node has 26 children (for letters); O(m) search for word of length m.

### Heap
Complete binary tree; min/max property; parent ≤ children (min-heap) or ≥ (max-heap); O(1) access to min/max, O(log n) insertion/deletion.

---

## Common Pitfalls

1. **Missing base case**: Infinite recursion → stack overflow
2. **Not reducing problem**: Recursive call doesn't make progress
3. **Inefficient subproblems**: Solving same subproblem repeatedly (use memoization)
4. **Deep recursion on large input**: Stack limited; iterative or tail-recursion optimization preferred

---

## Efficiency Note

Recursion has function call overhead (stack frame allocation). For heavily-used algorithms (factorial, Fibonacci), iterative solutions faster. Recursion shines for tree/graph problems where structure naturally recursive.

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/abstract-data-types]]
- [[programming-for-engineers/basic-data-structures]]
- [[shared/algorithmic-complexity]]
