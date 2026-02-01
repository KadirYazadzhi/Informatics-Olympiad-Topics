# 📐 Geometry: Polygons and Lattices

## 🕸️ Pick's Theorem

Allows finding the area of a polygon whose vertices lie on an integer lattice.
$$S = I + \frac{B}{2} - 1$$
- $S$: Area of the polygon.
- $I$: Number of interior integer points.
- $B$: Number of integer points on the boundary.

$B$ is easily found: for a segment between $(x_1, y_1)$ and $(x_2, y_2)$, the number of points is $\gcd(|x_1-x_2|, |y_1-y_2|)$.
$S$ is found via Shoelace formula.
From this, we can find $I = S - B/2 + 1$.

---

## ✂️ Half-plane Intersection

Every line $Ax + By + C \le 0$ defines a half-plane. The intersection of $N$ half-planes is always a **convex region** (can be unbounded or empty).
- Used for finding the kernel of a polygon or problems like "Where can a camera be placed to see everything?".
- **Algorithm**: Sort lines by angle and use a `deque`. Complexity $O(N \log N)$.

---

## 🐢 Rotating Calipers

A method for solving problems on convex polygons in $O(N)$.
Idea: "Rotate" two parallel supporting lines around the polygon.

**Applications**:
1. **Diameter (Farthest Pair)**: Maximum distance between two points.
2. **Width of Polygon**: Minimum distance between two parallel supporting lines.
3. **Minimum Bounding Rectangle**: Rectangle with minimum area containing the polygon.

---

## 🏁 Conclusion

Pick's Theorem is mandatory for lattice problems. Rotating Calipers is an elegant method transforming $O(N^2)$ search into $O(N)$, but requires careful implementation of angles.
