# 🌊 Flows and Cuts: From Beginners to Experts

Network flows are one of the most powerful tools in algorithmic programming. They model a wide range of problems: from goods transport and data routing to task allocation and image segmentation.

## 1. Maximum Flow

### 1.1. Definition
Let we have a directed graph $G = (V, E)$, where each edge $(u, v)$ has a capacity $c(u, v) \ge 0$. We have two special vertices:
*   **Source ($S$)**: The vertex from which the flow originates.
*   **Sink ($T$)**: The vertex where the flow drains.

A **flow** is a function $f(u, v)$ that satisfies two conditions:
1.  **Capacity Constraint**: $0 \le f(u, v) \le c(u, v)$.
2.  **Flow Conservation**: For every vertex $v \in V \setminus \{S, T\}$, the sum of incoming flow is equal to the sum of outgoing flow.
    $\sum_{u} f(u, v) = \sum_{w} f(v, w)$

The goal is to maximize the total flow leaving $S$ (which is equal to the flow entering $T$). 

### 1.2. Ford-Fulkerson Method
The basic idea is iterative flow augmentation.
1.  We start with $f(u, v) = 0$ for all edges.
2.  We search for a path from $S$ to $T$ in the **residual graph**. The residual capacity of an edge is $c_f(u, v) = c(u, v) - f(u, v)$.
    *   Important: If we send flow along $(u, v)$, we add a "back-edge" $(v, u)$ with capacity equal to the flow. This allows us to "undo" poor decisions later.
3.  If we find such a path (called an **Augmenting Path**), we increase the flow along it by the "bottleneck" (the minimum residual capacity along the path).
4.  We repeat until we can no longer reach $T$ from $S$.

### 1.3. Edmonds-Karp Algorithm
This is a specific implementation of Ford-Fulkerson that uses **BFS** to find the shortest (in terms of number of edges) augmenting path.
*   Complexity: $O(V E^2)$.
*   Suitable for graphs with up to 500-1000 vertices.

### 1.4. Dinic's Algorithm
The standard for competitions. It is much faster than Edmonds-Karp.
*   **Idea**: Instead of running BFS for each individual path, Dinic uses BFS to build a **Level Graph** (a graph containing only edges leading to a vertex further from $S$).
*   Then we run **DFS** multiple times on the Level Graph to send a "blocking flow" (several paths simultaneously).
*   Complexity: $O(V^2 E)$ in the general case.
*   For networks with unit capacities (e.g., matching): $O(E \sqrt{V})$.

```cpp
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

const long long INF = 1e18;

struct Edge {
    int v;
    long long cap, flow;
    int rev; // index of the back-edge in adj[v]
};

struct Dinic {
    int n;
    vector<vector<Edge>> adj;
    vector<int> level; // distance from S (BFS)
    vector<int> ptr;   // current index in adj for DFS (optimization)

    Dinic(int n) : n(n), adj(n), level(n), ptr(n) {}

    void add_edge(int u, int v, long long cap) {
        Edge a = {v, cap, 0, (int)adj[v].size()};
        Edge b = {u, 0, 0, (int)adj[u].size()}; // Back-edge with 0 capacity
        adj[u].push_back(a);
        adj[v].push_back(b);
    }

    bool bfs(int s, int t) {
        fill(level.begin(), level.end(), -1);
        level[s] = 0;
        queue<int> q;
        q.push(s);
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            for (auto& e : adj[u]) {
                if (e.cap - e.flow > 0 && level[e.v] == -1) {
                    level[e.v] = level[u] + 1;
                    q.push(e.v);
                }
            }
        }
        return level[t] != -1;
    }

    long long dfs(int u, int t, long long pushed) {
        if (pushed == 0 || u == t) return pushed;
        for (int& cid = ptr[u]; cid < (int)adj[u].size(); ++cid) {
            auto& e = adj[u][cid];
            if (level[u] + 1 != level[e.v] || e.cap - e.flow == 0) continue;
            long long tr = dfs(e.v, t, min(pushed, e.cap - e.flow));
            if (tr == 0) continue;
            e.flow += tr;
            adj[e.v][e.rev].flow -= tr;
            return tr;
        }
        return 0;
    }

    long long max_flow(int s, int t) {
        long long flow = 0;
        while (bfs(s, t)) {
            fill(ptr.begin(), ptr.end(), 0);
            while (long long pushed = dfs(s, t, INF)) {
                flow += pushed;
            }
        }
        return flow;
    }
};
```

