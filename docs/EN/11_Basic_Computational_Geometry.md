# 📐 Basics of Computational Geometry

Geometry is one of the most challenging topics in competitive programming due to working with floating-point numbers (`double`) and edge cases. This topic requires a precise understanding of mathematical concepts, attention to detail, and knowledge of common errors when working with geometric tasks.

## 1. Points and Vectors

### 1.1. Basic Definitions

A point $P(x, y)$ and a vector $\vec{v}(x, y)$ are represented identically in memory, but they have different mathematical and geometric meanings:

*   **Point**: Represents a specific position in two-dimensional space with coordinates $(x, y)$.
*   **Vector**: Represents a direction and magnitude. A vector is defined as the difference between two points: $\vec{AB} = B - A$.

### 1.2. Full Implementation of the Point Structure

```cpp
struct Point {
    long long x, y; // Use long long whenever possible!
    
    // Constructors
    Point() : x(0), y(0) {}
    Point(long long x, long long y) : x(x), y(y) {}
    
    // Arithmetic operations
    Point operator+(const Point& other) const { 
        return {x + other.x, y + other.y}; 
    }
    
    Point operator-(const Point& other) const { 
        return {x - other.x, y - other.y}; 
    }
    
    // Scalar multiplication
    Point operator*(long long k) const { 
        return {x * k, y * k}; 
    }
    
    // Comparison (for sorting)
    bool operator<(const Point& other) const {
        if (x != other.x) return x < other.x;
        return y < other.y;
    }
    
    bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
    
    // Vector length squared (to avoid sqrt)
    long long lengthSq() const {
        return x * x + y * y;
    }
};
```

### 1.3. Basic Operations with Vectors

```cpp
// Distance between two points squared
long long distSq(Point a, Point b) {
    return (a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y);
}

// Euclidean distance (only if necessary)
double dist(Point a, Point b) {
    return sqrt(distSq(a, b));
}

// Manhattan distance
long long manhattan(Point a, Point b) {
    return abs(a.x - b.x) + abs(a.y - b.y);
}
```

## 2. Dot Product

### 2.1. Mathematical Definition

The dot product of two vectors $A$ and $B$ is defined as:
$$A \cdot B = x_A x_B + y_A y_B = |A| |B| \cos \theta$$ 

where $\theta$ is the angle between the two vectors.

### 2.2. Geometric Properties

*   If $A \cdot B > 0$: The angle $\theta$ is **acute** ($< 90^\circ$).
*   If $A \cdot B = 0$: The vectors are **perpendicular** (orthogonal).
*   If $A \cdot B < 0$: The angle $\theta$ is **obtuse** ($> 90^\circ$).

### 2.3. Applications

**Vector projection:** The projection of vector $A$ onto vector $B$ is:
$$\text{proj}_B A = \frac{A \cdot B}{|B|^2} \cdot B$$ 

**Finding the angle between vectors:**
$$\cos \theta = \frac{A \cdot B}{|A| |B|}$$ 

```cpp
long long dot(Point a, Point b) {
    return a.x * b.x + a.y * b.y;
}

// Angle between two vectors (in radians)
double angle(Point a, Point b) {
    return acos(dot(a, b) / (sqrt(a.lengthSq()) * sqrt(b.lengthSq())));
}

// Check whether two vectors are perpendicular
bool isPerpendicular(Point a, Point b) {
    return dot(a, b) == 0;
}
```

## 3. Cross Product

### 3.1. Mathematical Definition

In 2D, the cross product is a **scalar value** (in 3D, it is a vector):
$$A \times B = x_A y_B - x_B y_A = |A| |B| \sin \theta$$ 

where $\theta$ is the angle between the two vectors.

### 3.2. Geometric Properties

*   **Geometric significance**: The oriented area of the parallelogram formed by vectors $A$ and $B$.
*   **Triangle area** with vertices $(0,0), A, B$: $\frac{|A \times B|}{2}$.
*   **Sign**: 
    - Positive: $B$ is **to the left** of $A$ (counter-clockwise - CCW).
    - Negative: $B$ is **to the right** of $A$ (clockwise - CW).
    - Zero: the vectors are **collinear** (parallel).

