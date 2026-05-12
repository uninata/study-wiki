# Visualization Techniques

**Summary**: Creating and displaying plots with matplotlib for understanding data, algorithm behavior, and communicating results across engineering and science applications.

**Course**: programming-for-engineers, deep-learning, computer-vision

**Sources**: l10_visualization.qmd

**Last updated**: 2026-04-19

---

## Overview

Visualization transforms data into visual form for understanding, debugging, and communication. Key use cases:
- **Debugging algorithms**: Plot convergence, show sorting steps
- **Data exploration**: Distributions, outliers, relationships
- **Reporting results**: Training curves, accuracy metrics, images

---

## Matplotlib Basics

Most widely-used plotting library in Python.

### Simple Plot

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 2*np.pi, 100)
y = np.sin(x)

plt.plot(x, y)
plt.xlabel('x')
plt.ylabel('sin(x)')
plt.title('Sine Wave')
plt.show()
```

### Multiple Lines

```python
x = np.linspace(0, 2*np.pi, 100)

plt.plot(x, np.sin(x), label='sin(x)', marker='o')
plt.plot(x, np.cos(x), label='cos(x)', marker='s')
plt.xlabel('x')
plt.ylabel('y')
plt.legend()
plt.grid(True)
plt.show()
```

### Subplots

```python
fig, axes = plt.subplots(2, 2, figsize=(10, 8))

axes[0, 0].plot(x, np.sin(x))
axes[0, 0].set_title('sin(x)')

axes[0, 1].plot(x, np.cos(x))
axes[0, 1].set_title('cos(x)')

axes[1, 0].scatter(x, np.sin(x) + np.random.normal(0, 0.1, len(x)))
axes[1, 0].set_title('sin(x) with noise')

axes[1, 1].bar(range(5), [1, 2, 3, 2, 1])
axes[1, 1].set_title('Bar chart')

plt.tight_layout()
plt.show()
```

---

## Common Plot Types

### Line Plot (Trends)

```python
plt.plot(x_values, y_values, marker='o', linestyle='-', linewidth=2)
# marker='o', 's', '^', '*', etc. for point style
# linestyle='-', '--', '-.', ':' for line style
```

**Use**: Algorithm convergence, time series, function behavior.

### Scatter Plot (Relationships)

```python
plt.scatter(x, y, s=100, alpha=0.6, c=color_values, cmap='viridis')
# s: point size
# alpha: transparency [0, 1]
# c: color by value
# cmap: colormap name
```

**Use**: Correlation analysis, clustering visualization, 2D data.

### Histogram (Distributions)

```python
plt.hist(data, bins=20, edgecolor='black')
# bins: number of bins or bin edges
```

**Use**: Understand data distribution, identify outliers.

### Bar Chart (Categories)

```python
categories = ['A', 'B', 'C']
values = [10, 24, 36]
plt.bar(categories, values)
```

**Use**: Compare values across categories, class frequencies.

### Image Display

```python
img = np.random.rand(100, 100)  # Grayscale image
plt.imshow(img, cmap='gray')
plt.colorbar()  # Add color scale
plt.axis('off')  # Hide axes
plt.show()
```

**Use**: Display images, heatmaps, matrix visualization.

### 3D Surface Plot

```python
from mpl_toolkits.mplot3d import Axes3D

fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')

x = np.linspace(-5, 5, 50)
y = np.linspace(-5, 5, 50)
X, Y = np.meshgrid(x, y)
Z = X**2 + Y**2

ax.plot_surface(X, Y, Z, cmap='viridis')
plt.show()
```

---

## Styling and Customization

```python
# Colors and styles
plt.plot(x, y, color='red', linewidth=2, label='data')

# Annotations
plt.annotate('Peak', xy=(max_x, max_y), xytext=(max_x+0.5, max_y+0.5),
            arrowprops=dict(arrowstyle='->'))

# Legends
plt.legend(loc='upper right', fontsize=12)

# Limits
plt.xlim(0, 10)
plt.ylim(-1, 1)

# Grid
plt.grid(True, alpha=0.3, linestyle='--')

# Figure size and DPI
plt.figure(figsize=(10, 6), dpi=100)
```

---

## Application: Algorithm Visualization

### Sorting Algorithm Steps

```python
import matplotlib.animation as animation

def visualize_sort(states):
    """Animate sorting process frame by frame."""
    fig, ax = plt.subplots()
    bars = ax.bar(range(len(states[0])), states[0])
    
    def animate(frame):
        for bar, val in zip(bars, states[frame]):
            bar.set_height(val)
        return bars
    
    anim = animation.FuncAnimation(fig, animate, frames=len(states),
                                 interval=50, blit=True)
    plt.show()

# Usage: record states during sort, then visualize
states = []
def bubble_sort_with_recording(arr):
    for i in range(len(arr)):
        for j in range(len(arr) - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
            states.append(arr.copy())

visualize_sort(states)
```

### Training Curves

```python
epoch_losses = [0.5, 0.4, 0.3, 0.28, 0.27]
epoch_accuracies = [0.7, 0.75, 0.8, 0.82, 0.83]

fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 4))

ax1.plot(epoch_losses, marker='o')
ax1.set_xlabel('Epoch')
ax1.set_ylabel('Loss')
ax1.set_title('Training Loss')
ax1.grid()

ax2.plot(epoch_accuracies, marker='o', color='green')
ax2.set_xlabel('Epoch')
ax2.set_ylabel('Accuracy')
ax2.set_title('Validation Accuracy')
ax2.grid()

plt.tight_layout()
plt.show()
```

---

## Terminal-Based Output

When graphical plots unavailable or unwanted:

### Progress Bars (tqdm)

```python
from tqdm import tqdm
import time

for i in tqdm(range(100), desc="Processing"):
    time.sleep(0.1)  # Simulate work
```

**Output**: `Processing |████████████░░░| 75% [75/100 00:15<00:05]`

### Formatted Tables (tabulate)

```python
from tabulate import tabulate

data = [['Algorithm', 'Time (ms)', 'Accuracy'],
        ['Linear Search', '0.5', '100%'],
        ['Binary Search', '0.001', '100%'],
        ['Naive Bayes', '10', '92%']]

print(tabulate(data, headers='firstrow', tablefmt='grid'))
```

**Output**:
```
┌──────────────────┬─────────────┬────────────┐
│ Algorithm        │ Time (ms)   │ Accuracy   │
├──────────────────┼─────────────┼────────────┤
│ Linear Search    │ 0.5         │ 100%       │
│ Binary Search    │ 0.001       │ 100%       │
│ Naive Bayes      │ 10          │ 92%        │
└──────────────────┴─────────────┴────────────┘
```

---

## Best Practices

1. **Label everything**: Axes, title, legend for clarity
2. **Meaningful colors**: Use colormaps for ordinal/quantitative data
3. **Resolution**: Save high-DPI for reports (300 dpi minimum)
4. **Consistency**: Keep styling consistent across plots in same report
5. **Simplicity**: Don't overwhelm; one idea per plot
6. **Context**: Include error bars, confidence intervals where relevant

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/sorting-algorithms]]
- [[programming-for-engineers/scientific-python-libraries]]
- [[programming-for-engineers/numpy-fundamentals]]
- [[deep-learning/course-overview]]
- [[computer-vision/course-overview]]
