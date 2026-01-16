# 🔗 Union-Find (DSU): Advanced Techniques

## 🔙 DSU with Rollback

Sometimes we need to add edges, answer queries, and then **remove** the last added edges (e.g., in Divide and Conquer on time).
- Standard Path Compression does not allow easy rollback.
- Therefore, we use **only Union by Rank/Size** (no Path Compression). Tree depth remains $O(\log N)$.
- Keep a stack of changes: "Vertex $u$ changed its parent from $u$ to $v$".
- `rollback()`: Reverts the last change from the stack.

```cpp
struct DSU_Rollback {
    vector<int> parent, sz;
    vector<pair<int, int>> history; // {child, original_parent}

    // ... init ...

    int find(int i) { // No path compression!
        if (parent[i] == i) return i;
        return find(parent[i]); 
    }

    void unite(int i, int j) {
        int root_i = find(i), root_j = find(j);
        if (root_i != root_j) {
            if (sz[root_i] < sz[root_j]) swap(root_i, root_j);
            parent[root_j] = root_i;
            sz[root_i] += sz[root_j];
            history.push_back({root_j, root_j}); // Record change
        } else {
            history.push_back({-1, -1}); // Nothing changed
        }
    }

    void rollback() {
        auto last = history.back(); history.pop_back();
        if (last.first != -1) {
            parent[last.first] = last.second;
            sz[last.second] -= sz[last.first];
        }
    }
};
```

---

## 🌲 DSU on Tree (Small-to-Large Merging)

Technique for solving problems like "For every subtree, count distinct colors".
- Idea: Maintain a `set` of colors for each node.
- When merging child's `set` with parent's, always move elements from **smaller** to **larger** set.
- Complexity: Each element is moved at most $O(\log N)$ times. Total $O(N \log^2 N)$.

---

## ⚖️ Weighted DSU

Besides parent-child link, keep extra info about the edge.
- Example: Potential difference $val[u] - val[parent[u]]$.
- During `find` with Path Compression, update this value too.
- Application: Checking consistency of a system of equations/inequalities ($A - B = 5$, $B - C = 3 \implies A - C = 8$).

---

## 🏁 Conclusion

DSU is much more than just finding connected components. The Rollback version is key to solving dynamic problems, and Small-to-Large merging is a standard technique for tree problems. 🚀
