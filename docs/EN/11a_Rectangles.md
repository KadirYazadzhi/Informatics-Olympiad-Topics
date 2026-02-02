# 📐 Rectangles and Coordinate Geometry

Tasks involving rectangles whose sides are parallel to the coordinate axes (Axis-Aligned Bounding Box - AABB) are extremely common. They are the basis for more complex algorithms such as Sweep-Line and Segment Trees.

## 1. Representation

The most convenient way to store a rectangle is by using the coordinates of its lower-left $(x_1, y_1)$ and upper-right $(x_2, y_2)$ vertices.
Important: Ensure that $x_1 \le x_2$ and $y_1 \le y_2$.

```cpp
struct Rect {
    long long x1, y1, x2, y2; // Use long long to avoid overflow in area calculations

    long long width() const { return max(0LL, x2 - x1); } 
    long long height() const { return max(0LL, y2 - y1); } 
    long long area() const { return width() * height(); }
};
```

## 2. Basic Operations

### 2.1. Intersection
The intersection of two rectangles $A$ and $B$ is also a rectangle (or an empty set).
Its coordinates are obtained by taking the "inner" boundaries: `max` of the left/bottom boundaries and `min` of the right/top ones.

```cpp
Rect intersect(const Rect& a, const Rect& b) {
    Rect res;
    res.x1 = max(a.x1, b.x1);
    res.y1 = max(a.y1, b.y1);
    res.x2 = min(a.x2, b.x2);
    res.y2 = min(a.y2, b.y2);
    
    // If invalid (with negative sides), return zero-size
    if (res.x1 >= res.x2 || res.y1 >= res.y2) 
        return {0, 0, 0, 0};
        
    return res;
}
```

### 2.2. Union Area
Unlike the intersection, the union of rectangles is not necessarily a rectangle. Usually, the goal is to find the **area of the union**.

**For 2 rectangles (Inclusion-Exclusion Principle):**
$S(A \cup B) = S(A) + S(B) - S(A \cap B)$

**For 3 rectangles:**
$S(A \cup B \cup C) = S(A) + S(B) + S(C) - S(A \cap B) - S(A \cap C) - S(B \cap C) + S(A \cap B \cap C)$

For $N$ rectangles, the **Sweep Line** method is used, which is covered in Topic 27.

### 2.3. Point in Rectangle
```cpp
bool contains(const Rect& r, long long x, long long y) {
    return x >= r.x1 && x <= r.x2 && y >= r.y1 && y <= r.y2;
    // Be careful with strict inequalities (< vs <=) depending on the task requirements
}
```

## 3. 2D Prefix Sums

If we have a matrix $A$ with numbers and multiple queries for the sum in a sub-rectangle from $(x_1, y_1)$ to $(x_2, y_2)$, we can answer in $O(1)$ after $O(N \cdot M)$ preprocessing.

### Preprocessing
We create a matrix `pref`, where `pref[i][j]` stores the sum of the rectangle from $(1, 1)$ to $(i, j)$.
Formula (inclusion-exclusion):
`pref[i][j] = matrix[i][j] + pref[i-1][j] + pref[i][j-1] - pref[i-1][j-1]`

### Query
The sum in a rectangle defined by $(r1, c1)$ (top-left) and $(r2, c2)$ (bottom-right):
`Sum = pref[r2][c2] - pref[r1-1][c2] - pref[r2][c1-1] + pref[r1-1][c1-1]`

```cpp
// Sample implementation (1-based indexing)
vector<vector<long long>> pref(N + 1, vector<long long>(M + 1, 0));
for (int i = 1; i <= N; i++) {
    for (int j = 1; j <= M; j++) {
        pref[i][j] = matrix[i][j] + pref[i-1][j] + pref[i][j-1] - pref[i-1][j-1];
    }
}
// Query for (r1, c1) to (r2, c2)
long long query(int r1, int c1, int r2, int c2) {
    return pref[r2][c2] - pref[r1-1][c2] - pref[r2][c1-1] + pref[r1-1][c1-1];
}
```

