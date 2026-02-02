# 🧹 Sweep Line Method

The sweep line method is one of the most powerful techniques in computational geometry. The basic idea is to move a vertical (or horizontal) line across the plane and process geometric objects at the moment the line "touches" them.

---

## 1. Concept

Sweep line transforms a 2D static problem into a 1D dynamic problem.

*   **Events**: The points where something interesting happens (start/end of a segment, position of a point). We sort these events by $x$-coordinate.
*   **Status**: A data structure (usually `std::set` or Segment Tree) that stores information about the objects currently intersected by the line.

### General Template

```cpp
struct Event {
    int x;
    int type; // Type of event
    // Additional information
    
    bool operator<(const Event& other) const {
        if (x != other.x) return x < other.x;
        return type < other.type;
    }
};

void sweepLine(vector<Event>& events) {
    sort(events.begin(), events.end());
    
    set<int> status; // Or another suitable structure
    
    for (const Event& e : events) {
        // Processing according to the event type
        if (e.type == START) {
            status.insert(/* ... */);
        } else if (e.type == END) {
            status.erase(/* ... */);
        }
        // Calculations based on current status
    }
}
```

---

## 2. Closest Pair of Points

Given $N$ points. We search for the two with the minimum distance between them.
Complexity: $O(N \log N)$.

### Algorithm with Divide and Conquer + Sweep

```cpp
struct Point {
    long long x, y;
    
    double dist(const Point& other) const {
        long long dx = x - other.x;
        long long dy = y - other.y;
        return sqrt(dx * dx + dy * dy);
    }
};

double closestPair(vector<Point>& pts) {
    int n = pts.size();
    if (n < 2) return 1e18;
    
    // Sort by x
    sort(pts.begin(), pts.end(), [](const Point& a, const Point& b) {
        return a.x < b.x;
    });
    
    set<pair<long long, long long>> s; // {y, x}
    double minDist = 1e18;
    int j = 0;
    
    for (int i = 0; i < n; i++) {
        // Remove points outside the window
        while (j < i && pts[i].x - pts[j].x >= minDist) {
            s.erase({pts[j].y, pts[j].x});
            j++;
        }
        
        // Search in the range [y - minDist, y + minDist]
        auto it1 = s.lower_bound({pts[i].y - (long long)minDist - 1, LLONG_MIN});
        auto it2 = s.upper_bound({pts[i].y + (long long)minDist + 1, LLONG_MAX});
        
        for (auto it = it1; it != it2; it++) {
            Point p2 = {it->second, it->first};
            minDist = min(minDist, pts[i].dist(p2));
        }
        
        s.insert({pts[i].y, pts[i].x});
    }
    
    return minDist;
}
```

---

## 3. Union Area of Rectangles

We have $N$ rectangles. We seek the area covered by at least one of them.

### Algorithm with Segment Tree

```cpp
struct Rectangle {
    int x1, y1, x2, y2;
};

struct Event {
    int x, y1, y2, type; // type: +1 for left side, -1 for right side
    
    bool operator<(const Event& other) const {
        return x < other.x;
    }
};

class SegmentTree {
    vector<int> cnt;    // How many times it is covered
    vector<long long> len; // Total length of covered parts
    vector<int> coord;  // Compressed coordinates
    
    void update(int v, int l, int r, int ql, int qr, int val) {
        if (qr <= l || r <= ql) return;
        
        if (ql <= l && r <= qr) {
            cnt[v] += val;
        } else {
            int mid = (l + r) / 2;
            update(2*v, l, mid, ql, qr, val);
            update(2*v+1, mid, r, ql, qr, val);
        }
        
        if (cnt[v] > 0) {
            len[v] = coord[r] - coord[l];
        } else if (r - l == 1) {
            len[v] = 0;
        } else {
            len[v] = len[2*v] + len[2*v+1];
        }
    }
    
public:
    SegmentTree(vector<int>& y_coords) : coord(y_coords) {
        int n = y_coords.size();
        cnt.resize(4 * n);
        len.resize(4 * n);
    }
    
    void update(int y1, int y2, int val) {
        int n = coord.size();
        int pos1 = lower_bound(coord.begin(), coord.end(), y1) - coord.begin();
        int pos2 = lower_bound(coord.begin(), coord.end(), y2) - coord.begin();
        update(1, 0, n - 1, pos1, pos2, val);
    }
    
    long long getLength() {
        return len[1];
    }
};

long long rectangleUnionArea(vector<Rectangle>& rects) {
    vector<Event> events;
    set<int> y_set;
    
    for (auto& r : rects) {
        events.push_back({r.x1, r.y1, r.y2, 1});
        events.push_back({r.x2, r.y1, r.y2, -1});
        y_set.insert(r.y1);
        y_set.insert(r.y2);
    }
    
    sort(events.begin(), events.end());
    vector<int> y_coords(y_set.begin(), y_set.end());
    
    SegmentTree tree(y_coords);
    long long totalArea = 0;
    
    for (int i = 0; i < events.size(); i++) {
        if (i > 0) {
            totalArea += tree.getLength() * (events[i].x - events[i-1].x);
        }
        tree.update(events[i].y1, events[i].y2, events[i].type);
    }
    
    return totalArea;
}
```

---

## 4. Convex Hull - Andrew's Algorithm

