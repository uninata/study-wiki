# Scientific Python Libraries

**Summary**: Overview of scientific computing ecosystem: NumPy for arrays, SciPy for advanced math, Pandas for tabular data, scikit-learn for ML preprocessing, scikit-image for image processing.

**Course**: programming-for-engineers

**Sources**: l9_scientific_python_proc.qmd

**Last updated**: 2026-04-19

---

## Scientific Python Stack

The scientific Python ecosystem builds in layers:

```
Applications (domain-specific)
  ↑
  scikit-learn (ML) | scikit-image (images) | Pandas (data)
  ↑
  SciPy (advanced math/optimization)
  ↑
  NumPy (arrays & vectorization)
  ↑
  Python
```

---

## NumPy: Numerical Arrays

**Purpose**: Foundational array library with C-level performance.

**Key features**:
- Typed, homogeneous n-dimensional arrays
- Vectorization (parallel operations across elements)
- Broadcasting for automatic dimension expansion
- Integration with C/Fortran code

```python
import numpy as np
a = np.arange(1000000)
b = a * 2 + 1  # Vectorized: 100x+ faster than Python loop
```

See [[programming-for-engineers/numpy-fundamentals]] for details.

---

## SciPy: Scientific Computing

**Purpose**: High-level algorithms built on NumPy.

**Modules**:
- **Optimization**: minimize, maximize, least squares
- **Integration**: Numerical integration (quad, odeint)
- **Interpolation**: Spline fitting, griddata
- **Eigenvalue problems**: eig, svd
- **Algebraic equations**: Solving systems
- **Statistics**: pdf, cdf, random variables
- **Signal processing**: Filtering, spectral analysis
- **Sparse matrices**: Efficient large sparse arrays

```python
from scipy.optimize import minimize
result = minimize(lambda x: (x - 5) ** 2, x0=0)
# result.x ≈ 5

from scipy.integrate import odeint
def model(y, t):
    return -y
t = np.linspace(0, 10, 100)
y = odeint(model, y0=1, t=t)
```

---

## Pandas: Tabular Data

**Purpose**: Data manipulation and analysis with labeled structures.

**Key structures**:
- **Series**: 1D labeled array (like dict)
- **DataFrame**: 2D table with named columns and index

```python
import pandas as pd

# Read CSV
df = pd.read_csv('data.csv', parse_dates=['date'])
df = pd.read_excel('data.xlsx')

# Create DataFrame
df = pd.DataFrame({'A': [1, 2], 'B': [3, 4]})

# Access
df['A']              # Column as Series
df.loc[0]            # Row 0
df.iloc[0, 1]        # Row 0, col 1

# Operations
df.sum()             # Sum each column
df.groupby('category').mean()  # Group and aggregate
df.dropna()          # Remove missing values
df.merge(other_df)   # SQL-like join
```

**Advantages**:
- Named columns vs NumPy numeric indices
- Mixed dtypes per column (unlike NumPy homogeneity)
- Built-in I/O (CSV, Excel, SQL)
- Time series support
- Groupby/aggregation

---

## scikit-learn: Machine Learning

**Purpose**: ML pipeline for classification, regression, clustering.

**Main components**:
- **Preprocessing**: Scaling, encoding, imputation
- **Supervised learning**: Classifiers, regressors
- **Unsupervised learning**: K-means, DBSCAN, PCA
- **Model selection**: Cross-validation, grid search
- **Metrics**: Accuracy, precision, recall, F1

```python
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import cross_val_score

# Standardize features
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# Train classifier
clf = RandomForestClassifier()
scores = cross_val_score(clf, X_scaled, y, cv=5)
print(f"Mean accuracy: {scores.mean():.2f}")
```

---

## scikit-image: Image Processing

**Purpose**: Image analysis algorithms (filtering, morphology, feature detection).

**Key modules**:
- **Filters**: Gaussian blur, edge detection (Sobel, Canny)
- **Morphology**: Erosion, dilation, skeleton
- **Feature detection**: Corners (Harris), blobs, edges
- **Segmentation**: Thresholding, watershed
- **Geometry**: Transforms, rotation, warping
- **Color**: Color space conversion (RGB↔HSV)

```python
from skimage import io, filters, feature
from skimage.transform import rotate

# Load and process image
img = io.imread('image.png')
blurred = filters.gaussian(img, sigma=2)
edges = feature.canny(blurred)
rotated = rotate(img, 45)

# Display
import matplotlib.pyplot as plt
plt.imshow(edges, cmap='gray')
```

---

## Data Loading Patterns

### NumPy (Text Files)

```python
# Load CSV/text
data = np.loadtxt('data.csv', delimiter=',')

# Save array
np.savetxt('output.csv', data, delimiter=',')

# Binary (fast for large arrays)
np.save('array.npy', data)
data = np.load('array.npy')
```

### Pandas (Tabular)

```python
# CSV
df = pd.read_csv('file.csv')
df.to_csv('output.csv')

# Excel (requires openpyxl)
df = pd.read_excel('file.xlsx', sheet_name='Sheet1')
df.to_excel('output.xlsx')

# SQL (requires database driver)
import sqlalchemy
conn = sqlalchemy.create_engine('sqlite:///db.sqlite')
df = pd.read_sql('SELECT * FROM table', conn)
```

### Image (scikit-image)

```python
from skimage import io
img = io.imread('image.png')       # NumPy array
io.imshow(img)
io.imsave('output.png', img)
```

---

## Integration and Workflows

Typical data science pipeline:

```
Data (CSV) 
  ↓ [Pandas]
Load & clean
  ↓ [NumPy/Pandas]
Preprocess (scale, encode)
  ↓ [scikit-learn]
Train ML model
  ↓ [NumPy]
Evaluate metrics
  ↓ [Matplotlib/Seaborn]
Visualize results
```

---

## Performance Tips

1. **Vectorize**: Replace loops with NumPy operations (10-100x faster)
2. **Use appropriate types**: `float32` vs `float64`, `int8` vs `int32`
3. **Avoid copies**: Use views where possible; in-place operations
4. **Batch operations**: Process multiple samples simultaneously
5. **Profile before optimizing**: Use `timeit`, `cProfile` to find bottlenecks

---

## Related pages

- [[programming-for-engineers/course-overview]]
- [[programming-for-engineers/numpy-fundamentals]]
- [[programming-for-engineers/strings-and-io]]
- [[shared/visualization-techniques]]
- [[shared/array-operations]]
