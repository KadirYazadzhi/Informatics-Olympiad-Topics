# 🌊 Flows and Cuts: Max-Flow Min-Cut

Network Flow is a powerful modeling tool for problems involving transportation, matching, and connectivity.

## 1. The Max-Flow Problem

Given a directed graph $G = (V, E)$ with edge capacities $c(u, v)$.
Find the maximum amount of "flow" that can be sent from a **Source ($s$)** to a **Sink ($t$)**, satisfying:
1.  **Capacity Constraint**: Flow $f(u, v) \le c(u, v)$.
2.  **Flow Conservation**: Incoming flow = Outgoing flow (except for $s$ and $t$).

## 2. Algorithms

### 2.1. Ford-Fulkerson Method
Iteratively find an **augmenting path** in the residual graph and add flow along it.
The residual capacity is $c_f(u, v) = c(u, v) - f(u, v)$. Reverse edges have capacity equal to the flow sent.

### 2.2. Edmonds-Karp Algorithm
Implementation of Ford-Fulkerson using **BFS** to find the shortest augmenting path (in number of edges).
Complexity: $O(V E^2)$.
Good enough for $V < 100, E < 1000$.

### 2.3. Dinic's Algorithm
The standard for competitive programming.
*   Builds a **Level Graph** using BFS (only edges $(u, v)$ where $dist(v) = dist(u) + 1$).
*   Sends multiple flows ("Blocking Flow") using DFS on the Level Graph.
*   Complexity: $O(V^2 E)$. For Unit Networks (like Bipartite Matching), it is $O(EV)$. 

```cpp
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

const long long INF = 1e18;

struct Edge {
    int v;
    long long cap, flow;
    int rev; // index of reverse edge
};

vector<vector<Edge>> adj;
vector<int> level;
vector<int> ptr;

void add_edge(int u, int v, long long cap) {
    adj[u].push_back({v, cap, 0, (int)adj[v].size()});
    adj[v].push_back({u, 0, 0, (int)adj[u].size() - 1});
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
    for (int& cid = ptr[u]; cid < adj[u].size(); ++cid) {
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

long long dinic(int s, int t) {
    long long flow = 0;
    while (bfs(s, t)) {
        fill(ptr.begin(), ptr.end(), 0);
        while (long long pushed = dfs(s, t, INF)) {
            flow += pushed;
        }
    }
    return flow;
}
```

## 3. Min-Cut Max-Flow Theorem
The maximum flow from $s$ to $t$ is exactly equal to the capacity of the **Minimum Cut** separating $s$ and $t$.
A cut partitions vertices into two sets $S$ (containing $s$) and $T$ (containing $t$). The capacity is the sum of edges going from $S$ to $T$.

**Applications**:
*   **Image Segmentation**: Foregound/Background separation.
*   **Project Selection**: Closure problem.

## 4. Bipartite Matching
Finding the maximum matching in a bipartite graph is equivalent to Max Flow.
*   Create source $s$ connected to all Left nodes (cap 1).
*   Create sink $t$ connected from all Right nodes (cap 1).
*   Edges Left $\to$ Right have cap 1 (or $\infty$).
*   Max Flow = Max Matching.

## 5. Practice Problems
1.  **SPOJ FASTFLOW**: Pure max flow benchmark.
2.  **Codeforces 1082G**: Petya and Graph (Project Selection / Min-Cut).
3.  **UVa 820**: Internet Bandwidth.