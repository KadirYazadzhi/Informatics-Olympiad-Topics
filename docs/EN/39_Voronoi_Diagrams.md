# 📍 Voronoi Diagrams and KD-Trees

## 🔍 Nearest Neighbor Search

If we don't have time to build Voronoi ($O(N \log N)$ is hard to implement), we can use **KD-Tree** (k-dimensional tree).
- Binary tree splitting space.
- Even levels split by X, odd by Y.
- Nearest point search is fast "on average" (but $O(N)$ worst case).

## 🚀 Fortune's Algorithm (Idea)

To achieve $O(N \log N)$, the "Sweep Line" is not a line but a "Beach Line" made of parabolic arcs.
- Intersection points of parabolas (breakpoints) trace the diagram edges.
- Events:
  1. **Site Event**: Line hits new point $\to$ new parabola appears.
  2. **Circle Event**: Three parabolas meet $\to$ parabola disappears, Voronoi vertex formed.

---

## 🏁 Conclusion

In competitions, full Voronoi implementation is suicide. Usually, KD-Tree is used, or reduction to simpler conditions (e.g., Manhattan distance allowing coordinate rotation and Sweep-line).