---

## 2. Max-Flow Min-Cut Theorem

An **s-t cut** is a partition of the vertices into two sets: $A$ (containing $S$) and $B$ (containing $T$). The capacity of the cut is the sum of the capacities of the edges going from $A$ to $B$.

**Theorem**: The value of the maximum flow is equal to the capacity of the minimum cut.

### Application: Project Selection Problem
We have a set of projects (bringing profit) and a set of tools/resources (costing money). Each project depends on certain tools. We want to choose projects and tools such that profit minus costs is maximized.

**Solution with Min-Cut**:
1.  We create a source $S$ and connect $S$ to all projects with capacity = profit.
2.  We create a sink $T$ and connect all tools to $T$ with capacity = cost.
3.  If project $P$ requires tool $I$, we add an edge $P \to I$ with capacity $\infty$.
4.  The minimum cut in this graph represents the minimum loss (unrealized profit + paid costs).
5.  Answer = (Sum of all possible profits) - Max Flow.

---

## 3. Bipartite Matching

The task of finding the maximum number of pairs in a bipartite graph can be directly solved with Maximum Flow.
1.  Add $S$ and $T$.
2.  Connect $S$ to all vertices of the left part with capacity 1.
3.  Connect all vertices of the right part to $T$ with capacity 1.
4.  Direct all existing edges from left to right with capacity 1 (or $\infty$).
5.  Max Flow = Max Matching.

With Dinic, this works in $O(E \sqrt{V})$, which is equivalent to the specialized Hopcroft-Karp algorithm.

---

## 4. Flows with Demands

Sometimes edges have not only a capacity $C_{max}$, but also a lower bound $L_{min}$ (i.e., at least that much flow must mandatory pass through).

### 4.1. Feasible Circulation
To solve this:
1.  For each edge $(u, v)$ with boundaries $[L, R]$, we replace it with an edge with capacity $R - L$.
2.  We create a `balance` array for each vertex. For each edge $(u, v)$ with a lower bound $L$, we "take" $L$ from $u$ (`balance[u] -= L`) and "give" it to $v$ (`balance[v] += L`).
3.  We create new super-source $SS$ and super-sink $TT$.
    *   If `balance[v] > 0`, we add an edge $SS \to v$ with capacity `balance[v]`.
    *   If `balance[v] < 0`, we add an edge $v \to TT$ with capacity `-balance[v]`.
4.  Run Max Flow from $SS$ to $TT$. If all edges leaving $SS$ are saturated (flow == capacity), then a feasible circulation exists.

---

## 5. Min-Cost Max-Flow

If each edge also has a "cost" per unit of flow, the task becomes more complex. We seek the maximum flow with the minimum total cost.
This is usually solved through a modification of Edmonds-Karp where instead of BFS we use **SPFA** (Shortest Path Faster Algorithm) or **Dijkstra with potentials** (to handle negative edges) to find the cheapest path in the residual graph.

## 6. Practice Tasks
1.  **SPOJ FASTFLOW**: A test for the speed of your Dinic.
2.  **CSES Download Speed**: Classical Max Flow.
3.  **CSES Police Chase**: Finding the edges of the Min-Cut.
4.  **Codeforces 1082G**: Petya and Graph (Project Selection).
5.  **UVa 10330**: Power Transmission (Flows with node capacities - split the vertex into $v_{in}$ and $v_{out}$). 

Good luck with flows! Once you understand how to model the task as a graph, the solution is often just a Dinic template.
