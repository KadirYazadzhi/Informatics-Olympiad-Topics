# 📐 Basic Computational Geometry

Geometry problems are challenging due to floating-point precision errors and corner cases. Always prefer integer arithmetic (`long long`) when coordinates are integers.

## 1. Points and Vectors

*   **Point**: A location $(x, y)$.
*   **Vector**: A direction and magnitude $(x, y)$. Represented as $B - A$.

```cpp
struct Point {
    long long x, y;
    Point operator+(Point p) const { return {x+p.x, y+p.y}; }
    Point operator-(Point p) const { return {x-p.x, y-p.y}; }
};
```

## 2. Dot Product
$A \cdot B = x_A x_B + y_A y_B = |A||B|\cos \theta$.
*   Positive: Acute angle.
*   Zero: Perpendicular vectors.
*   Negative: Obtuse angle.

## 3. Cross Product
$A \times B = x_A y_B - x_B y_A = |A||B|\sin \theta$.
*   Represents the **signed area** of the parallelogram spanned by $A$ and $B$.
*   Area of Triangle $ABC = \frac{|(B-A) \times (C-A)|}{2}$.

## 4. Orientation (CCW)
Determines the relative position of point $C$ with respect to vector $\vec{AB}$.
$val = (B.x - A.x)(C.y - A.y) - (B.y - A.y)(C.x - A.x)$

*   $val > 0$: Left Turn (Counter-Clockwise).
*   $val < 0$: Right Turn (Clockwise).
*   $val = 0$: Collinear.

## 5. Intersection of Segments
Two segments $AB$ and $CD$ intersect if and only if:
1.  $C$ and $D$ are on opposite sides of line $AB$.
2.  $A$ and $B$ are on opposite sides of line $CD$.
Special care needed for collinear cases (bounding box check).

## 6. Tips & Tricks
1.  **Precision**: Never use `==` for doubles. Use `abs(a-b) < EPS`.
2.  **Distance**: Compare squared distances to avoid `sqrt`.
3.  **Polygon Area**: Shoelace formula $\frac{1}{2} |\sum (x_i y_{i+1} - x_{i+1} y_i)|$.

## 7. Practice
1.  **UVa 191**: Intersection.
2.  **SPOJ GSSQ**: Geometry basics.
