# 🔷 Polygon Geometry

A polygon is a closed chain of segments. In competitions, vertices are usually given in order (clockwise or counter-clockwise).

A polygon is called **simple** if its sides do not intersect each other (except at common vertices). A polygon is called **convex** if all interior angles are less than 180° and every segment between two points in the polygon lies entirely inside it.

## 0. Basic Definitions and Structures

```cpp
struct Point {
    long long x, y;
    Point(long long x = 0, long long y = 0) : x(x), y(y) {}
    Point operator-(const Point& p) const { return Point(x - p.x, y - p.y); }
    Point operator+(const Point& p) const { return Point(x + p.x, y + p.y); }
};

// Cross Product
long long cross(const Point& a, const Point& b) {
    return (long long)a.x * b.y - (long long)a.y * b.x;
}

// Dot Product
long long dot(const Point& a, const Point& b) {
    return (long long)a.x * b.x + (long long)a.y * b.y;
}

// Vector length squared
long long len2(const Point& a) {
    return a.x * a.x + a.y * a.y;
}
```

## 1. Polygon Area (Shoelace Formula)

The area of any simple polygon with vertices $(x_1, y_1), \dots, (x_n, y_n)$ can be calculated using the **Shoelace Formula**. It is based on the oriented areas of trapezoids (or triangles).

$2 \cdot S = \left| \sum_{i=1}^{n} (x_i y_{i+1} - x_{i+1} y_i) \right|$

where $(x_{n+1}, y_{n+1}) = (x_1, y_1)$.

### Geometric Intuition
The formula works by considering each side of the polygon and calculating the "oriented area" of the trapezoid formed between that side and the x-axis. If the vertices are in counter-clockwise order, the sum gives a positive area; if they are clockwise – a negative one.

### Implementation
```cpp
// Returns double the area (2 * S)
long long polygonArea2(const vector<Point>& p) {
    long long area = 0;
    int n = p.size();
    for (int i = 0; i < n; i++) {
        area += cross(p[i], p[(i + 1) % n]);
    }
    return abs(area); // Absolute value for positive area
}

// Returns the actual area (can be fractional)
double polygonArea(const vector<Point>& p) {
    return polygonArea2(p) / 2.0;
}

// Signed area (positive if vertices are counter-clockwise)
long long signedArea2(const vector<Point>& p) {
    long long area = 0;
    int n = p.size();
    for (int i = 0; i < n; i++) {
        area += cross(p[i], p[(i + 1) % n]);
    }
    return area;
}
```

### Example
For a square with vertices $(0, 0), (2, 0), (2, 2), (0, 2)$:
- $2S = |0 \cdot 0 - 2 \cdot 0 + 2 \cdot 2 - 2 \cdot 0 + 2 \cdot 2 - 0 \cdot 2 + 0 \cdot 0 - 0 \cdot 2| = |0 + 4 + 4 + 0| = 8$
- $S = 4$ (correct for a square with side 2)

## 2. Polygon Perimeter

The perimeter is the sum of the lengths of all sides of the polygon.

```cpp
// Perimeter (fractional number)
double polygonPerimeter(const vector<Point>& p) {
    double perimeter = 0;
    int n = p.size();
    for (int i = 0; i < n; i++) {
        Point diff = p[(i + 1) % n] - p[i];
        perimeter += sqrt(len2(diff));
    }
    return perimeter;
}

// For integer coordinates - squared
long long polygonPerimeter2(const vector<Point>& p) {
    long long perimeter2 = 0;
    int n = p.size();
    for (int i = 0; i < n; i++) {
        Point diff = p[(i + 1) % n] - p[i];
        perimeter2 += len2(diff);
    }
    return perimeter2;
}
```

## 3. Pick's Theorem

For a polygon whose vertices are integer coordinates (lattice polygon):
$S = I + \frac{B}{2} - 1$

*   $S$: Area of the polygon.
*   $I$: Number of integer points **inside** the polygon.
*   $B$: Number of integer points **on the boundary**.

### Finding B (Boundary Points)
$B$ can be found by summing the GCD of the differences in coordinates for each side:
$B = \sum_{i=1}^{n} \gcd(|x_i - x_{i+1}|, |y_i - y_{i+1}|)$

```cpp
long long gcd(long long a, long long b) {
    return b ? gcd(b, a % b) : a;
}

// Number of integer points on the boundary
long long boundaryPoints(const vector<Point>& p) {
    long long B = 0;
    int n = p.size();
    for (int i = 0; i < n; i++) {
        Point diff = p[(i + 1) % n] - p[i];
        B += gcd(abs(diff.x), abs(diff.y));
    }
    return B;
}

// Number of integer points inside (using Pick's Theorem)
long long interiorPoints(const vector<Point>& p) {
    long long area2 = polygonArea2(p);
    long long B = boundaryPoints(p);
    // S = I + B/2 - 1  =>  I = S - B/2 + 1
    // 2S = 2I + B - 2  =>  I = (2S - B + 2) / 2
    return (area2 - B + 2) / 2;
}
```

