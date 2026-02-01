# 🌳 Tree Structures: Hierarchy and Algorithms

A tree is an undirected graph that is connected and has no cycles. It is a fundamental data structure for representing hierarchies.

---

## 1. Core Properties

*   **Nodes and Edges**: A tree with $N$ vertices always has exactly $N-1$ edges.
*   **Rooted Tree**: A tree with one designated node as the root.
*   **Leaves**: Nodes with no children.
*   **Ancestor/Descendant**: Node $u$ is an ancestor of $v$ if $u$ is on the unique path from the root to $v$.

---

## 2. Traversals

1.  **Pre-order**: Root, Left, Right.
2.  **In-order**: Left, Root, Right. (For BST, this yields sorted values).
3.  **Post-order**: Left, Right, Root. (Useful for subtree DP).

---

## 3. Lowest Common Ancestor (LCA)

The LCA of two nodes $u$ and $v$ is the node deepest in the tree that is an ancestor of both.

### 3.1. Binary Lifting Method ($O(\log N)$)
Precompute the $2^i$-th ancestor for every node.
*   `up[u][i]` stores the $2^i$-th ancestor of $u$.
*   `up[u][i] = up[up[u][i-1]][i-1]`.

```cpp
int up[MAXN][LOGN];
int tin[MAXN], tout[MAXN], timer;

void dfs(int u, int p) {
    tin[u] = ++timer;
    up[u][0] = p;
    for (int i = 1; i < LOGN; i++)
        up[u][i] = up[up[u][i-1]][i-1];
    for (int v : adj[u])
        if (v != p) dfs(v, u);
    tout[u] = ++timer;
}

bool is_ancestor(int u, int v) {
    return tin[u] <= tin[v] && tout[u] >= tout[v];
}

int get_lca(int u, int v) {
    if (is_ancestor(u, v)) return u;
    if (is_ancestor(v, u)) return v;
    for (int i = LOGN - 1; i >= 0; i--) {
        if (!is_ancestor(up[u][i], v))
            u = up[u][i];
    }
    return up[u][0];
}
```

---

## 4. Tree Diameter

The diameter is the longest path between any two nodes in the tree.
**Two-DFS Algorithm**:
1.  Run DFS from an arbitrary node $A$ to find the farthest node $B$.
2.  Run DFS from $B$ to find the farthest node $C$.
3.  The distance between $B$ and $C$ is the diameter.

---

## 5. Tree DP (Dynamic Programming)
Solving problems where the state at node $u$ depends on its children.
Example: Counting the number of nodes in every subtree.
$size[u] = 1 + \sum_{v \in children(u)} size[v]$.

---

## 6. Practice Problems
1.  **CSES Tree Diameter**: Basic diameter.
2.  **CSES Company Queries II**: LCA.
3.  **Codeforces 191C**: Path counting using LCA and Difference Arrays.
4.  **UVa 112**: Path Sum.

## 7. Conclusion
Trees are often simpler to process than general graphs because the path between any two nodes is unique. Mastering LCA and tree DP is essential for competitive programming.
