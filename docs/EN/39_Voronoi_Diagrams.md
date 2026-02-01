# 🌐 Voronoi Diagrams and Delaunay Triangulation

Voronoi Diagrams and their dual structure – Delaunay Triangulation – are fundamental objects in Computational Geometry.

## 1. Voronoi Diagram

### 1.1. Definition
Given a set of $N$ points $P = \{p_1, p_2, \dots, p_n\}$ in the plane, called **sites**.
For each site $p_i$, the **Voronoi cell** $V(p_i)$ consists of all points closer to $p_i$ than to any other site.

$V(p_i) = \{ x \in \mathbb{R}^2 \mid \text{dist}(x, p_i) \le \text{dist}(x, p_j), \forall j \neq i \}$

### 1.2. Properties
1.  Each cell is a **convex polygon**.
2.  Edges are parts of the **perpendicular bisectors** between sites.
3.  Vertices are points equidistant to at least 3 sites (circumcenters of Delaunay triangles).

### 1.3. Applications
*   **Nearest Neighbor**: Finding the nearest site for a query point $q$ means finding which cell $q$ falls into.
*   **Largest Empty Circle**: The center must be a Voronoi vertex.

---

## 2. Delaunay Triangulation

The dual graph of the Voronoi diagram. If two cells $V(p_i)$ and $V(p_j)$ share an edge, we connect $p_i$ and $p_j$.

### 2.1. Properties
1.  **Empty Circle Property**: The circumcircle of any triangle in the triangulation contains **no other sites** in its interior.
2.  **Max-Min Angle**: It maximizes the minimum angle of all angles in the triangulation (avoids skinny triangles).
3.  The outer edges form the **Convex Hull**.

---

## 3. Algorithms

### 3.1. Fortune's Algorithm - $O(N \log N)$
A Sweep-Line algorithm.
*   Maintains a **Beach Line** (parabolic arcs).
*   Events: Site events (new arc) and Circle events (arc disappears, forming a vertex).
*   Complex to implement from scratch in contests.

### 3.2. Incremental Algorithm (Bowyer-Watson) - $O(N^2)$ worst case
Suitable for $N \le 2000$ or random points.
1.  Start with a super-triangle containing all points.
2.  Add points one by one.
3.  For a new point $P$:
    *   Find all existing triangles whose circumcircle contains $P$ (they are now invalid).
    *   Remove them, creating a star-shaped "hole".
    *   Connect $P$ to the vertices of the hole to form new triangles.

```cpp
// Conceptual snippet
struct Triangle { int a, b, c; bool bad; };

vector<Triangle> bowyer_watson(vector<Point>& points) {
    // Add super-triangle
    // For each point p in points:
    //   bad_triangles = { t | t's circumcircle contains p }
    //   polygon = boundary of bad_triangles
    //   remove bad_triangles
    //   triangulate polygon with p
    // Remove super-triangle vertices
}
```

---

## 4. Practice Problems
1.  **Codeforces 212E**: Requires geometry concepts.
2.  **UVa 10002**: Center of Masses.
3.  **SPOJ CLOPPAIR**: Closest Pair of Points (Delaunay edges contain the closest pair).
4.  **IOI 2007 - Aliens**: Minimum Enclosing Circle.

**Tip:** In contests, check if a problem requires Voronoi or can be solved with simpler tools like **Half-plane intersection**, **Convex Hull**, or **Coordinate Compression**.