## 4. Coordinate Compression

If the coordinates are large (e.g., up to $10^9$) but the number of rectangles $N$ is small (e.g., $1000$), we cannot create a matrix of size $10^9 \times 10^9$.
However, the truly important coordinates are only those where a rectangle starts or ends ($x_1$ and $x_2$).
There are at most $2N$ unique X and $2N$ unique Y coordinates.

### Algorithm:
1.  Collect all $x$ coordinates into a vector `xs`.
2.  Sort `xs` and remove duplicates (`sort` + `unique`).
3.  Replace each $x$ coordinate from the input with its index in `xs` (using `lower_bound`).
4.  Now you are working with coordinates in the interval $[0, 2N]$.

```cpp
vector<int> xs;
for (auto r : rects) { xs.push_back(r.x1); xs.push_back(r.x2); }
sort(xs.begin(), xs.end());
xs.erase(unique(xs.begin(), xs.end()), xs.end());

// Get compressed value
int compressed_x = lower_bound(xs.begin(), xs.end(), raw_x) - xs.begin();
// Get original value
int original_x = xs[compressed_x];
```

This is a key technique when working with SegTree or Sweep Line on large coordinates.

## 5. Tasks
1.  **CSES Forest Queries**: Classical task for 2D prefix sums.
2.  **Codeforces 1195C**: Basketball Exercise (has geometric variations).
3.  **IOI 2013 Dreaming**: Involves working with metrics.

**Tip**: Always draw the cases when rectangles do not intersect to understand the `max` and `min` formulas for the intersection.

---


## 6. Rectangle Rotation

If the rectangle is not axis-aligned (the sides are not parallel to the axes), the tasks become significantly more complex. Often it is necessary:
*   Using linear algebra (transformation matrices).
*   Checking for intersection via the **Separating Axis Theorem (SAT)**.

For basic tasks with rotation by 90°/180°/270°:
```cpp
// Rotating a point (x, y) by 90° around the origin
pair<int,int> rotate90(int x, int y) {
    return {-y, x};
}
```

---


## 7. Bounding Box

If we have a set of points, the smallest axis-aligned rectangle that contains them is easily found:
```cpp
Rect boundingBox(vector<Point>& points) {
    int minX = INT_MAX, minY = INT_MAX;
    int maxX = INT_MIN, maxY = INT_MIN;
    
    for (auto& p : points) {
        minX = min(minX, p.x);
        minY = min(minY, p.y);
        maxX = max(maxX, p.x);
        maxY = max(maxY, p.y);
    }
    
    return {minX, minY, maxX, maxY};
}
```

---


## 8. Precision Problems with Coordinates

If the coordinates are real numbers (`double`), be careful with equality checks:
```cpp
const double EPS = 1e-9;

bool areEqual(double a, double b) {
    return abs(a - b) < EPS;
}
```

When working with integer coordinates ($10^9$), the area can reach $10^{18}$, which fits in `long long`. But with even larger coordinates, BigInt or modular arithmetic may be needed.

---


## 9. Advanced Tasks

### 9.1. Area of Union of $N$ Rectangles
For this, the **Sweep Line algorithm** is used (see Topic 27):
*   Collect all vertical edges (opening and closing).
*   Sort them by X coordinate.
*   Maintain active intervals along the Y-axis using a Segment Tree or another structure.

Complexity: $O(N \log N)$.

### 9.2. Number of Points in a Rectangle (Range Query 2D)
If we have $M$ queries for the number of points in a given rectangle:
*   **Offline method**: Use Sweep Line + Fenwick Tree.
*   **Online method**: 2D Range Tree or Fractional Cascading.


---


## 🏁 Conclusion

Working with rectangles is fundamental in many areas of competitive programming. Mastering:
*   The formulas for intersection and union.
*   2D prefix sums.
*   Coordinate compression.

...will allow you to solve a wide range of geometric and optimization tasks. Always test with edge cases (rectangles sharing only one vertex, completely overlapping, etc.).