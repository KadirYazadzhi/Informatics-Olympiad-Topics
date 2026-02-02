# 🌐 Voronoi Diagrams and Delaunay Triangulation

Voronoi Diagrams and their dual structure – Delaunay Triangulation – are some of the most fundamental objects in computational geometry.

## 1. Voronoi Diagram

### 1.1. Definition
Let we have a set of $N$ points $P = \{p_1, p_2, \dots, p_n\}$ in the plane, called **sites**.
For each point $p_i$, the **Voronoi cell** $V(p_i)$ consists of all points in the plane that are closer to $p_i$ than to any other point from $P$.

$V(p_i) = \{ x \in \mathbb{R}^2 \mid \text{dist}(x, p_i) \le \text{dist}(x, p_j), \forall j \neq i \}$

### 1.2. Properties
1.  Each cell is a **convex polygon** (an intersection of half-planes).
2.  The edges of the diagram are parts of the **perpendicular bisectors** of the segments connecting pairs of points.
3.  The vertices of the diagram are points that are equidistant from at least 3 sites (centers of circumscribed circles).
4.  If all points are collinear, the diagram consists of parallel lines.

### 1.3. Applications
*   **Nearest Neighbor**: For a given point $q$, finding the nearest site reduces to finding which cell $q$ falls into ($O(\log N)$ after $O(N \log N)$ preprocessing).
*   **Largest Empty Circle**: The center of such a circle must coincide with a vertex of the Voronoi diagram or lie on the convex hull.

---\n

## 2. Delaunay Triangulation

The Delaunay triangulation is the dual graph of the Voronoi diagram. If two cells $V(p_i)$ and $V(p_j)$ share an edge, we connect $p_i$ and $p_j$ with a segment.

### 2.1. Properties
1.  **Empty Circles**: The circumscribed circle around every triangle of the triangulation **does not contain** other points of the set in its interior.
2.  **Maximizing the Minimum Angle**: Of all possible triangulations of the point set, the Delaunay one maximizes the smallest angle in the triangles (avoids "skinny" triangles).
3.  The outer edges form the **Convex Hull** of the set.

---\n

## 3. Construction Algorithms

### 3.1. Fortune's Algorithm - $O(N \log N)$
This is a "Sweep Line" algorithm.
*   We use a scanning line (sweep line) that moves from top to bottom.
*   We maintain a **"Beach Line"**: The boundary between the processed and unprocessed part of the plane. It consists of arcs of parabolas (since the geometric locus of points equidistant from a point (site) and a line (sweep line) is a parabola).
*   The meeting of two arcs describes a Voronoi edge.
*   The complexity is optimal, but the implementation is extremely difficult and is rarely done in competitions (unless you have prepared code in a library).

### 3.2. Incremental Algorithm (Bowyer-Watson) - $O(N^2)$ (worst case)
For competitions with small $N$ (up to $\approx 1000-2000$) or randomly distributed points, this method is preferred.
1.  We start with a "super-triangle" that contains all points.
2.  We add the points one by one.
3.  For each new point $P$:
    *   Find all existing triangles whose circumscribed circle contains $P$. These triangles are no longer valid (they violate the Delaunay condition).
    *   Remove them, which forms a "star-shaped hole".
    *   Connect $P$ with the vertices of this hole to form new triangles.

```cpp
#include <iostream>
#include <vector>
#include <cmath>
#include <algorithm>

using namespace std;

struct Point { double x, y; };

struct Edge {
    int u, v; // indices of points
    bool bad;
};

struct Triangle {
    int a, b, c; // indices of points
    bool bad; // marked for removal
};

// Check if point p is inside the circumcircle of the triangle
bool is_inside_circumcircle(Point p, Point a, Point b, Point c) {
    // Determinant or distance formula
    double ax = a.x - p.x, ay = a.y - p.y;
    double bx = b.x - p.x, by = b.y - p.y;
    double cx = c.x - p.x, cy = c.y - p.y;
    
    // (a^2 + ay^2) * (bx*cy - by*cx) - ...
    // For simplicity: distance from center. 
    // Here we show only the idea.
    return false; // Implement Geometry logic
}

// Sample structure of the algorithm
vector<Triangle> bowyer_watson(vector<Point>& points) {
    vector<Triangle> triangles;
    // 1. Add super-triangle (large enough)
    // ...

    for (int i = 0; i < (int)points.size(); ++i) {
        vector<Triangle> badTriangles;
        for (auto& t : triangles) {
            // Get the coordinates of the vertices of t
            // Check if points[i] is in the circumcircle
            // If yes -> t.bad = true; badTriangles.push_back(t);
        }

        vector<Edge> polygon;
        for (auto& t : badTriangles) {
            // Add the edges of t to the polygon
            // If an edge is repeated (shared between two bad triangles), it is internal -> remove it.
        }

        // Remove bad triangles from triangles
        // ...

        // Create new triangles from points[i] to each edge in polygon
        for (auto& edge : polygon) {
            triangles.push_back({edge.u, edge.v, i, false});
        }
    }
    
    // Remove triangles connected to the super-triangle
    return triangles;
}
```