### Example
For a triangle with vertices $(0, 0), (3, 0), (0, 4)$:
- Area: $S = \frac{3 \cdot 4}{2} = 6$
- $B = \gcd(3, 0) + \gcd(3, 4) + \gcd(0, 4) = 3 + 1 + 4 = 8$
- $I = S - \frac{B}{2} + 1 = 6 - 4 + 1 = 3$

## 4. Point in Polygon

### 4.1. Convex Polygon
For a convex polygon, this can be solved in $O(\log N)$ using binary search on angles.

```cpp
// Check whether a point is in a convex polygon (O(log n))
bool inConvexPolygon(const vector<Point>& p, Point pt) {
    int n = p.size();
    // Check whether the point is on the correct side of all sides
    bool pos = false, neg = false;
    for (int i = 0; i < n; i++) {
        long long cp = cross(p[(i + 1) % n] - p[i], pt - p[i]);
        if (cp > 0) pos = true;
        if (cp < 0) neg = true;
    }
    return !(pos && neg); // Inside if all cross products have the same sign
}
```

### 4.2. Arbitrary Polygon (Ray Casting)
For an arbitrary (not necessarily convex) polygon, we use the **Ray Casting algorithm**.

**Idea:** We fire a ray from point $P$ to the right (towards $+\infty$). We count how many times the ray intersects the sides of the polygon.
*   Odd number of intersections $\implies$ The point is **inside**.
*   Even number (including 0) $\implies$ The point is **outside**.

**Edge cases:**
- The point lies exactly on an edge.
- The ray passes through a vertex of the polygon.
- The ray is parallel to some side.

```cpp
// Ray Casting algorithm - arbitrary polygon
bool isInside(const vector<Point>& p, Point pt) {
    int n = p.size();
    bool inside = false;
    for (int i = 0, j = n - 1; i < n; j = i++) {
        // We check whether the ray intersects edge (j, i)
        if (((p[i].y > pt.y) != (p[j].y > pt.y)) &&
            (pt.x < (p[j].x - p[i].x) * (pt.y - p[i].y) / (double)(p[j].y - p[i].y) + p[i].x)) {
            inside = !inside;
        }
    }
    return inside;
}

// Check whether a point lies on the boundary of the polygon
bool onBoundary(const vector<Point>& p, Point pt) {
    int n = p.size();
    for (int i = 0; i < n; i++) {
        Point a = p[i], b = p[(i + 1) % n];
        // Check whether pt lies on segment [a, b]
        if (cross(pt - a, b - a) == 0 &&
            min(a.x, b.x) <= pt.x && pt.x <= max(a.x, b.x) &&
            min(a.y, b.y) <= pt.y && pt.y <= max(a.y, b.y)) {
            return true;
        }
    }
    return false;
}

// Full check: inside, outside, or on the boundary
// Returns: 0 = outside, 1 = on the boundary, 2 = inside
int pointInPolygon(const vector<Point>& p, Point pt) {
    if (onBoundary(p, pt)) return 1;
    return isInside(p, pt) ? 2 : 0;
}
```

### 4.3. Winding Number Algorithm
An alternative method that counts how many times the polygon "wraps around" the point.

```cpp
// Winding number - more robust for delicate cases
int windingNumber(const vector<Point>& p, Point pt) {
    int wn = 0;
    int n = p.size();
    for (int i = 0; i < n; i++) {
        Point a = p[i], b = p[(i + 1) % n];
        if (a.y <= pt.y) {
            if (b.y > pt.y && cross(b - a, pt - a) > 0)
                wn++;
        } else {
            if (b.y <= pt.y && cross(b - a, pt - a) < 0)
                wn--;
        }
    }
    return wn; // != 0 means inside
}
```

## 5. Convexity Check

A polygon is **convex** if all turns are in the same direction (only left or only right). This means that all interior angles are less than 180°.

A polygon is **concave** if there is at least one interior angle greater than 180°.

### Convexity Check
We use the cross product for every three consecutive vertices. If all have the same sign, the polygon is convex.