### 3.3. Implementation and Applications

```cpp
long long cross(Point a, Point b) {
    return a.x * b.y - a.y * b.x;
}

// Area of triangle ABC (can be negative)
long long triangleArea2(Point a, Point b, Point c) {
    return cross(b - a, c - a);
}

// Absolute area of a triangle
long long absTriangleArea2(Point a, Point b, Point c) {
    return abs(triangleArea2(a, b, c));
}

// Polygon area using the Shoelace formula
long long polygonArea2(const vector<Point>& poly) {
    long long area = 0;
    int n = poly.size();
    for (int i = 0; i < n; i++) {
        int j = (i + 1) % n;
        area += cross(poly[i], poly[j]);
    }
    return abs(area);
}
```

## 4. Orientation of Three Points (CCW Test)

### 4.1. Basic Concept

Where is point $C$ relative to the oriented line $\overrightarrow{AB}$?

We use the cross product of $\vec{AB}$ and $\vec{AC}$:
$$\text{orient}(A, B, C) = (B.x - A.x)(C.y - A.y) - (B.y - A.y)(C.x - A.x)$$ 

### 4.2. Interpretation of the Result

*   $\text{orient}(A,B,C) > 0$: $C$ is **to the left** of $\overrightarrow{AB}$ (counter-clockwise - CCW).
*   $\text{orient}(A,B,C) < 0$: $C$ is **to the right** of $\overrightarrow{AB}$ (clockwise - CW).
*   $\text{orient}(A,B,C) = 0$: The points $A, B, C$ are **collinear** (lie on one line).

### 4.3. Implementation

```cpp
long long orient(Point a, Point b, Point c) {
    return cross(b - a, c - a);
}

// Check whether a point lies on a segment
bool onSegment(Point a, Point b, Point c) {
    // First we check whether they are collinear
    if (orient(a, b, c) != 0) return false;
    
    // Then we check whether c is between a and b
    return min(a.x, b.x) <= c.x && c.x <= max(a.x, b.x) &&
           min(a.y, b.y) <= c.y && c.y <= max(a.y, b.y);
}
```

## 5. Distances and Metrics

### 5.1. Euclidean Distance

The classical distance between two points in the plane:
$$d(P_1, P_2) = \sqrt{(x_1-x_2)^2 + (y_1-y_2)^2}$$ 

**Important**: Always use the squared distance in comparisons to avoid the `sqrt` operation and floating-point errors!

### 5.2. Manhattan Distance

The distance measured along horizontal and vertical lines (as in a street grid):
$$d_M(P_1, P_2) = |x_1-x_2| + |y_1-y_2|$$ 

### 5.3. Distance from a Point to a Line

To find the distance from point $P$ to a line passing through points $A$ and $B$:

$$d = \frac{|\text{orient}(A, B, P)|}{|AB|}$$ 

```cpp
// Distance from a point to a line (as squared for accuracy)
long long distToLineSq(Point p, Point a, Point b) {
    long long num = abs(orient(a, b, p));
    long long den = distSq(a, b);
    // Return num^2 / den to avoid double
    return (num * num) / den;
}

// Distance from a point to a segment
double distToSegment(Point p, Point a, Point b) {
    if (a == b) return dist(p, a);
    
    Point ab = b - a;
    Point ap = p - a;
    
    // Projection of ap onto ab
    long long t = dot(ap, ab);
    
    if (t <= 0) return dist(p, a);  // Closest to a
    
    long long abLen = ab.lengthSq();
    if (t >= abLen) return dist(p, b);  // Closest to b
    
    // Perpendicular projection
    return abs(cross(ab, ap)) / sqrt(abLen);
}
```

## 6. Intersection of Segments

### 6.1. General Case

Two segments $AB$ and $CD$ intersect if:
1. Points $C$ and $D$ are on **different sides** of the line $AB$, **AND**
2. Points $A$ and $B$ are on **different sides** of the line $CD$.

