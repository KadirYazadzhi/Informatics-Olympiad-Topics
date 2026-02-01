# 📐 Rectangles and Coordinate Geometry

Axis-Aligned Bounding Boxes (AABB) are the most common geometric objects in competitive programming.

## 1. Representation

Best stored via bottom-left $(x_1, y_1)$ and top-right $(x_2, y_2)$ coordinates.
Ensure $x_1 \le x_2$ and $y_1 \le y_2$.

```cpp
struct Rect {
    long long x1, y1, x2, y2;
    long long area() const { 
        return max(0LL, x2 - x1) * max(0LL, y2 - y1); 
    }
};
```

## 2. Operations

### 2.1. Intersection
The intersection of two rectangles is also a rectangle (or empty).
Take `max` of left/bottom and `min` of right/top coordinates.

```cpp
Rect intersect(const Rect& a, const Rect& b) {
    Rect res;
    res.x1 = max(a.x1, b.x1);
    res.y1 = max(a.y1, b.y1);
    res.x2 = min(a.x2, b.x2);
    res.y2 = min(a.y2, b.y2);
    if (res.x1 >= res.x2 || res.y1 >= res.y2) return {0,0,0,0};
    return res;
}
```

### 2.2. Union Area
$S(A \cup B) = S(A) + S(B) - S(A \cap B)$.
For $N$ rectangles, use **Sweep-Line Algorithm** (Topic 27).

### 2.3. Point in Rectangle
`return x >= r.x1 && x <= r.x2 && y >= r.y1 && y <= r.y2;`

## 3. 2D Prefix Sums

To answer sum queries in subrectangles in $O(1)$.
Precomputation: $O(NM)$.

**Build**: `pref[i][j] = matrix[i][j] + pref[i-1][j] + pref[i][j-1] - pref[i-1][j-1]`
**Query**: `sum = pref[r2][c2] - pref[r1-1][c2] - pref[r2][c1-1] + pref[r1-1][c1-1]`

## 4. Coordinate Compression

If coordinates are up to $10^9$ but $N \le 1000$:
1.  Collect all $x_1, x_2$ into a vector.
2.  Sort and remove duplicates (`unique`).
3.  Replace original coords with their rank (index in sorted vector).
Reduces grid to $2N \times 2N$.

## 5. Practice
1.  **CSES Forest Queries**: 2D Prefix Sums.
2.  **Codeforces 1195C**: Basketball Exercise.
3.  **IOI 2013 Dreaming**.

```