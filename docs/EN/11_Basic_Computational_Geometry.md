# 📐 Basic Computational Geometry: Theory and Tools

Geometry is notoriously difficult in competitive programming due to precision issues and numerous corner cases. Using vector-based logic and integer arithmetic wherever possible is the key to success.

---

## 1. Fundamental Structures

### 1.1. Point and Vector
In 2D, points and vectors are represented as $(x, y)$.
*   **Vector** $\vec{AB} = B - A = (B_x - A_x, B_y - A_y)$.

```cpp
struct Point {
    long long x, y;
    Point operator+(Point p) const { return {x + p.x, y + p.y}; }
    Point operator-(Point p) const { return {x - p.x, y - p.y}; }
    long long distSq(Point p) const { 
        return (x - p.x) * (x - p.x) + (y - p.y) * (y - p.y); 
    }
};
```

---

## 2. Vector Products

### 2.1. Dot Product (Scalar Product)
$A \cdot B = x_A x_B + y_A y_B = |A||B|\cos \theta$.
*   **Uses**: Finding the angle between vectors, projecting one vector onto another, checking for perpendicularity ($A \cdot B = 0$).

### 2.2. Cross Product (Vector Product)
In 2D, this is a scalar: $A \times B = x_A y_B - x_B y_A = |A||B|\sin \theta$.
*   **Uses**:
    1.  **Area**: The signed area of the parallelogram formed by $A$ and $B$.
    2.  **Triangle Area**: $Area(ABC) = \frac{1}{2} |(B-A) \times (C-A)|$.
    3.  **Orientation**: Is point $C$ to the left or right of line $AB$?

---

## 3. Orientation and Turns (CCW)

The `orient` function is the foundation of many geometry algorithms (Convex Hull, Line Intersection).

```cpp
long long cross_product(Point a, Point b, Point c) {
    return (b.x - a.x) * (c.y - a.y) - (b.y - a.y) * (c.x - a.x);
}

int orientation(Point a, Point b, Point c) {
    long long val = cross_product(a, b, c);
    if (val == 0) return 0;  // Collinear
    return (val > 0) ? 1 : 2; // 1: CCW (Left), 2: CW (Right)
}
```

---

## 4. Line and Segment Intersection

### 4.1. Line Representation
1.  **General Form**: $Ax + By + C = 0$.
2.  **Parametric**: $P = A + t(B-A)$.

### 4.2. Segment Intersection
Two segments $AB$ and $CD$ intersect if and only if:
*   $C$ and $D$ have different orientations relative to line $AB$.
*   $A$ and $B$ have different orientations relative to line $CD$.
*   *Edge case*: Handle collinear segments using bounding box checks.

---

## 5. Polygon Area (Shoelace Formula)

For a simple polygon with vertices $P_1, P_2, \dots, P_n$ in order:
$$Area = \frac{1}{2} \left| \sum_{i=1}^n (x_i y_{i+1} - x_{i+1} y_i) \right|$$
where $P_{n+1} = P_1$.

```cpp
long long area2(const vector<Point>& poly) {
    long long area = 0;
    for (int i = 0; i < poly.size(); i++) {
        area += poly[i].x * poly[(i+1)%poly.size()].y;
        area -= poly[(i+1)%poly.size()].x * poly[i].y;
    }
    return abs(area); // Returns 2 * Area
}
```

---

## 6. Practical Tips

### 6.1. Avoid Floating Point
If coordinates are integers, the cross product and squared distance will be integers. Only use `double` and `sqrt` at the very last step.

### 6.2. Dealing with Precision
If `double` is unavoidable, use an epsilon constant:
```cpp
const double EPS = 1e-9;
if (abs(a - b) < EPS) // a == b
```

---

## 7. Practice Problems
1.  **UVa 191**: Intersection.
2.  **CSES Polygon Area**: Direct Shoelace formula.
3.  **Codeforces 1C**: Ancient Berland Circus.
4.  **SPOJ CLOPPAIR**: Closest Pair of Points.

## 8. Conclusion
Geometry requires a solid template. The `Point` struct and `orientation` function are your best friends. Always test against collinear points and degenerate cases (e.g., points at the same location).