```cpp
bool segmentsIntersect(Point a, Point b, Point c, Point d) {
    long long o1 = orient(a, b, c);
    long long o2 = orient(a, b, d);
    long long o3 = orient(c, d, a);
    long long o4 = orient(c, d, b);
    
    // General case
    if (o1 * o2 < 0 && o3 * o4 < 0) return true;
    
    // Special cases - collinear points
    if (o1 == 0 && onSegment(a, b, c)) return true;
    if (o2 == 0 && onSegment(a, b, d)) return true;
    if (o3 == 0 && onSegment(c, d, a)) return true;
    if (o4 == 0 && onSegment(c, d, b)) return true;
    
    return false;
}
```

### 6.2. Finding the Intersection Point

```cpp
// Finding the intersection point (with double for accuracy)
pair<double, double> intersectionPoint(Point a, Point b, Point c, Point d) {
    double a1 = b.y - a.y;
    double b1 = a.x - b.x;
    double c1 = a1 * a.x + b1 * a.y;
    
    double a2 = d.y - c.y;
    double b2 = c.x - d.x;
    double c2 = a2 * c.x + b2 * c.y;
    
    double det = a1 * b2 - a2 * b1;
    
    double x = (b2 * c1 - b1 * c2) / det;
    double y = (a1 * c2 - a2 * c1) / det;
    
    return {x, y};
}
```

## 7. Polygons

### 7.1. Checking Whether a Point is Inside a Polygon

**Ray Casting Method:** We fire a ray from the point to the right and count the intersections with the edges. If they are an odd number, the point is inside.

```cpp
bool pointInPolygon(Point p, const vector<Point>& poly) {
    int n = poly.size();
    int count = 0;
    
    for (int i = 0; i < n; i++) {
        int j = (i + 1) % n;
        
        // We check whether the ray intersects the edge poly[i]-poly[j]
        if ((poly[i].y <= p.y && p.y < poly[j].y) ||
            (poly[j].y <= p.y && p.y < poly[i].y)) {
            
            // Calculate the x-coordinate of the intersection
            long long x = poly[i].x + 
                (p.y - poly[i].y) * (poly[j].x - poly[i].x) / 
                (poly[j].y - poly[i].y);
            
            if (p.x < x) count++;
        }
    }
    
    return count % 2 == 1;
}
```

### 7.2. Checking Whether a Polygon is Convex

A polygon is convex if all turns are in the same direction (all CCW or all CW).

```cpp
bool isConvex(const vector<Point>& poly) {
    int n = poly.size();
    if (n < 3) return false;
    
    int sign = 0;
    for (int i = 0; i < n; i++) {
        long long o = orient(poly[i], poly[(i+1)%n], poly[(i+2)%n]);
        
        if (o != 0) {
            if (sign == 0) sign = (o > 0) ? 1 : -1;
            else if ((o > 0 ? 1 : -1) != sign) return false;
        }
    }
    return true;
}
```

## 8. Convex Hull

### 8.1. Graham Scan Algorithm

This algorithm finds the convex hull of a set of points with a time complexity of $O(n \log n)$.

```cpp
// Finding the convex hull with Graham Scan
vector<Point> convexHull(vector<Point> points) {
    int n = points.size();
    if (n < 3) return points;
    
    // Find the bottommost point (if ties - the leftmost)
    int minIdx = 0;
    for (int i = 1; i < n; i++) {
        if (points[i].y < points[minIdx].y || 
            (points[i].y == points[minIdx].y && points[i].x < points[minIdx].x)) {
            minIdx = i;
        }
    }
    swap(points[0], points[minIdx]);
    Point pivot = points[0];
    
    // Sorting by polar angle relative to the pivot
    sort(points.begin() + 1, points.end(), [&](Point a, Point b) {
        long long o = orient(pivot, a, b);
        if (o == 0) {
            // Collinear - the closer one first
            return distSq(pivot, a) < distSq(pivot, b);
        }
        return o > 0;
    });
    
    // Build the hull
    vector<Point> hull;
    for (Point p : points) {
        // Remove points that make a right turn
        while (hull.size() > 1 && 
               orient(hull[hull.size()-2], hull[hull.size()-1], p) <= 0) {
            hull.pop_back();
        }
        hull.push_back(p);
    }
    
    return hull;
}
```

