# 🕸️ Graph Algorithms: Foundations and Optimizations

Graphs are a fundamental data structure for modeling relationships between objects (cities, computers, people, social networks). Understanding graph algorithms is critical for success in informatics Olympiads. In this material, we consider the most important algorithms for traversal, finding the shortest path, minimum spanning trees, and topological sorting.

**Basic graph representations:**
- **Adjacency List** (`vector<vector<int>>` or `vector<vector<pair<int,int>>>`): Efficient for sparse graphs.
- **Adjacency Matrix** (`bool adj[MAXN][MAXN]`): Fast check for edge existence, but requires $O(V^2)$ memory.

---

## 1. Graph Traversal

### 1.1. DFS (Depth-First Search)
DFS is a graph traversal algorithm that explores as deep as possible along a given branch before backtracking. It uses a stack (implicitly via recursion or explicitly via `std::stack`).

**How it works:**
1. We start from a given vertex and mark it as visited.
2. We recursively visit all unvisited neighbors.
3. We backtrack when there are no more unvisited neighbors.

**Full implementation:**
```cpp
vector<int> adj[MAXN];  // Adjacency list
bool visited[MAXN];     // Marking visited vertices
vector<int> component;  // Current component

void dfs(int u) {
    visited[u] = true;
    component.push_back(u);  // Add to the current component
    
    for (int v : adj[u]) {
        if (!visited[v]) {
            dfs(v);
        }
    }
}

// Finding all connected components
void findComponents() {
    memset(visited, false, sizeof(visited));
    int numComponents = 0;
    
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            component.clear();
            dfs(i);
            numComponents++;
            // component contains all vertices in the current component
        }
    }
}
```

**Complexity:** $O(V + E)$ - each vertex and edge is processed exactly once.

**Applications:**
- Finding connected components in an undirected graph.
- Cycle detection (if we encounter a visited vertex other than the parent).
- Topological sorting in a DAG.
- Finding bridges and articulation points.
- Solving mazes.

### 1.2. BFS (Breadth-First Search)
BFS traverses the graph "layer by layer" using a queue (`std::queue`). It first visits all neighbors of the current vertex, then their neighbors, and so on.

**How it works:**
1. We start from a given vertex, place it in the queue, and mark it.
2. While the queue is not empty:
   - We extract a vertex from the queue.
   - For each unvisited neighbor: we mark it and add it to the queue.

**Full implementation:**
```cpp
vector<int> adj[MAXN];
bool visited[MAXN];
int dist[MAXN];  // Distance from the starting vertex

void bfs(int start) {
    memset(visited, false, sizeof(visited));
    memset(dist, -1, sizeof(dist));
    
    queue<int> q;
    q.push(start);
    visited[start] = true;
    dist[start] = 0;
    
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        
        for (int v : adj[u]) {
            if (!visited[v]) {
                visited[v] = true;
                dist[v] = dist[u] + 1;
                q.push(v);
            }
        }
    }
}

// BFS with path reconstruction
vector<int> reconstructPath(int start, int end) {
    vector<int> parent(n, -1);
    queue<int> q;
    q.push(start);
    parent[start] = start;
    
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        
        if (u == end) break;  // Goal found
        
        for (int v : adj[u]) {
            if (parent[v] == -1) {
                parent[v] = u;
                q.push(v);
            }
        }
    }
    
    // Reconstructing the path
    if (parent[end] == -1) return {};  // No path
    
    vector<int> path;
    for (int v = end; v != start; v = parent[v]) {
        path.push_back(v);
    }
    path.push_back(start);
    reverse(path.begin(), path.end());
    return path;
}
```

**Complexity:** $O(V + E)$.

**Applications:**
- **Shortest path in an unweighted graph** (BFS guarantees the minimum number of edges).
- Checking whether a graph is bipartite (two-color coloring).
- Finding all vertices at distance $k$ from a given vertex.
- 0-1 BFS for graphs with weights 0 and 1.

---

## 2. Shortest Path

### 2.1. Dijkstra's Algorithm
Dijkstra's algorithm finds the shortest path from a starting vertex to all other vertices in a graph with **non-negative** weights. It uses a priority queue to always process the vertex with the smallest distance.

**Idea:** Gradually "relax" the distances, always choosing the nearest unprocessed vertex.

