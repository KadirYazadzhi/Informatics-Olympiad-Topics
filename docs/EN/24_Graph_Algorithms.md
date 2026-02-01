# 🕸️ Graph Algorithms: Traversals and Shortest Paths

Graphs are used to represent objects and the relationships between them. Mastery of graph theory is essential for competitive programming.

---

## 1. Traversals

### 1.1. Depth-First Search (DFS)
Explores as far as possible along each branch before backtracking.
*   **Time Complexity**: $O(V + E)$.
*   **Applications**: Finding cycles, connectivity, topological sort, and bridges.

### 1.2. Breadth-First Search (BFS)
Explores neighbors layer by layer using a `std::queue`.
*   **Time Complexity**: $O(V + E)$.
*   **Application**: Finding the shortest path in an **unweighted** graph.

---

## 2. Shortest Paths

### 2.1. Dijkstra's Algorithm
Finds the shortest path from a single source to all other nodes in a graph with **non-negative** edge weights. Uses a priority queue.
*   **Time Complexity**: $O(E \log V)$.

```cpp
void dijkstra(int s) {
    dist.assign(n, INF);
    dist[s] = 0;
    priority_queue<pair<long long, int>, vector<pair<long long, int>>, greater<pair<long long, int>>> pq;
    pq.push({0, s});
    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;
        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
}
```

### 2.2. Bellman-Ford Algorithm
Handles **negative** edge weights and detects negative cycles.
*   **Time Complexity**: $O(V \cdot E)$.

### 2.3. Floyd-Warshall Algorithm
Finds shortest paths between **all pairs** of vertices.
*   **Time Complexity**: $O(V^3)$.

```cpp
for (int k = 0; k < n; k++)
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
```

---

## 3. Minimum Spanning Tree (MST)

An MST is a subset of edges that connects all vertices without cycles and with minimum total weight.

### 3.1. Kruskal's Algorithm
Sort edges by weight and add them if they don't form a cycle (using DSU).
*   **Complexity**: $O(E \log E)$.

### 3.2. Prim's Algorithm
Grows the tree from a starting vertex by adding the cheapest edge to an unvisited node.
*   **Complexity**: $O(E \log V)$.

---

## 4. Directed Acyclic Graphs (DAG)

### 4.1. Topological Sort
An ordering of vertices such that for every directed edge $u \to v$, node $u$ comes before $v$.
*   Possible only if the graph has no cycles.

---

## 5. Practice Problems
1.  **CSES Shortest Routes I & II**.
2.  **CSES High Score**: Bellman-Ford with negative cycle detection.
3.  **Codeforces 20C**: Dijkstra.
4.  **UVa 10034**: Freckles (MST).

## 6. Conclusion
Most graph problems can be solved by choosing between BFS, Dijkstra, or Floyd-Warshall depending on the edge weights and constraints. For connectivity, DFS and DSU are your best tools.