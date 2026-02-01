# 🕸️ Advanced Graph Algorithms: Connectivity and Structure

DFS (Depth-First Search) is more than just a traversal tool. It is the core of many algorithms that analyze the structural integrity and connectivity of graphs.

---

## 1. Undirected Graphs: Bridges and Articulation Points

In an undirected graph, identifying "weak links" is crucial.

*   **Bridge**: An edge whose removal increases the number of connected components.
*   **Articulation Point (Cut Vertex)**: A vertex whose removal increases the number of connected components.

### 1.1. Tarjan's Low-Link Algorithm
We maintain two values for each node:
1.  `tin[u]`: Entry time (discovery time).
2.  `low[u]`: Lowest `tin` reachable from $u$ in the DFS tree using at most one back-edge.

```cpp
void dfs(int u, int p = -1) {
    tin[u] = low[u] = timer++;
    for (int v : adj[u]) {
        if (v == p) continue;
        if (vis[v]) {
            low[u] = min(low[u], tin[v]); // Back-edge
        } else {
            dfs(v, u);
            low[u] = min(low[u], low[v]);
            if (low[v] > tin[u]) 
                is_bridge(u, v);
            if (low[v] >= tin[u] && p != -1)
                is_articulation(u);
        }
    }
}
```

---

## 2. Directed Graphs: Strongly Connected Components (SCC)

An SCC is a maximal subset of vertices where every vertex is reachable from every other vertex in the subset.

### 2.1. Tarjan's SCC Algorithm
Uses a stack to track nodes currently in the DFS path.
1.  If `low[u] == tin[u]`, $u$ is the "root" of an SCC.
2.  Pop all nodes from the stack until $u$.

### 2.2. Kosaraju's Algorithm
1.  Perform DFS on original graph to get finishing times (order nodes by exit).
2.  Reverse the graph ($G^R$).
3.  Perform DFS on $G^R$ in decreasing order of finishing times. Each traversal finds one SCC.

---

## 3. 2-Satisfiability (2-SAT)

Given a set of boolean variables and constraints of the form $(a \lor b)$, find a valid truth assignment.

**Reduction to SCC**:
The clause $(a \lor b)$ is equivalent to $(\neg a \implies b)$ and $(\neg b \implies a)$.
1.  Create an implication graph where each variable $x_i$ has two nodes: $x_i$ and $\neg x_i$.
2.  For each clause, add the two directed edges.
3.  Find SCCs.
4.  If $x_i$ and $\neg x_i$ are in the same SCC $\implies$ **Impossible**.
5.  Otherwise, assignment is based on topological order of SCCs.

---

## 4. Euler Paths and Circuits

*   **Euler Circuit**: Visits every edge exactly once and returns to start.
    *   Undirected: All degrees even.
    *   Directed: $InDegree(v) = OutDegree(v)$ for all $v$.
*   **Euler Path**: Visits every edge once but can end at a different node.
    *   Undirected: 0 or 2 vertices with odd degree.

### Hierholzer's Algorithm ($O(E)$)
Use DFS but only add the node to the result path AFTER visiting all its incident edges. Reverse the result.

---

## 5. Practice Problems
1.  **CSES Giant Pizza**: 2-SAT implementation.
2.  **CSES Flight Routes Check**: Check if directed graph is strongly connected.
3.  **Codeforces 118E**: Bertown Roads (Bridges and DFS orientation).
4.  **UVa 10731**: SCC problem.

## 6. Conclusion
Low-link values (`tin` and `low`) are the most powerful technique for connectivity. Master Tarjan's logic, as it applies to both undirected (Bridges) and directed (SCC) cases with minor variations.
