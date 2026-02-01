# 📐 Rectangles: Intersection, Union, and Advanced Queries

Axis-Aligned Bounding Boxes (AABB) are ubiquitous in computational geometry problems. A rectangle is usually defined by its lower-left corner $(x_1, y_1)$ and its upper-right corner $(x_2, y_2)$.

---

## 1. Core Representation

Always ensure $x_1 \le x_2$ and $y_1 \le y_2$. Using `long long` is recommended to prevent overflow when calculating the area.

```cpp
struct Rect {
    long long x1, y1, x2, y2;
    
    long long width() const { return max(0LL, x2 - x1); }
    long long height() const { return max(0LL, y2 - y1); }
    long long area() const { return width() * height(); }
};
```

---

## 2. Fundamental Operations

### 2.1. Intersection
The intersection of two rectangles $A$ and $B$ is also a rectangle. Its boundaries are the "innermost" limits of the two rectangles.

```cpp
Rect intersect(const Rect& a, const Rect& b) {
    Rect res;
    res.x1 = max(a.x1, b.x1);
    res.y1 = max(a.y1, b.y1);
    res.x2 = min(a.x2, b.x2);
    res.y2 = min(a.y2, b.y2);
    
    if (res.x1 >= res.x2 || res.y1 >= res.y2) {
        return {0, 0, 0, 0}; // Pertains to an empty intersection
    }
    return res;
}
```

### 2.2. Union Area
The area covered by the union of two rectangles is:
$$Area(A \cup B) = Area(A) + Area(B) - Area(A \cap B)$$

For $N$ rectangles, the **Inclusion-Exclusion Principle** is too slow ($O(2^N)$). Instead, use the **Sweep-Line Algorithm** with a **Segment Tree** to achieve $O(N \log N)$.

---

## 3. Sub-rectangle Queries (2D Prefix Sums)

If you have a grid of values and need to find the sum of values in a rectangular sub-region $(r_1, c_1)$ to $(r_2, c_2)$, use 2D Prefix Sums.

### 3.1. Building the Table ($O(NM)$)
`pref[i][j]` stores the sum of the rectangle from $(0, 0)$ to $(i, j)$.
$$pref[i][j] = val[i][j] + pref[i-1][j] + pref[i][j-1] - pref[i-1][j-1]$$

### 3.2. Querying the Table ($O(1)$)
$$Sum = pref[r_2][c_2] - pref[r_1-1][c_2] - pref[r_2][c_1-1] + pref[r_1-1][c_1-1]$$

---

## 4. Coordinate Compression

When rectangle coordinates are large (up to $10^9$) but the number of rectangles $N$ is small (up to $1000$):
1.  Collect all $x_1$ and $x_2$ values into a sorted vector `xs`.
2.  Remove duplicates.
3.  Replace every $x$ coordinate with its index in `xs`.
4.  Do the same for $y$ coordinates.
5.  This maps the infinite plane onto a finite $2N \times 2N$ grid.

---

## 5. Maximum Sub-rectangle Sum

Given an $N \times M$ matrix, find the rectangle with the maximum sum.
*   **Naive**: $O(N^2 M^2)$ checking all pairs of corners.
*   **Optimized**: $O(N^2 M)$ by fixing two row indices and applying Kadane's algorithm on the vertical sums.

---

## 6. Practice Problems
1.  **CSES Forest Queries**: 2D Prefix Sums.
2.  **CSES Area of Rectangles**: Sweep Line + Segment Tree.
3.  **Codeforces 1195C**: Basketball Exercise.
4.  **UVa 108**: Maximum Sum (2D Kadane).

## 7. Conclusion
Rectangles are the simplest polygons, but they appear in many disguised forms (like time ranges or 2D ranges in data structures). Master the intersection formulas and the 2D prefix sum technique.
