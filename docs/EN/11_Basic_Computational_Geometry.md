# 🧭 Basic Computational Geometry

Computational geometry deals with algorithms for objects like points, segments, lines, and polygons.

## 📍 Point and Vector
The most important structure. A vector is a directed segment from $(0,0)$ to $(x,y)$.

```cpp
struct Point {
    double x, y;
    Point operator-(const Point& other) const { return {x - other.x, y - other.y}; }
};
```

## 📏 Dot and Cross Product

### 1. Dot Product
$$\vec{A} \cdot \vec{B} = x_A x_B + y_A y_B = |A||B| \cos \theta$$
- If 0 $\to$ vectors are perpendicular.
- Helps find projections.

### 2. Cross Product
$$A \times B = x_A y_B - x_B y_A$$
- Gives the oriented area of the parallelogram.
- **Most important**: Turn direction.
  - $>0$ $\to$ left turn.
  - $<0$ $\to$ right turn.
  - $=0$ $\to$ collinear (on the same line).

---

## 🏁 Conclusion
Geometry requires precision. Always work with integers (`long long`) when possible to avoid `double` rounding errors.