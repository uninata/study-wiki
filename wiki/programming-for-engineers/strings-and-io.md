# Strings and File I/O

**Summary**: Python string operations, immutability implications, file handling, and text processing for engineering applications.

**Course**: programming-for-engineers

**Sources**: l3_strings_io.qmd

**Last updated**: 2026-04-19

---

## String Basics

**Definition**: Immutable Unicode text sequences. Indexing returns a 1-character string, not a separate char type.

### Creation

```python
s = ""                      # Empty string
s = "hello"                 # Double or single quotes equivalent
s = """multi-line
string"""                   # Triple quotes preserve newlines
s = "string" + "concat"     # Concatenation
s = "repeat " * 3           # "repeat repeat repeat "
```

### Immutability

```python
s = "hello"
s[0] = 'H'  # TypeError: 'str' object does not support item assignment

# To "change" a string, create a new one:
s = "H" + s[1:]  # Creates new string "Hello"
```

**Implication**: Operations like concatenation, replace, upper create new strings. Repeated mutations in loops are inefficient.

---

## String Operations

### Character Access & Slicing

```python
s = "python"
s[0]        # "p"
s[-1]       # "n" (last character)
s[1:4]      # "yth" (indices 1-3)
s[::2]      # "pto" (every 2nd character)
s[::-1]     # "nohtyp" (reversed)
```

### Common Methods

```python
s = "Hello World"

# Case
s.upper()           # "HELLO WORLD"
s.lower()           # "hello world"

# Searching
s.find("World")     # 6 (index of substring, -1 if not found)
s.index("World")    # 6 (index, raises ValueError if not found)
s.count("l")        # 3 (occurrences)
s.startswith("H")   # True
s.endswith("d")     # True

# Modification
s.replace("World", "Python")  # "Hello Python"
s.strip()                     # Remove leading/trailing whitespace
s.split()                     # ["Hello", "World"] (split by whitespace)
s.split(",")                  # Split by delimiter

# Joining
"-".join(["a", "b", "c"])     # "a-b-c"

# Type checking
"123".isdigit()               # True
"abc".isalpha()               # True
"abc123".isalnum()            # True
```

### Formatting

```python
# f-strings (Python 3.6+, preferred)
name, age = "Alice", 30
f"Name: {name}, Age: {age}"      # "Name: Alice, Age: 30"
f"{value:.2f}"                   # Format float to 2 decimals
f"{text:>10}"                    # Right-align in 10-char field

# .format()
"Hello, {}!".format("World")     # "Hello, World!"
"{0} {1} {0}".format("a", "b")   # "a b a"

# % formatting (legacy, not recommended)
"Hello, %s" % ("World",)         # "Hello, World"
```

---

## File I/O

### Reading Files

```python
# Read entire file as string
with open("file.txt", "r") as f:
    content = f.read()

# Read line by line
with open("file.txt", "r") as f:
    for line in f:
        print(line.strip())  # .strip() removes trailing newline

# Read all lines as list
with open("file.txt", "r") as f:
    lines = f.readlines()
```

### Writing Files

```python
# Write (overwrites existing)
with open("file.txt", "w") as f:
    f.write("Hello, World!\n")
    f.write("Second line\n")

# Append (adds to existing)
with open("file.txt", "a") as f:
    f.write("Appended line\n")
```

### Context Manager (`with` statement)

The `with` statement ensures files are properly closed even if errors occur:

```python
# Bad: file not closed on error
f = open("file.txt", "r")
content = f.read()
f.close()

# Good: file closed automatically
with open("file.txt", "r") as f:
    content = f.read()
```

### Binary Files

```python
# Read binary
with open("image.png", "rb") as f:
    data = f.read()

# Write binary
with open("output.bin", "wb") as f:
    f.write(data)
```

---

## Performance Considerations

### String Concatenation

```python
# Inefficient (creates new string each iteration)
result = ""
for word in ["a", "b", "c", "d"]:
    result += word + ", "
# O(n²) due to repeated copying

# Efficient (builds once)
result = ", ".join(["a", "b", "c", "d"])
# O(n)
```

### Substring Searching

```python
# Optimized methods are faster than manual loops
s = "a" * 1000000
target = "x"

# Fast (CPython optimized)
if target in s:
    idx = s.find(target)

# Slow (manual loop)
for i in range(len(s)):
    if s[i:i+len(target)] == target:
        break
```

### Memory Efficiency

CPython stores strings using smallest possible internal format (1/2/4 bytes per character based on max Unicode value). ASCII-only strings more efficient than mixed Unicode.

---

## Unicode & Encoding

```python
# Unicode escape sequences
s = "Hello \u4e16\u754c"  # Hello 世界

# String encoding to bytes
data = "Hello".encode("utf-8")          # b'Hello'
data = "Hello 世界".encode("utf-8")     # Multi-byte characters

# Bytes decoding to string
s = b'Hello'.decode("utf-8")
s = b'Hello \xe4\xb8\x96\xe7\x95\x8c'.decode("utf-8")
```

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/numpy-fundamentals]]
- [[programming-for-engineers/scientific-python-libraries]]