```cpp
struct Point {
    long long x, y;
    
    Point operator-(const Point& other) const {
        return {x - other.x, y - other.y};
    }
    
    long long cross(const Point& other) const {
        return x * other.y - y * other.x;
    }
};

// Counter-clockwise turn check
long long ccw(const Point& a, const Point& b, const Point& c) {
    Point ab = b - a;
    Point ac = c - a;
    return ab.cross(ac);
}

vector<Point> convexHull(vector<Point> points) {
    int n = points.size();
    if (n <= 1) return points;
    
    sort(points.begin(), points.end(), [](const Point& a, const Point& b) {
        return a.x < b.x || (a.x == b.x && a.y < b.y);
    });
    
    vector<Point> hull;
    
    // Lower chain
    for (int i = 0; i < n; i++) {
        while (hull.size() >= 2 && 
               ccw(hull[hull.size()-2], hull[hull.size()-1], points[i]) <= 0) {
            hull.pop_back();
        }
        hull.push_back(points[i]);
    }
    
    // Upper chain
    int lowerSize = hull.size();
    for (int i = n - 2; i >= 0; i--) {
        while (hull.size() > lowerSize && 
               ccw(hull[hull.size()-2], hull[hull.size()-1], points[i]) <= 0) {
            hull.pop_back();
        }
        hull.push_back(points[i]);
    }
    
    hull.pop_back(); // Remove the duplicated starting point
    return hull;
}
```

---

## 5. Segment Intersection (Bentley-Ottmann)

Finds all $K$ intersection points of $N$ segments.

```cpp
struct Segment {
    Point a, b;
    int id;
    
    double yAtX(double x) const {
        if (a.x == b.x) return a.y;
        return a.y + (b.y - a.y) * (x - a.x) / (b.x - a.x);
    }
};

struct SegmentComparator {
    double sweepX;
    
    bool operator()(const Segment& s1, const Segment& s2) const {
        double y1 = s1.yAtX(sweepX);
        double y2 = s2.yAtX(sweepX);
        if (abs(y1 - y2) > 1e-9) return y1 < y2;
        return s1.id < s2.id;
    }
};

bool segmentsIntersect(const Segment& s1, const Segment& s2, Point& intersection) {
    long long d1 = ccw(s1.a, s1.b, s2.a);
    long long d2 = ccw(s1.a, s1.b, s2.b);
    long long d3 = ccw(s2.a, s2.b, s1.a);
    long long d4 = ccw(s2.a, s2.b, s1.b);
    
    if (((d1 > 0 && d2 < 0) || (d1 < 0 && d2 > 0)) &&
        ((d3 > 0 && d4 < 0) || (d3 < 0 && d4 > 0))) {
        // Calculation of intersection point
        return true;
    }
    return false;
}
```

---

## 6. Number of Points Under a Line

Uses sweep line to count points under a given line:

```cpp
long long pointsBelowLine(vector<Point>& points, Point lineStart, Point lineEnd) {
    sort(points.begin(), points.end(), [](const Point& a, const Point& b) {
        return a.x < b.x || (a.x == b.x && a.y < b.y);
    });
    
    long long count = 0;
    
    for (const Point& p : points) {
        // We check if the point is under the line
        if (ccw(lineStart, lineEnd, p) < 0) {
            count++;
        }
    }
    
    return count;
}
```

---

## 7. Points Inside a Polygon

```cpp
bool pointInPolygon(const Point& p, const vector<Point>& polygon) {
    int n = polygon.size();
    int crossings = 0;
    
    for (int i = 0; i < n; i++) {
        Point a = polygon[i];
        Point b = polygon[(i + 1) % n];
        
        if ((a.y <= p.y && p.y < b.y) || (b.y <= p.y && p.y < a.y)) {
            double x = a.x + (p.y - a.y) * (b.x - a.x) / (b.y - a.y);
            if (p.x < x) {
                crossings++;
            }
        }
    }
    
    return crossings % 2 == 1;
}
```

---

## 8. Maximum Perimeter

Finding a triangle with the maximum perimeter from given points:

```cpp
double maxTrianglePerimeter(vector<Point>& points) {
    int n = points.size();
    double maxPerimeter = 0;
    
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            for (int k = j + 1; k < n; k++) {
                double p = points[i].dist(points[j]) + 
                          points[j].dist(points[k]) + 
                          points[k].dist(points[i]);
                maxPerimeter = max(maxPerimeter, p);
            }
        }
    }
    
    return maxPerimeter;
}
```

---

## 9. Practical Tips

1. **Coordinates**: Use `long long` for precise calculations, `double` only when necessary.
2. **Epsilon**: When comparing `double`, use a tolerance: `abs(a - b) < 1e-9`.
3. **Degenerate cases**: Be careful with collinear points and vertical lines.
4. **Sorting**: Always sort events correctly (by x, then by type).
5. **Status**: Choose the correct structure (set, multiset, segment tree).

## 10. Practice Tasks

1. **SPOJ CLOPPAIR**: Closest Pair of Points.
2. **USACO Window Area**: Union of Rectangles.
3. **Codeforces 1027D**: Cycle Detection.
4. **CSES Convex Hull**: Basic Convex Hull.
5. **POJ 1113**: Wall (Convex Hull + Circle).
6. **UVA 10256**: The Great Divide (Convex Hull Separation).

## 🏁 Conclusion

Sweep Line is a powerful technique that transforms complex 2D problems into simpler 1D tasks. The key is the correct choice of events and status structure. With practice, you will develop an intuition for when and how to apply this technique. Always think about what changes when the line passes through a given point and how you can efficiently update the status! 🚀