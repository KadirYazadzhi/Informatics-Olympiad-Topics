# 🔗 Disjoint Set Union (DSU) / Union-Find

DSU is a data structure that tracks a set of elements partitioned into a number of disjoint (non-overlapping) subsets. It provides near-constant time operations to add new sets, merge existing sets, and determine whether elements are in the same set.

---

## 1. Core Operations

*   **Find**: Determine which subset a particular element is in. This is used for checking if two elements belong to the same subset.
*   **Union**: Join two subsets into a single subset.

---

## 2. Optimizations

Without optimization, DSU can degrade to $O(N)$ for both operations. We use two techniques to achieve $O(\alpha(N))$ (Ackermann Inverse):

### 2.1. Path Compression
During a `find` operation, make every visited node point directly to the root. This flattens the tree.

### 2.2. Union by Rank/Size
Always attach the smaller tree under the root of the larger tree. This keeps the tree height logarithmic.

```cpp
struct DSU {
    vector<int> parent;
    vector<int> sz; // Size of each component

    DSU(int n) {
        parent.resize(n + 1);
        sz.assign(n + 1, 1);
        for (int i = 0; i <= n; i++) parent[i] = i;
    }

    int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]); // Path compression
    }

    bool unite(int i, int j) {
        int root_i = find(i);
        int root_j = find(j);
        if (root_i != root_j) {
            // Union by size
            if (sz[root_i] < sz[root_j]) swap(root_i, root_j);
            parent[root_j] = root_i;
            sz[root_i] += sz[root_j];
            return true; // Successfully merged
        }
        return false; // Already in same set
    }
};
```

---

## 3. Applications

### 3.1. Kruskal's Algorithm
Used to find the Minimum Spanning Tree (MST) by efficiently checking if an edge connects two previously disconnected components.

### 3.2. Cycle Detection
In an undirected graph, if `find(u) == find(v)` before adding edge $(u, v)$, then adding the edge will create a cycle.

### 3.3. Dynamic Connectivity
DSU allows adding edges and checking connectivity online. (Note: standard DSU does not support edge deletion efficiently).

---

## 4. Advanced: DSU with Rollback
If you need to undo operations (e.g., in a Divide and Conquer on queries), you **cannot** use Path Compression. Use only Union by Rank/Size and maintain a stack of changes to revert.

---

## 5. Practice Problems
1.  **CSES Road Construction**: Standard DSU.
2.  **UVa 10608**: Friends (Max component size).
3.  **Codeforces 1213G**: Path Queries (Sorting edges + DSU).

## 6. Conclusion
DSU is a powerful tool for connectivity problems. Its implementation is short, but its efficiency makes it suitable for huge datasets ($N = 10^6$).