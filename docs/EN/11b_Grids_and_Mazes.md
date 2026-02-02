# 🌌 Grids, Mazes, and Traversal (Grids)

Grids are the most common representation of maps in competitive programming (mazes, games, terrains). They are actually graphs where each cell is a vertex, and adjacent cells are connected by edges.

## 1. Basic Concepts

### 1.1. Coordinates and Validity
Usually, we work with a matrix `grid[R][C]`. The row is $r$, and the column is $c$.
It is important to always check whether we have gone outside the map.

```cpp
const int MAXN = 1000;
int R, C;
int grid[MAXN][MAXN];
bool visited[MAXN][MAXN];

bool isValid(int r, int c) {
    return r >= 0 && r < R && c >= 0 && c < C; // Be careful with 0-based vs 1-based
}
```

### 1.2. Direction Arrays
To avoid writing 4 `if` statements for each direction, we use arrays for changing coordinates.

**4 Directions (North, South, West, East):**
```cpp
int dr[] = {-1, 1, 0, 0};
int dc[] = {0, 0, -1, 1};
```

**8 Directions (Including diagonals - for King/Queen):**
```cpp
int dr[] = {-1, -1, -1, 0, 0, 1, 1, 1};
int dc[] = {-1, 0, 1, -1, 1, -1, 0, 1};
```

**Knight Moves:**
```cpp
int dr[] = {-2, -2, -1, -1, 1, 1, 2, 2};
int dc[] = {-1, 1, -2, 2, -2, 2, -1, 1};
```

## 2. Depth First Search (DFS) - Flood Fill

DFS is ideal for tasks of the type "find the number of connected components" (e.g., islands in a sea) or "size of the current area". The algorithm, known as **Flood Fill**, "floods" the entire reachable zone.

### Task: Number of Islands
Given a map where `1` is land and `0` is water. How many islands are there?

```cpp
void dfs(int r, int c) {
    visited[r][c] = true;
    
    // Try all 4 directions
    for (int i = 0; i < 4; i++) {
        int nr = r + dr[i];
        int nc = c + dc[i];
        
        // If within map, not visited, and is land
        if (isValid(nr, nc) && !visited[nr][nc] && grid[nr][nc] == 1) {
            dfs(nr, nc);
        }
    }
}

int countIslands() {
    int islands = 0;
    // Reset visited...
    for (int i = 0; i < R; i++) {
        for (int j = 0; j < C; j++) {
            if (grid[i][j] == 1 && !visited[i][j]) {
                islands++; // Found a new island
                dfs(i, j); // Mark its entire territory
            }
        }
    }
    return islands;
}
```
Complexity: $O(R \cdot C)$, because each cell is visited at most once.

## 3. Breadth First Search (BFS) - Shortest Path

BFS is the **only** correct way to find the shortest path in an unweighted graph (a grid where each step has a cost of 1). DFS does not guarantee the shortest path!

### Task: Exit from a Maze
Minimum number of steps from Start (S) to End (E). `#` are walls.

```cpp
#include <queue>
#include <tuple>

using namespace std;

int dist[MAXN][MAXN]; // Stores distance. -1 for unvisited.

int bfs(int startR, int startC, int endR, int endC) {
    // Initialization
    for(int i=0; i<R; i++) 
        for(int j=0; j<C; j++) 
            dist[i][j] = -1;

    queue<pair<int, int>> q;
    q.push({startR, startC});
    dist[startR][startC] = 0;

    while (!q.empty()) {
        auto [r, c] = q.front();
        q.pop();

        if (r == endR && c == endC) return dist[r][c];

        for (int i = 0; i < 4; i++) {
            int nr = r + dr[i];
            int nc = c + dc[i];

            // Check: validity, not a wall, not visited
            if (isValid(nr, nc) && grid[nr][nc] != '#' && dist[nr][nc] == -1) {
                dist[nr][nc] = dist[r][c] + 1;
                q.push({nr, nc});
            }
        }
    }
    return -1; // No path
}
```

## 4. Multi-Source BFS