**Full implementation with path reconstruction:**
```cpp
const long long INF = 1e18;
vector<pair<int, int>> adj[MAXN];  // {neighbor, weight}
long long dist[MAXN];
int parent[MAXN];

void dijkstra(int start) {
    fill(dist, dist + n, INF);
    fill(parent, parent + n, -1);
    dist[start] = 0;
    
    // Min-heap: {distance, vertex}
    priority_queue<pair<long long, int>, 
                   vector<pair<long long, int>>, 
                   greater<pair<long long, int>>> pq;
    pq.push({0, start});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); 
        pq.pop();
        
        // Skip outdated entries
        if (d > dist[u]) continue;
        
        // Relaxation of neighbors
        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                parent[v] = u;
                pq.push({dist[v], v});
            }
        }
    }
}

// Reconstructing the shortest path
vector<int> getPath(int start, int end) {
    if (dist[end] == INF) return {};  // No path
    
    vector<int> path;
    for (int v = end; v != -1; v = parent[v]) {
        path.push_back(v);
    }
    reverse(path.begin(), path.end());
    return path;
}
```

**Complexity:** $O((V + E) \log V)$ with a binary heap, $O(V^2)$ with an array.

**Important:** Dijkstra **does not work** correctly with negative weights!

**Example task:** Finding the shortest path from city A to city B in a network of cities.

### 2.2. Bellman-Ford Algorithm
Bellman-Ford works with **negative** weights and can detect negative cycles. The algorithm relaxes all edges $V-1$ times.

**Idea:** If there is a path with at most $k$ edges, then after $k$ iterations the distance will be correct.

**Full implementation:**
```cpp
struct Edge {
    int from, to;
    long long weight;
};

vector<Edge> edges;
long long dist[MAXN];
int parent[MAXN];

bool bellmanFord(int start, int n) {
    fill(dist, dist + n, INF);
    fill(parent, parent + n, -1);
    dist[start] = 0;
    
    // Relaxation V-1 times
    for (int i = 0; i < n - 1; i++) {
        for (auto& e : edges) {
            if (dist[e.from] != INF && dist[e.from] + e.weight < dist[e.to]) {
                dist[e.to] = dist[e.from] + e.weight;
                parent[e.to] = e.from;
            }
        }
    }
    
    // Negative cycle check
    for (auto& e : edges) {
        if (dist[e.from] != INF && dist[e.from] + e.weight < dist[e.to]) {
            return false;  // There is a negative cycle
        }
    }
    
    return true;  // No negative cycle
}

// Finding all vertices reachable via a negative cycle
void findNegativeCycleNodes(int n) {
    bool relaxed[MAXN] = {};
    
    // Another V iterations - distances continue to decrease
    // only for vertices in or reachable from a negative cycle
    for (int i = 0; i < n; i++) {
        for (auto& e : edges) {
            if (dist[e.from] != INF && dist[e.from] + e.weight < dist[e.to]) {
                dist[e.to] = -INF;  // Mark as "infinitely small"
                relaxed[e.to] = true;
            }
        }
    }
}
```

**Complexity:** $O(V \cdot E)$ - slower than Dijkstra, but more universal.

**Optimization:** We can stop earlier if there is no relaxation in some iteration.

**Application:** Tasks with negative weights, arbitrage in finance.

### 2.3. Floyd-Warshall Algorithm
Floyd-Warshall finds the **shortest path between all pairs** of vertices using dynamic programming. It is used for small graphs (up to $\approx$ 400-500 vertices).

**Idea:** $dp[k][i][j]$ = the shortest path from $i$ to $j$ using only vertices $0...k$ as intermediates. It can be optimized to a 2D array.

**Full implementation:**
```cpp
const long long INF = 1e18;
long long dist[MAXN][MAXN];
int next_node[MAXN][MAXN];  // For path reconstruction

void floydWarshall(int n) {
    // Initialization
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (i == j) {
                dist[i][j] = 0;
            } else {
                dist[i][j] = INF;
            }
            next_node[i][j] = -1;
        }
    }
    
    // Add edges
    for (auto [u, v, w] : edges) {
        dist[u][v] = w;
        next_node[u][v] = v;
    }
    
    // Main algorithm
    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (dist[i][k] != INF && dist[k][j] != INF) {
                    if (dist[i][k] + dist[k][j] < dist[i][j]) {
                        dist[i][j] = dist[i][k] + dist[k][j];
                        next_node[i][j] = next_node[i][k];
                    }
                }
            }
        }
    }
    
    // Check for negative cycles
    for (int i = 0; i < n; i++) {
        if (dist[i][i] < 0) {
            // There is a negative cycle reachable from i
        }
    }
}

// Path reconstruction from u to v
vector<int> reconstructPath(int u, int v) {
    if (next_node[u][v] == -1) return {};  // No path
    
    vector<int> path = {u};
    while (u != v) {
        u = next_node[u][v];
        path.push_back(u);
    }
    return path;
}
```

**Complexity:** $O(V^3)$ - three nested loops.

**Applications:**
- Finding the transitive closure of a graph.
- Detection of negative cycles.
- Minimax/maximin paths (with modification).

