# 📐 Computational Geometry: Basics and Vectors

## 📏 Point in Polygon

How to check if a point $P$ lies inside an arbitrary (even non-convex) polygon?

### 1. Ray Casting Algorithm
Cast a horizontal ray from $P$ to the right towards $+\infty$.
- Count how many times the ray intersects the polygon edges.
- If the count is **odd**, the point is **inside**.
- If **even**, it is **outside**.
- **Edge cases**: Be careful if the ray passes exactly through a vertex or overlaps with an edge.

### 2. Winding Number
Sum the angles subtended by the edges from point $P$.
- If the sum is $2\pi$ (360 degrees), the point is inside.
- If 0, it is outside.
- This method is more stable but harder to implement (uses `atan2` or cross product).

---

## 📐 Area of a Triangle

### 1. Heron's Formula
If we know the side lengths $a, b, c$:
$$S = \sqrt{p(p-a)(p-b)(p-c)}, \quad p = \frac{a+b+c}{2}$$

### 2. Cross Product (Shoelace for Triangle)
If we know coordinates $(x_A, y_A), (x_B, y_B), (x_C, y_C)$:
$$S = \frac{1}{2} | (x_B - x_A)(y_C - y_A) - (y_B - y_A)(x_C - x_A) |$$
This is the more precise method as it avoids square roots and `double` (if coordinates are integers).

---

## 🛠️ Vectors in C++

Always define a `Point` class/struct with overloaded operators.

```cpp
struct Point {
    long long x, y;
    Point operator+(const Point& other) const { return {x + other.x, y + other.y}; }
    Point operator-(const Point& other) const { return {x - other.x, y - other.y}; }
    long long cross(const Point& other) const { return x * other.y - y * other.x; }
    long long dot(const Point& other) const { return x * other.x + y * other.y; }
};
```
- **Dot product**: $A \cdot B = |A||B|\cos \theta$. If 0, vectors are perpendicular.
- **Cross product**: If 0, vectors are collinear.

---

## 🏁 Conclusion

Avoid `sqrt`, `sin`, `cos`, and `double` whenever possible. The cross product is a programmer's best friend in geometry – it works with integers and gives precise results for orientation and area.

```