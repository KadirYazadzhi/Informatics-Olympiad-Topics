# 🌌 Grids, Mazes, and Flood Fill

Grids are essentially implicit graphs where each cell $(r, c)$ is a node, connected to adjacent cells.

## 1. Coordinates and Directions

### 1.1. Representation
Usually `grid[R][C]`. Be careful with 0-based vs 1-based indexing.
Always check bounds before accessing!

```cpp
bool isValid(int r, int c, int R, int C) {
    return r >= 0 && r < R && c >= 0 && c < C;
}
```

### 1.2. Direction Arrays
Simplifies code significantly.

**4 Directions (UDLR)**:
```cpp
int dr[] = {-1, 1, 0, 0};
int dc[] = {0, 0, -1, 1};
```

**8 Directions (King/Queen)**:
```cpp
int dr[] = {-1, -1, -1, 0, 0, 1, 1, 1};
int dc[] = {-1, 0, 1, -1, 1, -1, 0, 1};
```

**Knight Moves**:
```cpp
int dr[] = {-2, -2, -1, -1, 1, 1, 2, 2};
int dc[] = {-1, 1, -2, 2, -2, 2, -1, 1};
```

## 2. Graph Traversal on Grids

### 2.1. DFS (Flood Fill)
Used to find connected components (e.g., counting islands) or size of a region.
Recursively visits all reachable cells.

```cpp
void dfs(int r, int c) {
    vis[r][c] = true;
    for (int i = 0; i < 4; i++) {
        int nr = r + dr[i];
        int nc = c + dc[i];
        if (isValid(nr, nc) && !vis[nr][nc] && grid[nr][nc] != '#') {
            dfs(nr, nc);
        }
    }
}
```

### 2.2. BFS (Shortest Path)
Finds the shortest path in an unweighted grid (min number of steps).
Crucial: Use a `queue` and a `dist` array.

```cpp
void bfs(int sr, int sc) {
    queue<pair<int, int>> q;
    q.push({sr, sc});
    dist[sr][sc] = 0;
    
    while (!q.empty()) {
        auto [r, c] = q.front(); q.pop();
        
        if (r == endR && c == endC) return; // Found
        
        for (int i = 0; i < 4; i++) {
            int nr = r + dr[i];
            int nc = c + dc[i];
            if (isValid(nr, nc) && dist[nr][nc] == -1) {
                dist[nr][nc] = dist[r][c] + 1;
                q.push({nr, nc});
            }
        }
    }
}
```

## 3. Multi-Source BFS
If you have multiple starting points (e.g., fire spreading from several locations), push ALL of them into the queue initially with `dist=0`.

## 4. 0-1 BFS
If edge weights are 0 or 1 (e.g., moving to specific cells costs 0, others 1), use a `deque`.
*   Weight 0: `push_front`.
*   Weight 1: `push_back`.
This is faster than Dijkstra ($O(V+E)$).

## 5. Practice
1.  **CSES Counting Rooms**: DFS/Flood Fill.
2.  **CSES Labyrinth**: BFS + Path reconstruction.
3.  **Codeforces 1105D**: Kilani and the Game (Multi-source BFS expansion).