# 📐 Rectangles and Geometric Tasks

Axis-aligned rectangles are among the most common objects in problems.

## 🏗️ Representation
Usually stored as two points: bottom-left $(x_1, y_1)$ and top-right $(x_2, y_2)$.

```cpp
struct Rect {
    long long x1, y1, x2, y2;
    long long area() { return (x2 - x1) * (y2 - y1); }
};
```

## ✂️ Intersection of Two Rectangles
The intersection of two rectangles (if it exists) is also a rectangle.
- $x_{left} = \max(r1.x1, r2.x1)$
- $x_{right} = \min(r1.x2, r2.x2)$
- Analogous for $y$.
- If $x_{left} < x_{right}$ and $y_{bottom} < y_{top}$, intersection exists.

## ➕ Area of Union
Finding the area of the union of $N$ rectangles is a classic task.
- **Sweep-line Method**: Move a vertical line and track covered segments. Complexity $O(N \log N)$. (See Topic 27).

---

## 🏁 Conclusion
Working with rectangles requires careful handling of inequalities. Always check if a rectangle is "degenerate" (zero area).
