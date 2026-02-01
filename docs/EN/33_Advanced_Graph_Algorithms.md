# 🕸️ Advanced Graph Algorithms

Analyzing graph connectivity and structure using DFS.

## 1. Bridges and Articulation Points

In an undirected graph:
*   **Bridge**: An edge whose removal increases the number of connected components.
*   **Articulation Point (Cut Vertex)**: A vertex whose removal increases components.

### Tarjan's Bridge-Finding Algorithm
Uses `tin[u]` (entry time) and `low[u]` (lowest `tin` reachable via back-edge).

1.  DFS traversal.
2.  For child $v$ of $u$:
    *   If $v$ visited (back-edge): `low[u] = min(low[u], tin[v])`.
    *   If $v$ not visited:
        *   `dfs(v)`
        *   `low[u] = min(low[u], low[v])`
        *   **If `low[v] > tin[u]`**: $(u, v)$ is a **Bridge**.
        *   **If `low[v] >= tin[u]`**: $u$ is an **Articulation Point** (Root needs >1 children).

## 2. Strongly Connected Components (SCC)

In a directed graph, a subset of vertices where every vertex is reachable from every other vertex.
Contracting SCCs results in a DAG (Condensation Graph).

### Tarjan's SCC Algorithm
1.  Maintain `tin`, `low`, and a stack.
2.  Push nodes to stack during DFS.
3.  If `low[u] == tin[u]` after visiting children:
    *   Pop from stack until $u$. These nodes form an SCC.

## 3. 2-SAT (2-Satisfiability)

Given boolean variables and clauses $(a \lor b)$. Find a truth assignment.
Equivalent to $(\neg a \implies b) \land (\neg b \implies a)$.

**Algorithm:**
1.  Build Implication Graph with nodes $x_i, \neg x_i$.
2.  Find SCCs.
3.  If $x_i$ and $\neg x_i$ in same SCC $\implies$ Impossible.
4.  Otherwise, set $x_i = true$ if $SCC(x_i)$ appears after $SCC(\neg x_i)$ in topological order (reverse topological of condensation).

## 4. Euler Tour
A path that visits every edge exactly once.
*   **Euler Path**: Exists if 0 or 2 vertices have odd degree.
*   **Euler Circuit**: Exists if all vertices have even degree.
*   Algorithm: Hierholzer's or simple DFS (adding to path after visiting edges).

## 5. Practice
1.  **SPOJ EC_P**: Critical Edges.
2.  **CSES Giant Pizza**: 2-SAT.
3.  **Codeforces 118E**: Bertown roads (Bridges).

## 6. Conclusion
Mastering `low-link` values is essential for connectivity problems.