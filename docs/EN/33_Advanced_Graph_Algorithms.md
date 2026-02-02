# 🕸️ Advanced Graph Algorithms

These algorithms are based on DFS and are used for analyzing the connectivity and structure of a graph. They represent some of the most elegant and powerful techniques in competitive programming, allowing for the solution of complex problems regarding graph connectivity, cycles, and structural properties.

A key concept in most of these algorithms is the use of the DFS tree and "low-link" values, which allow us to detect critical elements in the graph - such as those whose removal changes the structure or connectivity of the graph.

## 1. Bridges and Articulation Points (Bridges & AP)

In an undirected graph:
*   **Bridge**: An edge whose removal increases the number of connected components. Bridges are critical links in a network - without them, the graph falls apart.
*   **Articulation Point (Cut Vertex)**: A vertex whose removal increases the number of connected components. These are the critical nodes in the network.

### 1.1. Tarjan's Algorithm (DFS Tree)

The algorithm uses two important concepts:
- `tin[u]` - the entry time into vertex $u$ during DFS traversal.
- `low[u]` - the smallest `tin` reachable from $u$ through its subtree and at most one back-edge.

**Basic idea:** An edge $(u, v)$ is a bridge if from the subtree of $v$ we cannot reach an ancestor of $u$ via back-edges.

1.  Start a DFS from an arbitrary vertex (usually the root).
2.  For each child $v$ of $u$:
    *   If $v$ is the parent of $u$, skip (avoid going backwards).
    *   If $v$ is already visited (back-edge): `low[u] = min(low[u], tin[v])`. The back-edge allows us to reach a higher ancestor.
    *   If $v$ is not visited (DFS tree edge):
        *   Recursively call `dfs(v)`.
        *   Update: `low[u] = min(low[u], low[v])`.
        *   **Bridge condition:** If `low[v] > tin[u]`, then $(u, v)$ is a **Bridge** (from $v$ we cannot reach $u$ or above).
        *   **Articulation point condition:** If `low[v] >= tin[u]`, then $u$ is an **Articulation Point**.
        
**Special case for the root:** The root of the DFS tree is an articulation point only if it has at least 2 children in the DFS tree.

### 1.2. Implementation for Finding Bridges

```cpp
vector<int> adj[MAXN];
bool visited[MAXN];
int tin[MAXN], low[MAXN];
int timer = 0;
vector<pair<int, int>> bridges;

void dfs(int u, int parent = -1) {
    visited[u] = true;
    tin[u] = low[u] = timer++;
    
    for (int v : adj[u]) {
        if (v == parent) continue;  // Avoid going backwards
        
        if (visited[v]) {
            // Back-edge - update low
            low[u] = min(low[u], tin[v]);
        } else {
            // DFS tree edge
            dfs(v, u);
            low[u] = min(low[u], low[v]);
            
            // Bridge check
            if (low[v] > tin[u]) {
                bridges.push_back({u, v});
            }
        }
    }
}

void findBridges(int n) {
    timer = 0;
    bridges.clear();
    for (int i = 1; i <= n; i++) {
        if (!visited[i]) {
            dfs(i);
        }
    }
}
```

### 1.3. Implementation for Finding Articulation Points

```cpp
vector<int> adj[MAXN];
bool visited[MAXN];
int tin[MAXN], low[MAXN];
int timer = 0;
set<int> articulation_points;

void dfs(int u, int parent = -1) {
    visited[u] = true;
    tin[u] = low[u] = timer++;
    int children = 0;  // Number of children in the DFS tree
    
    for (int v : adj[u]) {
        if (v == parent) continue;
        
        if (visited[v]) {
            low[u] = min(low[u], tin[v]);
        } else {
            dfs(v, u);
            low[u] = min(low[u], low[v]);
            
            // Articulation point check
            if (low[v] >= tin[u] && parent != -1) {
                articulation_points.insert(u);
            }
            children++;
        }
    }
    
    // Special case: the root is an AP if it has >= 2 children
    if (parent == -1 && children > 1) {
        articulation_points.insert(u);
    }
}

void findArticulationPoints(int n) {
    timer = 0;
    articulation_points.clear();
    for (int i = 1; i <= n; i++) {
        if (!visited[i]) {
            dfs(i);
        }
    }
}
```

### 1.4. Applications

- **Network Analysis:** Detecting critical links in communication networks.
- **Infrastructure Planning:** Identifying critical points during design.
- **Network Optimization:** Determining which links/nodes should be reinforced.

## 2. Strongly Connected Components (SCC)

In a directed graph, an SCC (Strongly Connected Component) is a maximal subset of vertices where every vertex is reachable from every other vertex in that subset. In other words, for every two vertices $u$ and $v$ in an SCC, there exists a path from $u$ to $v$ AND a path from $v$ to $u$.

**Important property:** The condensation of the graph (collapsing each SCC into a single super-vertex) always yields a DAG (Directed Acyclic Graph). This property is extremely useful for solving many tasks.

### 2.1. Kosaraju's Algorithm

