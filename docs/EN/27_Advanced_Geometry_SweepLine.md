# 🧹 Sweep Line Algorithm: Theory and Applications

The Sweep Line algorithm is a powerful paradigm in Computational Geometry. It converts a 2D problem into a sequence of 1D updates by moving a vertical (or horizontal) line across the plane and processing objects as the line hits them.

---

## 1. Core Concepts

### 1.1. The Line and Events
*   **Sweep Line**: An imaginary line (usually $x = k$) that moves from $-\infty$ to $+\infty$.
*   **Events**: Specific $x$-coordinates where the "status" of the sweep line changes (e.g., a point, the start/end of a segment). Events are stored in a **Priority Queue** or a **Sorted Vector**.

### 1.2. Status
The data structure that maintains information about objects currently intersected by the sweep line. Typically a **Balanced BST** (like `std::set`) or a **Segment Tree**.

---

## 2. Closest Pair of Points

Find the two points with the minimum distance $d$ among $N$ points in $O(N \log N)$.

### Algorithm:
1.  Sort points by $x$.
2.  Maintain a set of points whose $x$-distance to the current point is $< d$, sorted by $y$.
3.  For a new point $P$:
    *   Remove points from the set where $P.x - pts[j].x \ge d$.
    *   Find points in the set with $y \in [P.y - d, P.y + d]$.
    *   Check distance to these candidates (mathematically, at most 6 points).
    *   Update $d$.

```cpp
struct Point { long long x, y; };

double closest_pair(vector<Point>& pts) {
    sort(pts.begin(), pts.end(), [](Point a, Point b){ return a.x < b.x; });
    set<pair<int, int>> active; // {y, x}
    double d = 1e18;
    int j = 0;
    for (int i = 0; i < pts.size(); i++) {
        while (j < i && pts[i].x - pts[j].x >= d) {
            active.erase({pts[j].y, pts[j].x});
            j++;
        }
        auto it1 = active.lower_bound({pts[i].y - d, -2e9});
        auto it2 = active.upper_bound({pts[i].y + d, 2e9});
        for (auto it = it1; it != it2; it++)
            d = min(d, dist(pts[i], {it->second, it->first}));
        active.insert({pts[i].y, pts[i].x});
    }
    return d;
}
```

---

## 3. Area of Union of Rectangles

Compute the total area covered by $N$ axis-aligned rectangles in $O(N \log N)$.

### Algorithm:
1.  **Events**: Vertical edges of each rectangle.
    *   Left edge: $x$, range $[y_1, y_2]$, type $+1$.
    *   Right edge: $x$, range $[y_1, y_2]$, type $-1$.
2.  **Status**: A **Segment Tree** covering all unique $y$-coordinates.
    *   Nodes store: `count` (how many rectangles cover this range) and `length` (total covered length).
3.  As the line moves, `Area += Tree.Root.Length * (current_x - prev_x)`.

---

## 4. Convex Hull: Monotone Chain

A variation of Sweep Line to find the smallest convex polygon containing all points.
1.  Sort points by $x$ (then $y$).
2.  Construct the **Lower Hull** by processing points left-to-right.
3.  Construct the **Upper Hull** by processing points right-to-left.
4.  Use the **Cross Product** to ensure every turn is counter-clockwise.

---

## 5. Segment Intersection: Bentley-Ottmann

Find all $K$ intersections of $N$ segments in $O((N+K) \log N)$.
*   **Status**: Segments currently intersected by the line, ordered by $y$.
*   **Events**: Segment starts, ends, and **predicted intersection points**.
*   Intersection can only happen between adjacent segments in the status.

---

## 6. Practice Problems
1.  **SPOJ CLOPPAIR**: Closest Pair.
2.  **CSES Area of Rectangles**: Sweep Line + SegTree.
3.  **CSES Convex Hull**: Monotone Chain.
4.  **UVa 10245**: Closest Pair problem.

## 7. Conclusion
Sweep Line is the "standard" approach for 2D problems. Success depends on:
1.  Identifying what constitutes an event.
2.  Choosing an efficient data structure for the status.
3.  Handling degenerate cases (vertical lines, points at same $x$).