---\n

## 4. Practice Tasks

1.  **Codeforces 212E**: Requires geometry; although not directly Voronoi, the concepts help.
2.  **UVa 10002 - Center of Masses**: Working with polygons.
3.  **SPOJ CLOPPAIR**: Closest Pair of Points (Divide and Conquer is faster, but Voronoi also solves the task).
4.  **IOI 2007 - Aliens**: May require the smallest enclosing circle.

**Tip:** In competitions, if a task requires Voronoi, first think if it can be solved with:
*   Closest Pair of Points (Divide & Conquer).
*   Convex Hull.
*   Half-plane intersection.
The Voronoi diagram is the "heavy artillery".

---\n

## 5. Practical Implementation Tips

### 5.1. Floating Point Issues
When working with geometric algorithms, problems with the precision of fractional numbers often appear:
```cpp
const double EPS = 1e-9;

bool doubleEquals(double a, double b) {
    return abs(a - b) < EPS;
}
```

### 5.2. Determinants and Orientation
To check if three points are in a clockwise or counter-clockwise direction:
```cpp
double cross(Point a, Point b, Point c) {
    return (b.x - a.x) * (c.y - a.y) - (b.y - a.y) * (c.x - a.x);
}

// Returns: > 0 (counter-clockwise), < 0 (clockwise), = 0 (collinear)
```

This is useful when building a Convex Hull, which is a step before Delaunay.

---\n

## 6. Applications of Delaunay Triangulation

### 6.1. Mesh Generation
In computer graphics and simulations (finite elements), Delaunay triangulation is used to generate quality triangular meshes.

### 6.2. Terrain Interpolation
If you have a set of points with heights (for example, from GPS data), you can build a triangulation and interpolate the height at an arbitrary point.

### 6.3. Path Planning
In robotics, the Voronoi diagram helps in finding paths that are as far as possible from obstacles.

---\n

## 7. Related Structures

### 7.1. Convex Hull
The outer boundary of the Delaunay triangulation is always the convex hull of the points. If you only need that, use Graham's or Jarvis's algorithm (simpler to implement).

### 7.2. Closest Pair of Points
Can be solved with Divide and Conquer in $O(N \log N)$, which is easier than Voronoi. But theoretically, Voronoi also solves this problem.

### 7.3. Euclidean Minimum Spanning Tree
The MST of points in the plane, where the edges are Euclidean distances. Can be built in $O(N \log N)$ by first creating a Delaunay triangulation (which contains all edges of the MST).

---\n

## 8. More Examples and Tasks

### Task: Nearest Airport
You have $N$ airports on a map. For a given point $(x, y)$, find the nearest airport.

**Solution**:
1. Build a Voronoi diagram.
2. For each point, determine which cell it falls into (Point Location problem - $O(\log N)$ after preprocessing).

### Task: Largest Empty Circle
Find the largest circle that does not contain any of the given points.

**Solution**: The center is either a vertex of the Voronoi diagram or lies on the convex hull.

---\n

## 🏁 Final Recommendations

*   Voronoi and Delaunay are theoretically beautiful and powerful, but they are rarely implemented from scratch in competitions.
*   If the task can be solved with simpler methods (sweep line, binary search, convex hull), prefer them.
*   If you decide to implement Bowyer-Watson, test well with small examples and edge cases (collinear points, cocircular points).
*   For production code or scientific research, use libraries like CGAL (C++) or SciPy (Python).

Voronoi diagrams are a fundamental structure with applications in many areas - from computer graphics to biology and urban planning!
