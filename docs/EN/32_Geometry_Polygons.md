# 🔷 Polygon Geometry

Polygons are sequences of points.
Vertices are usually given in CCW or CW order.

## 1. Area (Shoelace Formula)
Area of a simple polygon with vertices $(x_1, y_1), \dots, (x_n, y_n)$:

$2 \cdot S = \left| \sum_{i=1}^{n} (x_i y_{i+1} - x_{i+1} y_i) \right|$
where $(x_{n+1}, y_{n+1}) = (x_1, y_1)$.

Equivalent to the sum of cross products of vectors from origin.

```cpp
long long polygonArea2(const vector<Point>& p) {
    long long area = 0;
    int n = p.size();
    for (int i = 0; i < n; i++) {
        area += cross(p[i], p[(i + 1) % n]);
    }
    return abs(area); // Returns 2 * Area
}
```

## 2. Pick's Theorem
For a lattice polygon (vertices on integer coordinates):
$S = I + \frac{B}{2} - 1$
*   $S$: Area.
*   $I$: Interior integer points.
*   $B$: Boundary integer points.

Boundary points $B$ can be found via GCD of coordinate differences:
$B = \sum \gcd(|x_i - x_{i+1}|, |y_i - y_{i+1}|)$

## 3. Point in Polygon

### 3.1. Convex Polygon
$O(\log N)$ using binary search on angles (if vertices are sorted).

### 3.2. General Polygon (Ray Casting)
Cast a ray from $P$ to $+\infty$. Count intersections with edges.
*   Odd intersections $\implies$ Inside.
*   Even intersections $\implies$ Outside.
*   Corner cases: Point on edge, Ray passes through vertex.

```cpp
bool isInside(const vector<Point>& p, Point pt) {
    int n = p.size();
    bool inside = false;
    for (int i = 0, j = n - 1; i < n; j = i++) {
        if (((p[i].y > pt.y) != (p[j].y > pt.y)) &&
            (pt.x < (p[j].x - p[i].x) * (pt.y - p[i].y) / (double)(p[j].y - p[i].y) + p[i].x)) {
            inside = !inside;
        }
    }
    return inside;
}
```

## 4. Convex Hull
The smallest convex polygon containing all points. (See Sweep-Line topic).

## 5. Practice
1.  **Codeforces 166B**: Polygon.
2.  **POJ 1265**: Area (Pick's theorem).
3.  **SPOJ GSSQ**: General queries.

## 6. Conclusion
Always work with $2 \cdot S$ (doubled area) to keep calculations in integers (`long long`).