### 8.2. Andrew's Algorithm (Monotone Chain)

A simpler and more stable algorithm with the same $O(n \log n)$ complexity.

```cpp
vector<Point> convexHullAndrew(vector<Point> points) {
    int n = points.size();
    if (n < 3) return points;
    
    sort(points.begin(), points.end());
    
    // Build the lower part of the hull
    vector<Point> lower;
    for (int i = 0; i < n; i++) {
        while (lower.size() >= 2 && 
               orient(lower[lower.size()-2], lower[lower.size()-1], points[i]) <= 0) {
            lower.pop_back();
        }
        lower.push_back(points[i]);
    }
    
    // Build the upper part of the hull
    vector<Point> upper;
    for (int i = n - 1; i >= 0; i--) {
        while (upper.size() >= 2 && 
               orient(upper[upper.size()-2], upper[upper.size()-1], points[i]) <= 0) {
            upper.pop_back();
        }
        upper.push_back(points[i]);
    }
    
    // Remove the last points (duplicated)
    lower.pop_back();
    upper.pop_back();
    
    // Merge both parts
    lower.insert(lower.end(), upper.begin(), upper.end());
    return lower;
}
```

## 9. Practical Applications

### 9.1. Determining Triangle Type

```cpp
string triangleType(Point a, Point b, Point c) {
    long long ab = distSq(a, b);
    long long bc = distSq(b, c);
    long long ca = distSq(c, a);
    
    // Check whether it is a right triangle (Pythagorean theorem)
    if (ab + bc == ca || bc + ca == ab || ca + ab == bc) {
        return "Right-angled";
    }
    
    // Check using dot product
    Point ab_vec = b - a, ac_vec = c - a;
    long long d1 = dot(ab_vec, ac_vec);
    
    if (d1 < 0) return "Obtuse";
    if (d1 > 0) return "Acute";
    
    return "Right-angled";
}
```

### 9.2. Finding the Centroid

```cpp
Point centroid(const vector<Point>& poly) {
    long long cx = 0, cy = 0;
    long long area = 0;
    int n = poly.size();
    
    for (int i = 0; i < n; i++) {
        int j = (i + 1) % n;
        long long cross_prod = cross(poly[i], poly[j]);
        area += cross_prod;
        cx += (poly[i].x + poly[j].x) * cross_prod;
        cy += (poly[i].y + poly[j].y) * cross_prod;
    }
    
    area /= 2;
    cx /= (6 * area);
    cy /= (6 * area);
    
    return Point(cx, cy);
}
```

## 10. Edge Cases and Common Errors

### 10.1. Edge Cases

1. **Collinear points**: Always check the case where three points lie on one line.
2. **Vertical/horizontal lines**: Be careful with division by zero.
3. **Coincident points**: Check whether two points are identical.
4. **Degenerate polygons**: A polygon with area 0 or with fewer than 3 vertices.

### 10.2. Numerical Stability

```cpp
// Constant for comparing double values
const double EPS = 1e-9;

// Safe comparison
bool equals(double a, double b) {
    return abs(a - b) < EPS;
}

bool lessOrEqual(double a, double b) {
    return a < b + EPS;
}
```

### 10.3. Overflow in Cross Product

When coordinates are up to $10^9$, the cross product can reach $10^{18}$, which fits in `long long`. But for larger values or multiple operations, watch out for overflow!

```cpp
// If coordinates are very large, use __int128
__int128 safeCross(Point a, Point b) {
    return (__int128)a.x * b.y - (__int128)a.y * b.x;
}
```
## 11. Practice Tasks

### 11.1. Basic Tasks

