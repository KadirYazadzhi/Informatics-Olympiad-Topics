# 🌌 Grids, Mazes, and Advanced Traversals

Grids are two-dimensional structures that represent physical or abstract spaces. In competitive programming, they are modeled as implicit graphs where each cell $(r, c)$ is a vertex, and edges exist between adjacent cells.

---

## 1. Grid Fundamentals

### 1.1. Coordinates and Indexing
Most grids are $R 	imes C$ matrices.
*   **0-indexed**: `r` in $[0, R-1]$, `c` in $[0, C-1]$. Standard for C++.
*   **1-indexed**: `r` in $[1, R]$, `c` in $[1, C]$. Sometimes used to simplify boundary checks (by padding the grid with obstacles).

### 1.2. Validity Checks
Always validate coordinates before array access to avoid `SIGSEGV`.

```cpp
bool isValid(int r, int c, int R, int C) {
    return r >= 0 && r < R && c >= 0 && c < C;
}
```

### 1.3. Direction Arrays
Instead of multiple `if` statements for neighbors, use direction vectors.

**4-way connectivity (Von Neumann neighborhood)**:
```cpp
int dr[] = {-1, 1, 0, 0}; // Up, Down, Left, Right
int dc[] = {0, 0, -1, 1};

for (int i = 0; i < 4; i++) {
    int nr = r + dr[i], nc = c + dc[i];
    if (isValid(nr, nc, R, C)) { /* process */ }
}
```

**8-way connectivity (Moore neighborhood)**:
Includes diagonals.
```cpp
int dr[] = {-1, -1, -1, 0, 0, 1, 1, 1};
int dc[] = {-1, 0, 1, -1, 1, -1, 0, 1};
```

---

## 2. Searching in Grids

### 2.1. DFS (Flood Fill)
Ideal for:
*   Counting connected components ("islands").
*   Finding the area of a region.
*   Checking reachability.

```cpp
void floodFill(int r, int c, int color, int target) {
    if (!isValid(r, c, R, C) || grid[r][c] != target || vis[r][c]) return;
    vis[r][c] = true;
    grid[r][c] = color;
    for (int i = 0; i < 4; i++) floodFill(r + dr[i], c + dc[i], color, target);
}
```

### 2.2. BFS (Shortest Path)
BFS is the **only** algorithm for finding the shortest path in an unweighted grid.
*   Uses a `std::queue<pair<int, int>>`.
*   Maintains a `dist[R][C]` array initialized to -1.

```cpp
int bfs(int sr, int sc, int tr, int tc) {
    queue<pair<int, int>> q;
    q.push({sr, sc});
    dist[sr][sc] = 0;
    
    while (!q.empty()) {
        auto [r, c] = q.front(); q.pop();
        if (r == tr && c == tc) return dist[r][c];
        
        for (int i = 0; i < 4; i++) {
            int nr = r + dr[i], nc = c + dc[i];
            if (isValid(nr, nc, R, C) && grid[nr][nc] != '#' && dist[nr][nc] == -1) {
                dist[nr][nc] = dist[r][c] + 1;
                q.push({nr, nc});
            }
        }
    }
    return -1; // Unreachable
}
```

---

## 3. Advanced Grid Techniques

### 3.1. Multi-Source BFS
Used when multiple "sources" act simultaneously (e.g., several fires spreading).
1.  Initialize `dist` to $\infty$.
2.  Push **all** source coordinates into the queue with `dist = 0`.
3.  Run a single BFS. This computes for each cell the distance to the **nearest** source.

### 3.2. 0-1 BFS
If moving between cells has cost 0 or 1.
*   Use a `std::deque`.
*   If cost is 0: `push_front`.
*   If cost is 1: `push_back`.
This is faster than Dijkstra ($O(V+E)$).

### 3.3. BFS with Bitmask
If the state depends on more than just $(r, c)$ (e.g., "shortest path after collecting 3 keys").
*   State: `(r, c, mask)`, where `mask` is a bitmask of collected keys.
*   `dist[R][C][1 << K]`.

---

## 4. Grid Geometry and Tricks

### 4.1. Mirror Reflection
If a path "bounces" off grid boundaries, it is often easier to "unfold" the grid into a larger coordinate system instead of simulating bounces.

### 4.2. Manhattan vs. Chebyshev Distance
*   **Manhattan** (4-way): $|r_1-r_2| + |c_1-c_2|$.
*   **Chebyshev** (8-way): $\max(|r_1-r_2|, |c_1-c_2|)$.

---

## 5. Practice Problems
1.  **CSES Counting Rooms**: Connected components.
2.  **CSES Labyrinth**: BFS with path reconstruction.
3.  **CSES Monsters**: Multi-source BFS vs Single BFS.
4.  **Codeforces 1063B**: Labyrinth (0-1 BFS).
5.  **UVa 1103**: Ancient Messages (Complex Flood Fill).

## 6. Conclusion
Grid problems are common and often serve as wrappers for graph algorithms. Master the direction arrays and BFS/DFS templates, and pay extreme attention to boundary conditions.
