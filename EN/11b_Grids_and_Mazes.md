# 🌌 Square Grids, Mazes and Regions

Grids are two-dimensional arrays that model physical space.

## 🗺️ Representation and Movement
Usually use `int grid[N][M]`. For movement in 4 directions (up, down, left, right) use direction arrays:
```cpp
int dx[] = {-1, 1, 0, 0};
int dy[] = {0, 0, -1, 1};

for (int i = 0; i < 4; i++) {
    int nx = x + dx[i];
    int ny = y + dy[i];
    if (isValid(nx, ny)) { /* move */ }
}
```

## 🏃‍♂️ Pathfinding

### 1. BFS (Breadth-First Search)
Finds the **shortest path** in a grid (without weights).
- Uses a queue (`std::queue`).
- Maintain a `dist[N][M]` array for distance.

### 2. DFS (Depth-First Search)
Used for reachability checks and finding connected regions (Flood Fill).

## 🌊 Flood Fill Algorithm
Filling a region with a specific color.
- **Example**: How many cells are in the "lake" we landed in?
```cpp
void floodFill(int x, int y) {
    if (!isValid(x, y) || visited[x][y]) return;
    visited[x][y] = true;
    count++;
    for (int i = 0; i < 4; i++) floodFill(x + dx[i], y + dy[i]);
}
```

---

## 🏁 Conclusion
Grids are at the heart of many graph algorithms. Mastering BFS/DFS on a grid is mandatory for every beginner programmer.