1.  **Codeforces 1C - Ancient Berland Circus**: Finding the area of the smallest regular polygon that contains three given points as vertices. (Areas and radii of circumscribed circles)

2.  **UVa 191 - Intersection**: Checking whether a segment intersects a rectangle. (Segment intersection and point in polygon)

3.  **Codeforces 166B - Polygons**: Checking whether one polygon is entirely within another. (Convex polygons)

### 11.2. Convex Hull Tasks

4.  **SPOJ - BSHEEP**: Finding the convex hull and calculating its perimeter.

5.  **Codeforces 685B - Kay and Snowflake**: Working with centroids of trees from polygons.

6.  **UVa 11265 - The Sultan's Problem**: Intersection of convex polygons.

### 11.3. Advanced Tasks

7.  **Codeforces 769C - Cycle**: Finding a cycle in a graph using geometric properties.

8.  **SPOJ - CLOSEST**: Finding the closest pair of points (Divide and Conquer with geometry).

9.  **IOI 2013 - Art Class**: Classification of pictures with geometric parameter values.

10. **BOI 2007 - Sound**: Segment intersection with angles and distances.

## 12. Tips for Success

### 12.1. Avoiding Floating-Point Numbers

1.  **Use `long long`** whenever possible. Cross product, dot product, and orientation can be calculated with integers.

2.  **Compare squared distances** instead of the distances themselves:
    ```cpp
    // Bad
    if (dist(a, b) < dist(c, d)) { ... }
    
    // Good
    if (distSq(a, b) < distSq(c, d)) { ... }
    ```

3.  **Avoid `sqrt`** whenever possible.

### 12.2. Working with Double (when inevitable)

1.  **Always use EPS** for comparison:
    ```cpp
    const double EPS = 1e-9;
    if (abs(a - b) < EPS) { // They are equal }
    ```

2.  **Be careful with division**:
    ```cpp
    // Check for zero before division
    if (abs(denominator) > EPS) {
        result = numerator / denominator;
    }
    ```

3.  **Use `long double`** for higher precision if needed.

### 12.3. Debugging Geometric Tasks

1.  **Visualize**: Draw the points and figures on paper or use a graphic tool.

2.  **Test with small examples**: Start with triangles and squares before moving to complex polygons.

3.  **Check edge cases**:
    - Collinear points
    - Vertical and horizontal lines
    - Coincident points
    - Degenerate figures (area 0)

4.  **Check signs**: In cross products, the sign matters!

### 12.4. Optimization

1.  **Precomputations**: If you use the same distances/angles multiple times, calculate them in advance.

2.  **Avoid expensive operations**: `sqrt`, `sin`, `cos`, `atan2` are slow. Use them only when necessary.

3.  **Use appropriate data structures**: For checking many points in a polygon, consider faster methods like triangulation.

## 🏁 Final Advice

✅ **Do**:
- Use `long long` for coordinates
- Compare squared distances
- Test edge cases
- Check the orientation of points
- Visualize the task

❌ **Avoid**:
- `double` without EPS in comparisons
- Unnecessary use of `sqrt`
- Forgetting collinear cases
- Overflow with large coordinates
- Division by zero

### Useful Formulas for Reference

**Areas**:
- Triangle: $\frac{|cross(AB, AC)|}{2}$
- Polygon (Shoelace): $\frac{1}{2}|\sum_{i=0}^{n-1} (x_i y_{i+1} - x_{i+1} y_i)|$

**Distances**:
- Euclidean: $\sqrt{(x_1-x_2)^2 + (y_1-y_2)^2}$
- Manhattan: $|x_1-x_2| + |y_1-y_2|$
- Point to line: $\frac{|ax_0 + by_0 + c|}{\sqrt{a^2 + b^2}}$

**Vector operations**:
- Dot product: $A \cdot B = |A||B|\cos\theta$
- Cross product: $A \times B = |A||B|\sin\theta$
- Length: $|A| = \sqrt{x^2 + y^2}$

Good luck with the Olympiads! 🚀
