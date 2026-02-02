# 🔗 Disjoint Set Union (DSU) / Union-Find

The Disjoint Set Union (also known as Union-Find) is a data structure that maintains a partition of a set of elements into several non-overlapping (disjoint) subsets. This structure is essential for efficiently solving numerous problems related to graphs, networks, and dynamic connectivity.

DSU is particularly useful when we need to maintain a collection of elements divided into non-intersecting groups and quickly answer queries of the type "Do two elements belong to the same group?" or "Union the groups of two elements."

---

## 1. Basic Operations

DSU supports two main operations:

1.  **Find(i)**: Determines which subset the element $i$ is in. Usually returns a "representative" (root) of that subset. If `find(i) == find(j)`, then $i$ and $j$ are in the same set. This operation is the basis for checking set membership.

2.  **Union(i, j)**: Unites the subsets in which $i$ and $j$ are located. After this operation, all elements from both sets will have a common representative.

Additional operations that are frequently used:
- **MakeSet(i)**: Creates a new set with a single element $i$.
- **GetSize(i)**: Returns the size of the set containing element $i$.
- **CountSets()**: Returns the total number of distinct sets.

---

## 2. Implementation and Optimizations

A naive implementation can lead to chains of length $O(N)$. To achieve almost constant time, we use two important optimizations. Without these optimizations, in the worst case, we can obtain a deep tree, which makes the `find` operation linear in time.

### 2.1. Path Compression

During every call to `find(i)`, we make all nodes along the path to the root direct children of the root. This technique is extremely effective - when we search for the root of a given element, all elements along the path are "compressed" and start pointing directly to the root.

**How it works:** When we perform `find(i)` and pass through a chain of parents `i → p1 → p2 → ... → root`, after the operation finishes, all these intermediate nodes `p1, p2, ...` now point directly to `root`.

This optimization makes subsequent queries for the same elements much faster, since the path to the root becomes practically direct.

### 2.2. Union by Rank/Size

We always attach the "shallower" tree to the root of the "deeper" one to keep the height minimal. There are two variants:

- **Union by Size**: We attach the smaller set to the larger one.
- **Union by Rank**: We attach the tree with the smaller height to the tree with the larger height.

Both yield excellent results; in practice, union by size is more popular because the size of the sets is often useful information in itself.

```cpp
struct DSU {
    vector<int> parent;  // parent[i] stores the parent of element i
    vector<int> sz;      // sz[i] stores the size of the set with root i

    // Constructor - initialization
    DSU(int n) {
        parent.resize(n + 1);
        sz.resize(n + 1, 1);
        // Each element is in its own set
        for (int i = 1; i <= n; i++) parent[i] = i;
    }

    // Find with path compression
    int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]); // Path compression
    }

    // Union by size
    void unite(int i, int j) {
        int root_i = find(i);
        int root_j = find(j);
        if (root_i != root_j) {
            // Always attach the smaller to the larger
            if (sz[root_i] < sz[root_j]) swap(root_i, root_j);
            parent[root_j] = root_i;
            sz[root_i] += sz[root_j];
        }
    }

    // Check if two elements are in the same set
    bool same(int i, int j) {
        return find(i) == find(j);
    }

    // Get the size of the set
    int getSize(int i) {
        return sz[find(i)];
    }
};
```

**Complexity**: With both optimizations, the operations are almost $O(1)$ (technically $O(\alpha(N))$, where $\alpha$ is the inverse Ackermann function). In practice, $\alpha(N) \leq 4$ for all realistic values of $N$, which makes the operations effectively constant.

### 2.3. Variant with Rank

If you prefer union by rank instead of union by size:

```cpp
struct DSU_Rank {
    vector<int> parent;
    vector<int> rank;  // rank[i] is the approximate height of the tree

    DSU_Rank(int n) {
        parent.resize(n + 1);
        rank.resize(n + 1, 0);
        for (int i = 1; i <= n; i++) parent[i] = i;
    }

    int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    void unite(int i, int j) {
        int root_i = find(i);
        int root_j = find(j);
        if (root_i != root_j) {
            if (rank[root_i] < rank[root_j]) {
                parent[root_i] = root_j;
            } else if (rank[root_i] > rank[root_j]) {
                parent[root_j] = root_i;
            } else {
                parent[root_j] = root_i;
                rank[root_i]++;  // Increase rank only upon tie
            }
        }
    }
};
```

---

## 3. Applications

DSU is an incredibly versatile structure with numerous applications in various fields.

### 3.1. Kruskal's Algorithm (Minimum Spanning Tree)

Kruskal's algorithm finds a minimum spanning tree in a weighted graph. DSU is a central part of this algorithm:

```cpp
struct Edge {
    int u, v, weight;
    bool operator<(const Edge& other) const {
        return weight < other.weight;
    }
};

long long kruskal(int n, vector<Edge>& edges) {
    sort(edges.begin(), edges.end()); // Sort by weight
    DSU dsu(n);
    long long total_weight = 0;
    int edges_added = 0;

    for (const Edge& e : edges) {
        // Add edge only if it does not create a cycle
        if (!dsu.same(e.u, e.v)) {
            dsu.unite(e.u, e.v);
            total_weight += e.weight;
            edges_added++;
            if (edges_added == n - 1) break; // MST has n-1 edges
        }
    }
    return total_weight;
}
```

**Explanation:** We check whether the two vertices of the edge are in different components. If they are, we add the edge because it does not create a cycle.

### 3.2. Connected Components

The number of connected components is equal to the number of elements $i$ for which `parent[i] == i`. These are the roots of the different sets.

```cpp
int countComponents(DSU& dsu, int n) {
    int count = 0;
    for (int i = 1; i <= n; i++) {
        if (dsu.find(i) == i) {
            count++;
        }
    }
    return count;
}
```

Alternatively, we can maintain the number of components directly in the structure:

```cpp
struct DSU_WithCount {
    vector<int> parent, sz;
    int components;

    DSU_WithCount(int n) {
        parent.resize(n + 1);
        sz.resize(n + 1, 1);
        components = n;
        for (int i = 1; i <= n; i++) parent[i] = i;
    }

    int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    void unite(int i, int j) {
        int root_i = find(i);
        int root_j = find(j);
        if (root_i != root_j) {
            if (sz[root_i] < sz[root_j]) swap(root_i, root_j);
            parent[root_j] = root_i;
            sz[root_i] += sz[root_j];
            components--;  // Decreases by 1 upon each union
        }
    }

    int getComponents() {
        return components;
    }
};
```

### 3.3. Dynamic Connectivity

We can add edges and check for a path between two vertices in real time. For example, in online queries:

```cpp
void processQueries(int n, vector<pair<int,int>>& queries) {
    DSU dsu(n);
    
    for (auto [type, a, b] : queries) {
        if (type == 1) {
            // Add edge between a and b
            dsu.unite(a, b);
        } else {
            // Check if a and b are connected
            if (dsu.same(a, b)) {
                cout << "YES\n";
            } else {
                cout << "NO\n";
            }
        }
    }
}
```

### 3.4. Cycle Detection

In an undirected graph, we can detect cycles:

```cpp
bool hasCycle(int n, vector<pair<int,int>>& edges) {
    DSU dsu(n);
    for (auto [u, v] : edges) {
        if (dsu.same(u, v)) {
            return true;  // Cycle found
        }
        dsu.unite(u, v);
    }
    return false;
}
```

### 3.5. Network Connectivity Tasks

It is often used for tasks of the type "is the network connected after adding/removing links", "finding critical links", etc.

## 4. Advanced Techniques and Variants

### 4.1. DSU with Rollback (Undo Operations)

In some tasks, it may be necessary to "undo" union operations. This requires a slightly different implementation without path compression:

```cpp
struct DSU_Rollback {
    vector<int> parent, rank;
    stack<pair<int, int>> history;  // History of changes

    DSU_Rollback(int n) {
        parent.resize(n + 1);
        rank.resize(n + 1, 0);
        for (int i = 1; i <= n; i++) parent[i] = i;
    }

    int find(int i) {
        // Without path compression to allow rollback
        while (parent[i] != i) i = parent[i];
        return i;
    }

    void unite(int i, int j) {
        int root_i = find(i);
        int root_j = find(j);
        if (root_i == root_j) return;
        
        if (rank[root_i] < rank[root_j]) swap(root_i, root_j);
        history.push({root_j, parent[root_j]});
        parent[root_j] = root_i;
        
        if (rank[root_i] == rank[root_j]) {
            history.push({root_i, rank[root_i]});
            rank[root_i]++;
        }
    }

    void rollback() {
        // Undoing the last operation
        if (history.empty()) return;
        auto [node, value] = history.top();
        history.pop();
        // Return logic...
    }
};
```

### 4.2. Persistent DSU

Saves previous versions of the structure, allowing queries for different moments in time.

### 4.3. DSU with Additional Information

We can store additional information for each set:

```cpp
struct DSU_Extended {
    vector<int> parent, sz;
    vector<long long> sum;  // Sum of elements in the set

    DSU_Extended(int n, vector<int>& values) {
        parent.resize(n + 1);
        sz.resize(n + 1, 1);
        sum.resize(n + 1);
        for (int i = 1; i <= n; i++) {
            parent[i] = i;
            sum[i] = values[i];
        }
    }

    int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]);
    }

    void unite(int i, int j) {
        int root_i = find(i);
        int root_j = find(j);
        if (root_i != root_j) {
            if (sz[root_i] < sz[root_j]) swap(root_i, root_j);
            parent[root_j] = root_i;
            sz[root_i] += sz[root_j];
            sum[root_i] += sum[root_j];  // Combine sums
        }
    }

    long long getSum(int i) {
        return sum[find(i)];
    }
};
```

## 5. Typical Errors and Tips

### 5.1. Common Errors

1. **Forgetting path compression**: Leads to slow execution.
2. **Incorrect union**: Uniting elements instead of their roots.
3. **Indexing**: Forgetting whether we work with 0-based or 1-based indices.
4. **Recursion without base case**: Forgetting the `if (parent[i] == i)` check.

### 5.2. Practical Tips

- Always use both optimizations (path compression + union by size/rank).
- For easier debugging, add a `print()` method that shows the current state.
- In tasks with large numbers, be careful not to overflow data types.
- Test with small examples to verify correctness.

## 6. Practice Tasks

### Basic tasks
1.  **CSES Road Construction**: Maintaining the number of components and the size of the largest one. A very good task to start with.
2.  **SPOJ MST**: Classical MST with Kruskal's algorithm.
3.  **Codeforces 25D**: Roads not only in Berland - an interesting task for graph restructuring.

### Intermediate tasks
4.  **Codeforces 277A**: Learning Languages - a connected components task.
5.  **CSES Road Reparation**: MST with connectivity check.
6.  **AtCoder ABC 120D**: Bridges - a dynamic connectivity task.

### Advanced tasks
7.  **Codeforces 400D**: Dima and Bacteria - DSU with additional constraints.
8.  **SPOJ KOZE**: Sheep - 2D DSU for connectivity in a matrix.
9.  **Codeforces 763C**: Timofey and Rectangles - a non-standard application of DSU.

## 🏁 Conclusion

DSU is one of the most important structures in competitive programming. It is:
- **Extremely fast**: Almost constant operations thanks to optimizations.
- **Easy to write**: The implementation is short and clear.
- **Versatile**: Applied in a vast number of different tasks.

DSU is often the "hidden" solution to tasks that at first glance do not appear related to graphs. When you see a task for grouping elements or maintaining equivalence between objects, think of DSU!

Learn this structure well - it will save you a lot of time and help you efficiently solve complex problems of connectivity and union of sets.