```cpp
// Check whether a polygon is convex
bool isConvex(const vector<Point>& p) {
    int n = p.size();
    if (n < 3) return false;
    
    bool hasPos = false, hasNeg = false;
    for (int i = 0; i < n; i++) {
        Point a = p[i];
        Point b = p[(i + 1) % n];
        Point c = p[(i + 2) % n];
        
        long long cp = cross(b - a, c - b);
        if (cp > 0) hasPos = true;
        if (cp < 0) hasNeg = true;
    }
    
    return !(hasPos && hasNeg); // Convex if all have the same sign
}

// Determining the orientation (clockwise or counter-clockwise)
// Returns: 1 = counter-clockwise, -1 = clockwise, 0 = degenerate
int orientation(const vector<Point>& p) {
    long long area = signedArea2(p);
    if (area > 0) return 1;  // CCW
    if (area < 0) return -1; // CW
    return 0; // Degenerate
}

// Reversing the orientation of a polygon
void reversePolygon(vector<Point>& p) {
    reverse(p.begin(), p.end());
}
```

### Detection of Convex and Reflex Vertices
```cpp
// Classification of each vertex: convex or reflex
// Returns a vector: true = convex vertex, false = reflex vertex
vector<bool> classifyVertices(const vector<Point>& p) {
    int n = p.size();
    vector<bool> isConvexVertex(n);
    
    for (int i = 0; i < n; i++) {
        Point a = p[(i - 1 + n) % n];
        Point b = p[i];
        Point c = p[(i + 1) % n];
        
        long long cp = cross(b - a, c - b);
        // For a CCW polygon: cp > 0 => convex vertex
        isConvexVertex[i] = (cp >= 0);
    }
    
    return isConvexVertex;
}
```

## 6. Polygon Triangulation

Every simple polygon can be divided into triangles. This is useful for many algorithms and visualizations.

### 6.1. Ear Clipping Method
Finds an "ear" (a triangle whose diagonal is inside the polygon) and removes it.

```cpp
// Check whether triangle (a, b, c) contains point p
bool triangleContains(Point a, Point b, Point c, Point p) {
    long long cp1 = cross(b - a, p - a);
    long long cp2 = cross(c - b, p - b);
    long long cp3 = cross(a - c, p - c);
    
    return (cp1 >= 0 && cp2 >= 0 && cp3 >= 0) ||
           (cp1 <= 0 && cp2 <= 0 && cp3 <= 0);
}

// Check whether the diagonal from i to k is valid (does not intersect the polygon)
bool isValidDiagonal(const vector<Point>& p, int i, int k) {
    int n = p.size();
    
    // Check whether the diagonal is inside the polygon
    Point mid = Point((p[i].x + p[k].x) / 2, (p[i].y + p[k].y) / 2);
    if (!isInside(p, mid)) return false;
    
    // Check whether any vertex is inside the triangle
    for (int j = 0; j < n; j++) {
        if (j == i || j == k || j == (i + 1) % n) continue;
        if (triangleContains(p[i], p[(i + 1) % n], p[k], p[j]))
            return false;
    }
    
    return true;
}

// Ear Clipping triangulation (O(n^2) for simple polygons)
vector<array<int, 3>> triangulate(vector<Point> p) {
    vector<array<int, 3>> triangles;
    int n = p.size();
    vector<int> indices(n);
    for (int i = 0; i < n; i++) indices[i] = i;
    
    while (indices.size() > 3) {
        bool found = false;
        for (int i = 0; i < (int)indices.size(); i++) {
            int prev = (i - 1 + indices.size()) % indices.size();
            int next = (i + 1) % indices.size();
            
            Point a = p[indices[prev]];
            Point b = p[indices[i]];
            Point c = p[indices[next]];
            
            // Check whether this is an "ear"
            if (cross(b - a, c - b) > 0) {
                bool isEar = true;
                for (int j = 0; j < (int)indices.size(); j++) {
                    if (j == prev || j == i || j == next) continue;
                    if (triangleContains(a, b, c, p[indices[j]])) {
                        isEar = false;
                        break;
                    }
                }
                
                if (isEar) {
                    triangles.push_back({indices[prev], indices[i], indices[next]});
                    indices.erase(indices.begin() + i);
                    found = true;
                    break;
                }
            }
        }
        if (!found) break; // Cannot be triangulated
    }
    
    if (indices.size() == 3) {
        triangles.push_back({indices[0], indices[1], indices[2]});
    }
    
    return triangles;
}
```

### 6.2. Monotone Triangulation (Advanced)
For convex polygons, triangulation is trivial - all diagonals from one vertex.

```cpp
// Triangulation of a convex polygon from vertex 0
vector<array<int, 3>> triangulateConvex(int n) {
    vector<array<int, 3>> triangles;
    for (int i = 1; i < n - 1; i++) {
        triangles.push_back({0, i, i + 1});
    }
    return triangles;
}
```

## 7. Polygon Decomposition

### 7.1. Decomposition into Convex Parts
A concave polygon can be divided into convex parts.