---

## 3. Minimum Spanning Tree (MST)

A minimum spanning tree is a subset of the edges that connects all vertices with the minimum total weight sum.

### 3.1. Kruskal's Algorithm
Kruskal sorts all edges by weight and adds them one by one if they do not form a cycle. It uses Disjoint Set Union (DSU) for efficient checking.

**Full implementation:**
```cpp
struct Edge {
    int u, v;
    long long weight;
    
    bool operator<(const Edge& other) const {
        return weight < other.weight;
    }
};

// Disjoint Set Union (Union-Find)
int parent[MAXN], rankDSU[MAXN];

void initDSU(int n) {
    for (int i = 0; i < n; i++) {
        parent[i] = i;
        rankDSU[i] = 0;
    }
}

int find(int x) {
    if (parent[x] != x) {
        parent[x] = find(parent[x]);  // Path compression
    }
    return parent[x];
}

bool unite(int x, int y) {
    int px = find(x), py = find(y);
    if (px == py) return false;  // Already in the same set
    
    // Union by rank
    if (rankDSU[px] < rankDSU[py]) swap(px, py);
    parent[py] = px;
    if (rankDSU[px] == rankDSU[py]) rankDSU[px]++;
    
    return true;
}

long long kruskal(vector<Edge>& edges, int n) {
    sort(edges.begin(), edges.end());  // Sorting by weight
    initDSU(n);
    
    long long mstWeight = 0;
    vector<Edge> mstEdges;  // Edges in MST
    
    for (auto& e : edges) {
        if (unite(e.u, e.v)) {  // If they do not form a cycle
            mstWeight += e.weight;
            mstEdges.push_back(e);
            
            if (mstEdges.size() == n - 1) break;  // MST is ready
        }
    }
    
    // Check whether the graph is connected
    if (mstEdges.size() != n - 1) {
        return -1;  // No MST (the graph is disconnected)
    }
    
    return mstWeight;
}
```

**Complexity:** $O(E \log E)$ because of sorting.

### 3.2. Prim's Algorithm
Prim builds the MST incrementally, starting from an arbitrary vertex and always adding the cheapest edge that connects the tree to a new vertex.

**Full implementation:**
```cpp
vector<pair<int, long long>> adj[MAXN];  // {neighbor, weight}
bool inMST[MAXN];

long long prim(int start, int n) {
    fill(inMST, inMST + n, false);
    
    // Min-heap: {weight, {vertex, from-where}}
    priority_queue<pair<long long, pair<int, int>>, 
                   vector<pair<long long, pair<int, int>>>, 
                   greater<pair<long long, pair<int, int>>>> pq;
    
    long long mstWeight = 0;
    vector<pair<int, int>> mstEdges;
    
    // Start from start
    pq.push({0, {start, -1}});
    
    while (!pq.empty()) {
        auto [w, vp] = pq.top();
        auto [v, from] = vp;
        pq.pop();
        
        if (inMST[v]) continue;  // Already in MST
        
        inMST[v] = true;
        mstWeight += w;
        if (from != -1) {
            mstEdges.push_back({from, v});
        }
        
        // Add neighbors to the queue
        for (auto [u, ew] : adj[v]) {
            if (!inMST[u]) {
                pq.push({ew, {u, v}});
            }
        }
    }
    
    // Check whether all vertices are in MST
    for (int i = 0; i < n; i++) {
        if (!inMST[i]) return -1;  // Graph is disconnected
    }
    
    return mstWeight;
}
```

**Complexity:** $O(E \log V)$ with a priority queue.

**When to use which:**
- **Kruskal**: Easier to implement, good for sparse graphs.
- **Prim**: More efficient for dense graphs, better for dynamic modifications.

---

## 4. Topological Sorting

Topological sorting arranges the vertices of a DAG (Directed Acyclic Graph) such that for every edge $u \to v$, $u$ appears before $v$ in the arrangement. It exists only for acyclic graphs.

### 4.1. DFS Approach
We use DFS and add vertices to the result upon exiting the recursion (postorder), then reverse the order.

**Implementation:**
```cpp
vector<int> adj[MAXN];
bool visited[MAXN];
vector<int> topoOrder;

void topoDFS(int u) {
    visited[u] = true;
    
    for (int v : adj[u]) {
        if (!visited[v]) {
            topoDFS(v);
        }
    }
    
    topoOrder.push_back(u);  // Add upon exit
}

vector<int> topologicalSort(int n) {
    fill(visited, visited + n, false);
    topoOrder.clear();
    
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            topoDFS(i);
        }
    }
    
    reverse(topoOrder.begin(), topoOrder.end());
    return topoOrder;
}
```

