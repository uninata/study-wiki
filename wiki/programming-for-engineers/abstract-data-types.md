# Abstract Data Types: Stacks, Queues, Trees

**Summary**: Abstract data type definitions (what operations available) with concrete implementations; includes stacks, queues, and tree structures with OOP fundamentals.

**Course**: programming-for-engineers

**Sources**: l6_adt.qmd

**Last updated**: 2026-04-19

---

## Abstract Data Types (ADT) Concept

**Definition**: Specification of what operations are available without specifying implementation details.

**Benefits**: 
- Abstraction and modularity (change implementation without changing interface)
- Easier reasoning about correctness
- Ability to swap implementations (array vs linked-list stack)

---

## Object-Oriented Programming Basics

### Classes

```python
class MyAwesomeClass:
    def __init__(self, a: int, b: str) -> None:
        """Constructor: called when instance created."""
        self.a = a          # Public attribute (readable/writable)
        self._b = b         # Protected (internal use + subclasses)
        self.__c = False    # Private (class internal only)

    def public_method(self) -> int:
        """Public method."""
        return self.a

    def _protected_method(self) -> None:
        """Protected method (convention: not for external use)."""
        self.a += 1

    def __private_method(self) -> None:
        """Private method (name mangling enforces privacy)."""
        pass

    def __len__(self) -> int:
        """Magic method: len(obj) calls this."""
        return self.a

    @property
    def b(self) -> str:
        """Property getter: read as attribute, not method."""
        return self._b

    @b.setter
    def b(self, value: str) -> None:
        """Property setter: write as attribute, not method."""
        self._b = value
```

**Access Levels**:
- **Public** (no prefix): Accessible everywhere
- **Protected** (_prefix): Intended for class and subclasses
- **Private** (__prefix): Name-mangled; inaccessible from outside

### Magic Methods

Enable natural syntax:

```python
class Container:
    def __init__(self):
        self.items = []
    
    def __len__(self):
        return len(self.items)      # len(obj)
    
    def __getitem__(self, idx):
        return self.items[idx]      # obj[idx]
    
    def __setitem__(self, idx, val):
        self.items[idx] = val       # obj[idx] = val
    
    def __contains__(self, item):
        return item in self.items   # item in obj
    
    def __iter__(self):
        return iter(self.items)     # for x in obj:
```

---

## Stack (LIFO)

**ADT Definition**: 
- `push(x)` — add element
- `pop()` — remove and return last element
- `peek()` — return last without removing
- `is_empty()` — check if empty

**Implementation**:

```python
class Stack:
    def __init__(self):
        self._items = []
    
    def push(self, item):
        self._items.append(item)
    
    def pop(self):
        if self.is_empty():
            raise IndexError("pop from empty stack")
        return self._items.pop()
    
    def peek(self):
        if self.is_empty():
            raise IndexError("peek at empty stack")
        return self._items[-1]
    
    def is_empty(self):
        return len(self._items) == 0
    
    def __len__(self):
        return len(self._items)
```

**Applications**: Function call stack, expression evaluation, undo/redo, bracket matching.

---

## Queue (FIFO)

**ADT Definition**:
- `enqueue(x)` — add element to back
- `dequeue()` — remove and return from front
- `peek()` — return front without removing
- `is_empty()` — check if empty

**Implementation**:

```python
from collections import deque

class Queue:
    def __init__(self):
        self._items = deque()
    
    def enqueue(self, item):
        self._items.append(item)
    
    def dequeue(self):
        if self.is_empty():
            raise IndexError("dequeue from empty queue")
        return self._items.popleft()
    
    def peek(self):
        if self.is_empty():
            raise IndexError("peek at empty queue")
        return self._items[0]
    
    def is_empty(self):
        return len(self._items) == 0
    
    def __len__(self):
        return len(self._items)
```

**Note**: Use `deque` for O(1) front removal; Python lists have O(n) popleft.

**Applications**: Task scheduling, BFS traversal, printer jobs, message queues.

---

## Binary Tree

**Components**:
- **Node**: Contains value and pointers to left/right children
- **Root**: Top node (no parent)
- **Leaf**: Node with no children
- **Parent, child, sibling**: Relationships

**Implementation**:

```python
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

class BinaryTree:
    def __init__(self, root_value):
        self.root = BinaryTreeNode(root_value)
    
    def insert_left(self, parent_node, value):
        if parent_node.left is None:
            parent_node.left = BinaryTreeNode(value)
        else:
            raise ValueError("Left child already exists")
    
    def insert_right(self, parent_node, value):
        if parent_node.right is None:
            parent_node.right = BinaryTreeNode(value)
        else:
            raise ValueError("Right child already exists")
```

**Traversal Methods**:

```python
def preorder(node):
    """Root, then left, then right."""
    if node is None:
        return
    print(node.value)
    preorder(node.left)
    preorder(node.right)

def inorder(node):
    """Left, root, then right."""
    if node is None:
        return
    inorder(node.left)
    print(node.value)
    inorder(node.right)

def postorder(node):
    """Left, right, then root."""
    if node is None:
        return
    postorder(node.left)
    postorder(node.right)
    print(node.value)
```

---

## Binary Search Tree (BST)

**Property**: Left subtree < node < right subtree.

**Complexity**:
- **Balanced**: O(log n) search, insert, delete
- **Degenerate**: O(n) (like sorted list)

**Implementation**:

```python
class BSTNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def bst_search(node, target):
    if node is None:
        return False
    if node.value == target:
        return True
    elif target < node.value:
        return bst_search(node.left, target)
    else:
        return bst_search(node.right, target)

def bst_insert(node, value):
    if node is None:
        return BSTNode(value)
    if value < node.value:
        node.left = bst_insert(node.left, value)
    elif value > node.value:
        node.right = bst_insert(node.right, value)
    return node
```

---

## Tree Variants

- **AVL Tree**: Self-balancing; O(log n) guaranteed
- **Red-Black Tree**: Self-balancing; weaker balance than AVL
- **B-Tree**: Multiple children; used in databases
- **Heap**: Min/max priority queue
- **Trie**: String prefix tree for autocomplete
- **Segment Tree**: Range queries/updates in O(log n)

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/basic-data-structures]]
- [[programming-for-engineers/recursion-and-trees]]
- [[shared/algorithmic-complexity]]