```cpp
// Finding all reflex vertices
vector<int> findReflexVertices(const vector<Point>& p) {
    vector<int> reflex;
    int n = p.size();
    
    for (int i = 0; i < n; i++) {
        Point a = p[(i - 1 + n) % n];
        Point b = p[i];
        Point c = p[(i + 1) % n];
        
        if (cross(b - a, c - b) < 0) { // For a CCW polygon
            reflex.push_back(i);
        }
    }
    
    return reflex;
}
```

## 8. Practical Applications and Special Cases

### 8.1. Centroid
```cpp
// Centroid of a polygon
Point centroid(const vector<Point>& p) {
    long long cx = 0, cy = 0, area = 0;
    int n = p.size();
    
    for (int i = 0; i < n; i++) {
        long long cp = cross(p[i], p[(i + 1) % n]);
        cx += (p[i].x + p[(i + 1) % n].x) * cp;
        cy += (p[i].y + p[(i + 1) % n].y) * cp;
        area += cp;
    }
    
    // We divide by 3 * area (to avoid division during accumulation)
    return Point(cx / (3 * area), cy / (3 * area));
}
```

### 8.2. Farthest Pair of Points (Rotating Calipers)
For a convex polygon, we can find the farthest pair of points in O(n).

### 8.3. Minkowski Sum
The sum of two polygons $P + Q = \{p + q : p \in P, q \in Q\}$.

### 8.4. Polygon Intersection
Checking whether two polygons intersect or one contains another.

```cpp
// Check whether two convex polygons intersect
bool convexPolygonsIntersect(const vector<Point>& p1, const vector<Point>& p2) {
    // Check whether any vertex of p1 is in p2
    for (const Point& pt : p1) {
        if (inConvexPolygon(p2, pt)) return true;
    }
    
    // Check whether any vertex of p2 is in p1
    for (const Point& pt : p2) {
        if (inConvexPolygon(p1, pt)) return true;
    }
    
    // Check whether any sides intersect
    for (int i = 0; i < (int)p1.size(); i++) {
        for (int j = 0; j < (int)p2.size(); j++) {
            // Check for intersection of segments [p1[i], p1[i+1]] and [p2[j], p2[j+1]]
            // (requires a segment intersection function)
        }
    }
    
    return false;
}
```

## 9. Special Cases and Errors

### Common Errors:
1. **Division by zero** when checking for intersection.
2. **Overflow** - always use `long long` for coordinates.
3. **Rounding errors** - work with double the area (2*S) as long as possible.
4. **Degenerate cases** - collinear points, polygon with area 0.
5. **Edge cases** in Point-in-Polygon - point on an edge or vertex.

### Validity Checks:
```cpp
// Check whether a polygon is valid (does not self-intersect)
bool isSimplePolygon(const vector<Point>& p) {
    int n = p.size();
    // Check for self-intersection of sides
    for (int i = 0; i < n; i++) {
        for (int j = i + 2; j < n; j++) {
            if (j == (i + n - 1) % n) continue; // Adjacent sides
            // Check whether segments [i, i+1] and [j, j+1] intersect
            // (requires a segment intersection function)
        }
    }
    return true;
}

// Check for a degenerate polygon (with area 0)
bool isDegenerate(const vector<Point>& p) {
    return polygonArea2(p) == 0;
}
```

## 10. Tasks
1.  **Codeforces 166B**: Polygon (Point inside convex polygon).
2.  **POJ 1265**: Area (Pick's theorem).
3.  **SPOJ GSSQ**: General queries.

## 11. Complexity and Optimizations

### Time Complexity:
- **Polygon area**: O(n)
- **Perimeter**: O(n)
- **Point in Polygon (Ray Casting)**: O(n)
- **Point in Polygon (convex, binary search)**: O(log n)
- **Convexity check**: O(n)
- **Triangulation (Ear Clipping)**: O(n²)
- **Triangulation (optimal)**: O(n log n)

### Optimization Tips:
1. Use `long long` for coordinates to avoid overflow.
2. Work with double the area (2*S) as long as possible.
3. Cache results like area and perimeter if they are used multiple times.
4. For convex polygons, use specialized algorithms.
5. Avoid floating-point operations when possible.

## 🏁 Conclusion

Polygon geometry is a fundamental topic in competitive programming. The basic concepts include:

✅ **Always work with $2 \cdot S$ (double the area)** to stay within integers (`long long`) until the last moment.

✅ **Watch out for overflow** - use `long long` for all intermediate calculations.

✅ **Test edge cases** - degenerate polygons, collinear points, points on the boundary.

✅ **Use cross product** - it is a basic tool for most polygon operations.

✅ **Understand the difference** between convex and concave polygons - different algorithms have different efficiencies.

Practice with real tasks to reinforce your knowledge!