This algorithm is more intuitive and uses two DFS traversals:

```cpp
vector<int> adj[MAXN], adj_rev[MAXN];  // Graph and reversed graph
bool visited[MAXN];
vector<int> order;  // Finishing order
int comp[MAXN];     // Component number for each vertex

void dfs1(int u) {
    visited[u] = true;
    for (int v : adj[u]) {
        if (!visited[v]) dfs1(v);
    }
    order.push_back(u);  // Add upon completion
}

void dfs2(int u, int component) {
    comp[u] = component;
    for (int v : adj_rev[u]) {
        if (comp[v] == -1) dfs2(v, component);
    }
}

void findSCC_Kosaraju(int n) {
    // Step 1: First DFS to find the finishing order
    for (int i = 1; i <= n; i++) {
        if (!visited[i]) dfs1(i);
    }
    
    // Step 2: DFS on the reversed graph in reverse order
    fill(comp, comp + n + 1, -1);
    int num_components = 0;
    reverse(order.begin(), order.end());
    
    for (int u : order) {
        if (comp[u] == -1) {
            dfs2(u, num_components++);
        }
    }
}
```

### 2.2. Tarjan's Algorithm for SCC

Uses one DFS traversal and a stack. More efficient than Kosaraju's:

```cpp
vector<int> adj[MAXN];
int tin[MAXN], low[MAXN], timer = 0;
bool on_stack[MAXN];
stack<int> st;
int comp[MAXN], num_components = 0;

void dfs(int u) {
    tin[u] = low[u] = timer++;
    st.push(u);
    on_stack[u] = true;
    
    for (int v : adj[u]) {
        if (tin[v] == -1) {
            // v is not visited
            dfs(v);
            low[u] = min(low[u], low[v]);
        } else if (on_stack[v]) {
            // v is in the current SCC
            low[u] = min(low[u], tin[v]);
        }
    }
    
    // If u is the root of the SCC
    if (low[u] == tin[u]) {
        while (true) {
            int v = st.top();
            st.pop();
            on_stack[v] = false;
            comp[v] = num_components;
            if (v == u) break;
        }
        num_components++;
    }
}

void findSCC_Tarjan(int n) {
    fill(tin, tin + n + 1, -1);
    fill(on_stack, on_stack + n + 1, false);
    num_components = 0;
    
    for (int i = 1; i <= n; i++) {
        if (tin[i] == -1) {
            dfs(i);
        }
    }
}
```

### 2.3. Applications of SCC

1.  **Finding cycles in directed graphs:** If an SCC contains > 1 vertex, there is a cycle.
2.  **Building a condensation graph:** Useful for DP on a DAG.
3.  **Strong connectivity check:** A graph is strongly connected $\iff$ there is exactly one SCC.
4.  **2-SAT:** (see below)

## 3. 2-SAT (2-Satisfiability)

We have a set of boolean variables $x_1, \ldots, x_n$ and conditions of the type $(a \lor b)$, where $a$ and $b$ are literals ($x_i$ or $\neg x_i$). We want to find values (True/False) satisfying all conditions, or determine that none exist.

**The problem:** This is an NP-complete problem for general SAT, but when every condition has exactly 2 literals (2-SAT), it can be solved in polynomial time!

### 3.1. Reduction to SCC

The condition $(a \lor b)$ is equivalent to two implications:
- $(\neg a \Rightarrow b)$ - "if $a$ is False, then $b$ must be True"
- $(\neg b \Rightarrow a)$ - "if $b$ is False, then $a$ must be True"

**Algorithm:**
1.  We build an implication graph with $2n$ vertices - one for $x_i$ and $\neg x_i$ for each variable.
2.  For each condition $(a \lor b)$, we add edges $\neg a \to b$ and $\neg b \to a$.
3.  We find the SCCs of the graph.
4.  **Checking for a solution:** If any variable $x_i$ and $\neg x_i$ are in the same SCC $\implies$ **No solution** (contradiction).
5.  **Finding a solution:** In the topological order of the condensation graph, if $SCC(x_i)$ is after $SCC(\neg x_i)$, we choose $x_i = True$, else $x_i = False$.

### 3.2. Implementation

```cpp
vector<int> adj[2 * MAXN];  // 2n vertices: i and i+n for x_i and neg(x_i)
int comp[2 * MAXN];
int n;  // Number of variables

// Helper function: returns the index of the literal
// i -> x_i (i >= 1), -i -> neg(x_i)
int getNode(int literal) {
    if (literal > 0) return literal;
    return n + (-literal);
}

// Adding a clause (a OR b)
void addClause(int a, int b) {
    // (a OR b) == (NOT a => b) AND (NOT b => a)
    adj[getNode(-a)].push_back(getNode(b));
    adj[getNode(-b)].push_back(getNode(a));
}

bool solve2SAT() {
    // Find SCCs (with Tarjan or Kosaraju)
    findSCC_Tarjan(2 * n);
    
    // Check for contradictions
    for (int i = 1; i <= n; i++) {
        if (comp[i] == comp[n + i]) {
            return false;  // x_i and neg(x_i) in the same SCC - no solution
        }
    }
    
    // A solution exists - we can construct it
    return true;
}

// Constructing a specific solution
vector<bool> getAssignment() {
    vector<bool> result(n + 1);
    for (int i = 1; i <= n; i++) {
        // If the SCC of x_i is later in the topological order (smaller number)
        result[i] = (comp[i] > comp[n + i]);
    }
    return result;
}
```

