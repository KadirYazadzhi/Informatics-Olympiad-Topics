# 🔷 Polygon Geometry: Area, Points, and Convexity

A polygon is a closed geometric shape defined by a finite sequence of points (vertices). In programming, we typically represent it as a `std::vector<Point>`.

---

## 1. Classification

*   **Simple Polygon**: No two edges cross each other.
*   **Convex Polygon**: For any two points inside, the segment joining them is also inside. All interior angles are $< 180^\circ$.
*   **Concave Polygon**: At least one interior angle is $> 180^\circ$.

---

## 2. Polygon Area (Shoelace Formula)

The area of a simple polygon with vertices $(x_1, y_1), \dots, (x_n, y_n)$ is:
$$Area = \frac{1}{2} \left| \sum_{i=1}^n (x_i y_{i+1} - x_{i+1} y_i) \right|$$
where $(x_{n+1}, y_{n+1}) = (x_1, y_1)$.

### Implementation ($O(N)$)
```cpp
long long doubled_area(const vector<Point>& p) {
    long long res = 0;
    for (int i = 0; i < p.size(); i++) {
        res += p[i].x * p[(i + 1) % p.size()].y;
        res -= p[(i + 1) % p.size()].x * p[i].y;
    }
    return abs(res); // returns 2 * Area
}
```

---

## 3. Pick's Theorem

For a **lattice polygon** (all vertices have integer coordinates):
$$Area = I + \frac{B}{2} - 1$$
*   $Area$: Total area.
*   $I$: Number of integer points strictly **inside**.
*   $B$: Number of integer points on the **boundary**.

### Finding $B$
The number of lattice points on a segment $(x_1, y_1)$ to $(x_2, y_2)$ is $\gcd(|x_1-x_2|, |y_1-y_2|) + 1$. For the boundary of a polygon, we sum the GCDs for each edge.

---

## 4. Point in Polygon

### 4.1. Convex Polygon ($O(\log N)$)
If vertices are sorted, use binary search on angles. Split the polygon into triangles originating from vertex 0 and check which triangle the point falls into.

### 4.2. General Polygon ($O(N)$) - Ray Casting
Cast a ray from the point to infinity (e.g., to the right). Count how many edges the ray intersects.
*   Odd crossings $\implies$ Inside.
*   Even crossings $\implies$ Outside.

```cpp
bool isInside(const vector<Point>& p, Point pt) {
    int n = p.size();
    bool inside = false;
    for (int i = 0, j = n - 1; i < n; j = i++) {
        if (((p[i].y > pt.y) != (p[j].y > pt.y)) &&
            (pt.x < (p[j].x - p[i].x) * (pt.y - p[i].y) / (double)(p[j].y - p[i].y) + p[i].x))
            inside = !inside;
    }
    return inside;
}
```

---

## 5. Convex Hull

The Convex Hull is the smallest convex polygon that contains all points.
*   **Monotone Chain Algorithm ($O(N \log N)$)**:
    1.  Sort points by $x$.
    2.  Build lower and upper hulls using cross products to detect turns.


## 6. Practice Problems
1.  **Codeforces 166B**: Polygon (Point in convex polygon).
2.  **POJ 1265**: Area (Pick's Theorem).
3.  **CSES Polygon Area**.
4.  **CSES Point in Polygon**.

## 7. Conclusion
Always prefer integer arithmetic. Work with $2 \times Area$ to avoid fractions until the output. For point-in-polygon checks, be careful with points that lie exactly on the boundary.