If we have a "fire" that starts from 5 different cells simultaneously, and we want to know when it will reach us.
*   Simply add **all** starting cells to the queue at the beginning (with `dist = 0`).
*   Start a standard BFS.

## 5. Practice Tasks
1.  **CSES Counting Rooms**: Classical Connected Components.
2.  **CSES Labyrinth**: BFS + path reconstruction (via a `parent` array).
3.  **Codeforces 598D**: Igor In the Museum (Flood Fill with precalculation).
4.  **UVa 439 - Knight Moves**: BFS with knight's moves.

## 🏁 Tips
*   Always check the boundaries (`isValid`) **first** in the `if` statement to avoid a `Segmentation Fault` when accessing the array.
*   The `visited` array is mandatory to avoid moving in circles.
*   For BFS, always use `queue`, not `stack` or recursion.

---

## 6. Path Reconstruction

Often tasks require not only finding whether there is a path but also outputting it. For this, we maintain a `parent` array that records where we came from for each cell.

```cpp
struct Cell {
    int r, c;
};

Cell parent[MAXN][MAXN];

int bfs_with_path(int sr, int sc, int er, int ec) {
    queue<pair<int,int>> q;
    q.push({sr, sc});
    dist[sr][sc] = 0;
    parent[sr][sc] = {-1, -1}; // Start
    
    while (!q.empty()) {
        auto [r, c] = q.front();
        q.pop();
        
        if (r == er && c == ec) {
            // Reconstructing the path
            vector<pair<int,int>> path;
            int cr = er, cc = ec;
            while (cr != -1) {
                path.push_back({cr, cc});
                auto p = parent[cr][cc];
                cr = p.r; cc = p.c;
            }
            reverse(path.begin(), path.end());
            
            // Printing the path
            for (auto [x, y] : path) {
                cout << "(" << x << ", " << y << ") ";
            }
            cout << endl;
            
            return dist[er][ec];
        }
        
        for (int i = 0; i < 4; i++) {
            int nr = r + dr[i];
            int nc = c + dc[i];
            
            if (isValid(nr, nc) && grid[nr][nc] != '#' && dist[nr][nc] == -1) {
                dist[nr][nc] = dist[r][c] + 1;
                parent[nr][nc] = {r, c};
                q.push({nr, nc});
            }
        }
    }
    return -1;
}
```

---

## 7. 0-1 BFS (Deque BFS)

If the cost of moves is not the same but is either 0 or 1, we can use **Deque BFS**:
*   Moves with cost 0 are added to the **front** of the queue.
*   Moves with cost 1 are added to the **back** of the queue.

This preserves the monotonicity of distances and works in $O(V + E)$.

```cpp
deque<pair<int,int>> dq;
dq.push_back({sr, sc});
dist[sr][sc] = 0;

while (!dq.empty()) {
    auto [r, c] = dq.front();
    dq.pop_front();
    
    for (int i = 0; i < 4; i++) {
        int nr = r + dr[i];
        int nc = c + dc[i];
        int cost = (grid[nr][nc] == 'X') ? 1 : 0; // Example: 'X' costs 1
        
        if (isValid(nr, nc) && dist[nr][nc] == -1) {
            dist[nr][nc] = dist[r][c] + cost;
            
            if (cost == 0) dq.push_front({nr, nc});
            else dq.push_back({nr, nc});
        }
    }
}
```

---

## 8. Common Problems and Optimizations

### 8.1. Memory
For large grids (e.g., $5000 \times 5000$), two-dimensional arrays may be too large. Use:
*   `vector<vector<int>>` for dynamic allocation.
*   One-dimensional array with index calculation: `index = r * C + c`.

### 8.2. Speed
*   For BFS, use `queue`, not `priority_queue` (unnecessary complexity).
*   For DFS, be careful not to exceed the stack memory during deep recursion. If $R \times C > 10^6$, use iterative DFS with `stack`.

---

## 🏁 Conclusion

Grids are a fundamental topic in competitive programming. Mastering DFS (for connected components) and BFS (for the shortest path) will open the doors to many algorithms such as Dijkstra, dynamic programming on graphs, and others.