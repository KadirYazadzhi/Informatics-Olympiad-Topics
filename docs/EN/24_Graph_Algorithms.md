# 🌐 Graph Algorithms: Advanced Methods

## 🛣️ Shortest Paths - Special Cases

### 0-1 BFS
If edge weights are only 0 and 1, we can find the shortest path in $O(V+E)$ using a `deque`.
- If weight is 0: `push_front`
- If weight is 1: `push_back`
This maintains the sorted property of the queue.

### SPFA (Shortest Path Faster Algorithm)
An improvement of Bellman-Ford, which in practice often runs in $O(kE)$ ($k$ is small constant), but in worst case is $O(VE)$.
- Uses a queue for vertices whose distance has changed (relaxed).
- **Warning**: Easy to create "killer tests" that slow it down. Dijkstra is preferred unless there are negative weights.

### Johnson's Algorithm
Finds all-pairs shortest paths in a graph with negative weights (no negative cycles).
1. Add a dummy vertex $S$, connected to all with weight 0.
2. Run Bellman-Ford from $S$ to find potentials $h(v)$.
3. Reweight edges: $w'(u, v) = w(u, v) + h(u) - h(v) \ge 0$.
4. Run Dijkstra $V$ times.
- Complexity: $O(V E \log V)$.

---

## 🌳 Tree Center and Diameter

### Diameter
The longest path between two vertices in a tree.
**Algorithm (2 BFS/DFS)**:
1. Start BFS from random node $u$ and find the farthest node $x$.
2. Start BFS from $x$ and find the farthest node $y$.
3. Distance between $x$ and $y$ is the diameter.

### Center
A vertex (or two adjacent ones) that minimizes the maximum distance to any other vertex.
- Found as the midpoint of the diameter.

---

## 🔍 Lowest Common Ancestor (LCA)

The lowest common ancestor of two nodes $u$ and $v$ in a rooted tree.
**Method with Binary Lifting**:
- `up[u][i]` is the $2^i$-th ancestor of $u$.
- Calculation: `up[u][i] = up[up[u][i-1]][i-1]`.
- Finding LCA:
  1. Equalize depths of $u$ and $v$.
  2. "Lift" both nodes simultaneously with large steps ($2^k$) until they become children of LCA.
  3. `up[u][0]` is the sought LCA.
- Complexity: $O(N \log N)$ preprocessing, $O(\log N)$ query.

---

## ♾️ Eulerian Path - Hierholzer's Algorithm

Finds an Eulerian cycle in $O(E)$.
1. Start from a valid vertex.
2. Walk along unused edges until stuck (return to start). This forms a cycle.
3. If there are vertices on this cycle with unused edges, start a new sub-cycle from them and splice it in.

---

## 🏁 Conclusion

Graphs are a vast topic. Binary Lifting is a technique used not only for LCA but for any path query in a tree (min, sum). 0-1 BFS is a very common trick in Grid-based problems.
