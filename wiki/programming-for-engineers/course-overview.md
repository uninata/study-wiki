# Programming for Engineers - Course Overview

**Summary**: Overview of the Programming for Engineers course structure, learning objectives, curriculum covering Python fundamentals through scientific computing.

**Course**: programming-for-engineers

**Sources**: l1_intro_strctures.qmd, l2_matrices_processing.qmd, l3_strings_io.qmd, l4_numpy_arrays.qmd, l5_array_processing.qmd, l6_adt.qmd, l7_recursion.qmd, l8_recursion.qmd, l9_scientific_python_proc.qmd, l10_visualization.qmd, l11_algorithmic_complexity.qmd

**Last updated**: 2026-04-19

---

## Course Structure

Programming for Engineers is an 11-lecture practical Python course taught by Radoslav Škoviera, covering fundamentals from basic data structures through scientific computing libraries. The course emphasizes engineering applications and understanding computational trade-offs.

## Learning Objectives

By course completion, students can:
- Work with fundamental Python data structures (lists, dicts, tuples, sets)
- Design and analyze algorithms for correctness and efficiency
- Implement recursive solutions and tree data structures
- Use NumPy, SciPy, Pandas for scientific computing
- Create visualizations and output formatted data
- Apply object-oriented programming principles

## Curriculum (11 Lectures)

| # | Topic | Key Concepts |
|---|-------|--------------|
| 1 | Basic Data Structures & Complexity | Lists, dicts, tuples, sets, type checking, O(n) analysis |
| 2 | Matrices & Array Processing | 2D arrays, search algorithms (linear/binary/interpolation), cumulative sum, integral image |
| 3 | Strings and I/O | String operations, immutability, file handling, Unicode |
| 4 | NumPy | Array creation, indexing, slicing, reshaping, broadcasting, aggregation |
| 5 | Sorting Algorithms | Bubble sort, quick sort, merge sort, algorithm visualization, complexity comparison |
| 6 | Abstract Data Types & OOP | Stacks, queues, trees, classes, magic methods, encapsulation |
| 7-8 | Recursion & Trees | Recursion patterns, binary trees, BST, tree traversal (preorder, inorder, postorder) |
| 9 | Scientific Python | NumPy, SciPy, Pandas, scikit-learn, scikit-image, data loading |
| 10 | Visualization & Output | matplotlib, tqdm, Rich, tabulate, logging, progress tracking |
| 11 | Algorithmic Complexity | Operation counting, empirical runtime measurement, complexity profiling |

## Course Philosophy

**Engineering Focus**: Practical tool usage for solving real problems, not just algorithms in isolation.

**Trade-off Analysis**: When should you use what? Lists vs dicts (O(n) vs O(1)), arrays vs lists (memory vs flexibility), sorting vs searching.

**Empirical Validation**: Theoretical O(n) complexity claims verified via profiling and visualization.

**Professionalism**: Proper error handling, logging, visualization for communication, and understanding limitations of tools.

## Prerequisites

- Basic programming experience (variables, loops, conditionals)
- No prior Python required (course covers from basics)
- Mathematical comfort with logarithms and exponential notation

## Tools & Libraries

**Core**: Python 3.x, Jupyter/Quarto notebooks

**Scientific Stack**: NumPy, SciPy, Pandas, scikit-learn, scikit-image

**Visualization**: matplotlib, plotly, graphviz, networkx, seaborn

**Utilities**: tqdm (progress), Rich (formatting), tabulate (tables), logging (messages)

## Key Insights

1. **Data structure selection matters**: O(1) dict lookup vs O(n) list search for membership testing
2. **Asymptotic complexity is useful but not everything**: Constant factors, cache behavior, and implementation overhead matter in practice
3. **Vectorization wins**: NumPy arrays 10-100x faster than Python loops for large data
4. **Visualization is essential**: Plots reveal algorithm behavior better than raw numbers
5. **Right tool for the job**: NumPy for arrays, Pandas for tabular data, SciPy for advanced math
6. **Profiling before optimizing**: Benchmark to know what actually matters; common bottlenecks often surprise

---

## Related pages

- [[programming-for-engineers/basic-data-structures]]
- [[programming-for-engineers/matrices-and-arrays]]
- [[programming-for-engineers/array-searching]]
- [[programming-for-engineers/array-processing-statistics]]
- [[programming-for-engineers/strings-and-io]]
- [[programming-for-engineers/numpy-fundamentals]]
- [[programming-for-engineers/sorting-algorithms]]
- [[programming-for-engineers/abstract-data-types]]
- [[programming-for-engineers/recursion-and-trees]]
- [[programming-for-engineers/scientific-python-libraries]]
- [[shared/algorithmic-complexity]]
- [[shared/array-operations]]
- [[shared/visualization-techniques]]