### 4.2. Kahn's Algorithm
Uses the incoming degree (indegree) of each vertex. We start with the vertices with indegree 0 and gradually "remove" the edges.

**Implementation:**
```cpp
vector<int> adj[MAXN];
int indegree[MAXN];

vector<int> kahnTopologicalSort(int n) {
    fill(indegree, indegree + n, 0);
    
    // Calculate incoming degree
    for (int u = 0; u < n; u++) {
        for (int v : adj[u]) {
            indegree[v]++;
        }
    }
    
    queue<int> q;
    for (int i = 0; i < n; i++) {
        if (indegree[i] == 0) {
            q.push(i);
        }
    }
    
    vector<int> topoOrder;
    
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        topoOrder.push_back(u);
        
        // "Remove" edges from u
        for (int v : adj[u]) {
            indegree[v]--;
            if (indegree[v] == 0) {
                q.push(v);
            }
        }
    }
    
    // If we have not included all vertices, there is a cycle
    if (topoOrder.size() != n) {
        return {};  // The graph has a cycle
    }
    
    return topoOrder;
}
```

**Complexity:** $O(V + E)$ for both approaches.

**Applications:**
- Task scheduling with dependencies.
- Resolving dependencies in build systems.
- Determining the order of courses with prerequisites.

**Example:** A list of courses and prerequisites is given. In what order should you take the courses?

---

## 5. Practical Tips and Common Errors

### 5.1. Choosing the Appropriate Algorithm
**Algorithm Selection Table:**

| Task | Conditions | Algorithm |
|------|------------|-----------|
| Shortest path | Unweighted graph | BFS |
| Shortest path | Positive weights | Dijkstra |
| Shortest path | Negative weights | Bellman-Ford |
| All-pairs shortest path | Small graph ($V \leq 500$) | Floyd-Warshall |
| MST | Sparse graph | Kruskal |
| MST | Dense graph | Prim |
| Cycle check | DAG | Topological sorting |

### 5.2. Common Errors
**1. Using Dijkstra with negative weights**
```cpp
// WRONG! Dijkstra does not work with negative weights
// Use Bellman-Ford instead
```

**2. Forgetting to check for outdated entries in Dijkstra**
```cpp
// CORRECT: 
if (d > dist[u]) continue;  // Very important!
```

**3. Incorrect initialization of dist array**
```cpp
// WRONG:
memset(dist, INF, sizeof(dist));  // Initializes by bytes!

// CORRECT:
fill(dist, dist + n, INF);
```

**4. Integer overflow in paths**
```cpp
// Use long long for distances!
vector<long long> dist(n, INF);
```

### 5.3. Optimizations
**0-1 BFS:** For graphs with weights only 0 and 1, use deque instead of priority_queue:
```cpp
void bfs01(int start) {
    vector<long long> dist(n, INF);
    deque<int> dq;
    
    dist[start] = 0;
    dq.push_back(start);
    
    while (!dq.empty()) {
        int u = dq.front();
        dq.pop_front();
        
        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                
                if (w == 0) {
                    dq.push_front(v);  // Weight 0 - priority
                } else {
                    dq.push_back(v);   // Weight 1 - to the back
                }
            }
        }
    }
}
```

---

## 6. Practice Tasks

### Easy tasks (for beginners):
1. **CSES - Building Roads**: Finding components with DFS/BFS.
2. **CSES - Message Route**: Shortest path with BFS.
3. **CSES - Labyrinth**: Maze traversal with BFS.
4. **Codeforces 277A**: Connected components.

### Intermediate tasks:
5. **CSES - Shortest Routes I**: Dijkstra.
6. **CSES - Shortest Routes II**: Floyd-Warshall.
7. **CSES - Road Reparation**: MST with Kruskal.
8. **CSES - Course Schedule**: Topological sorting.
9. **Codeforces 20C**: Dijkstra with path reconstruction.
10. **CSES - High Score**: Bellman-Ford with negative cycles.

### Hard tasks:
11. **CSES - Flight Discount**: Dijkstra with modifications.
12. **UVa 558**: Wormholes (Bellman-Ford).
13. **CSES - Investigation**: Counting shortest paths.
14. **Codeforces 295B**: Floyd-Warshall with modifications.

---

## 🏁 Conclusion

Graph algorithms are a fundamental part of competitive programming and informatics Olympiads. The key to success is:

1. **Understanding when to use which algorithm** - see the table above.
2. **Attention to detail** - overflow check, correct initialization, handling corner cases.
3. **Practice** - solve many tasks to reinforce concepts.
4. **Complexity analysis** - always check whether the chosen algorithm will handle the constraints.

Mastering these algorithms will give you a strong foundation for solving complex graph tasks in Olympiads like IOI, CEOI, and Balkan OI. Good luck!