### 3.3. Example: Giant Pizza Task

We have $n$ customers and $m$ toppings. Each customer wants or does not want exactly 2 toppings. We must decide if we can make a pizza that satisfies everyone:

```cpp
void solvePizza(int n, int m, vector<array<int, 4>>& customers) {
    // customers[i] = {topping1, want1, topping2, want2}
    // want = 1 if desired, -1 if not
    
    for (auto [t1, w1, t2, w2] : customers) {
        int a = (w1 == 1) ? t1 : -t1;  // Literal for first topping
        int b = (w2 == 1) ? t2 : -t2;  // Literal for second topping
        addClause(a, b);  // Add (a OR b)
    }
    
    if (solve2SAT()) {
        vector<bool> assignment = getAssignment();
        for (int i = 1; i <= m; i++) {
            cout << (assignment[i] ? '+' : '-') << " ";
        }
    } else {
        cout << "IMPOSSIBLE\n";
    }
}
```

### 3.4. Practical Tips for 2-SAT

- Be careful with indexing: $x_i$ and $\neg x_i$ are often confused.
- Check whether the graph is correctly built - debug with small examples.
- When constructing the solution, the topological order of the SCCs is important.
- 2-SAT is common in tasks with binary choices and constraints.

## 4. Additional Applications and Techniques

### 4.1. Bi-connected Components

Two vertices are in the same bi-connected component if there are at least two vertex-independent paths between them. We can find them using articulation points.

### 4.2. Edge-based Connectivity

Sometimes we want to consider the graph from the point of view of the edges - for example, to find the minimum number of edges that must be removed to make the graph disconnected.

### 4.3. Condensation Graph

After finding SCCs, we can build a condensation graph where each SCC is collapsed into a single vertex. This graph is always a DAG and we can apply DAG techniques (topological sorting, DP, etc.).

```cpp
vector<int> condensation[MAXN];

void buildCondensation(int n, int num_scc) {
    set<pair<int,int>> edges_added;  // Avoid duplicate edges
    
    for (int u = 1; u <= n; u++) {
        for (int v : adj[u]) {
            if (comp[u] != comp[v]) {
                if (edges_added.find({comp[u], comp[v]}) == edges_added.end()) {
                    condensation[comp[u]].push_back(comp[v]);
                    edges_added.insert({comp[u], comp[v]});
                }
            }
        }
    }
}
```

## 5. Practice Tasks

### Basic tasks
1.  **SPOJ EC_P**: Critical Edges (Bridges) - basic bridge task.
2.  **SPOJ SUBMERGE**: Submerging Islands (Articulation Points) - classical AP task.
3.  **CSES Planets and Kingdoms**: Finding SCCs - excellent for starters.

### Intermediate tasks
4.  **CSES Giant Pizza**: 2-SAT task - very well formulated.
5.  **Codeforces 427C**: Checkposts (SCC) - combination of SCC and combinatorics.
6.  **CSES Flight Routes Check**: Check whether the graph is strongly connected.

### Advanced tasks
7.  **Codeforces 652D**: Nested Segments - non-standard SCC application.
8.  **SPOJ TOUR**: Finding an Eulerian cycle with SCC.
9.  **Codeforces 292E**: Copying Data - segment trees and SCC.

## 6. Typical Errors and Debug Tips

### 6.1. Common Errors

1. **Forgetting the parent != -1 check** for articulation points.
2. **Incorrect initialization** of low and tin values.
3. **Confusing indices** in 2-SAT ($x_i$ vs $\neg x_i$).
4. **Forgetting multi-edges** when finding bridges.
5. **Incorrect traversal order** in Kosaraju's.

### 6.2. Debug Tips

- Test with small examples (3-5 vertices).
- Visualize the graph and the DFS tree.
- Print tin and low values for every vertex.
- Check whether you are correctly handling special cases (DFS root).
- For 2-SAT, check whether the implication graph is correctly built.

## 🏁 Conclusion

Advanced graph algorithms are powerful tools for analyzing the structure and connectivity of graphs. The key to understanding them is the concept of `low-link` values and the DFS tree.

**Main takeaways:**
- **Bridges and AP:** Critical elements in the network - learn to find them in $O(V + E)$.
- **SCC:** Collapsing cycles into a DAG - allows for DP and topological sorting.
- **2-SAT:** Elegant reduction of a boolean problem to a graph one - uses SCC.

These algorithms are often combined with other techniques (DP, greedy algorithms, binary search) to solve complex competitive tasks. Practice them until they become intuitive - they are among the most beautiful and useful algorithms in graph theory!