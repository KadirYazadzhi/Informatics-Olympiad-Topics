# 🧭 Basic Knowledge of Computational Geometry

## 📖 Introduction to Computational Geometry

Computational geometry is a branch of computer science that studies algorithms for processing geometric objects. It has wide applications in computer graphics, robotics, geographic information systems (GIS), computer vision, physical simulations, and many other fields.

### 🔎 Main Applications of Computational Geometry

1. **Computer Graphics** – modeling and visualization of geometric shapes.
2. **Robotics** – motion planning and obstacle avoidance.
3. **Geographic Information Systems (GIS)** – analysis of spatial data.
4. **Computer Vision** – shape recognition, image processing.
5. **Physical Simulations** – collision detection, simulation of fluids and rigid bodies.

## 🏗 Basic Geometric Objects

### 📌 Points

A point is a basic geometric object represented by coordinates:
- In two-dimensional space (2D): **(x, y)**
- In three-dimensional space (3D): **(x, y, z)**

Example of point representation in C++:
```cpp
struct Point {
    double x, y;
};
```

### 📏 Lines and Segments
A line is an infinite geometric object that can be defined by the equation: ```Ax+By+C=0```

A segment is a part of a line defined by two endpoints `(x1, y1)` and `(x2, y2)`.

Example of segment representation:
```cpp
struct LineSegment {
    Point p1, p2;
};
```

### 🔷 Rectangles and Polygons

- A rectangle is defined by two opposite corner points.
- A polygon is a collection of connected segments.

Example of rectangle representation:
```cpp
struct Rectangle {
    double x1, y1, x2, y2;
};
```

Example of polygon representation:
```cpp
#include <vector>
using namespace std;

struct Polygon {
    vector<Point> vertices;
};
```

## 📐 Operations with Rectangles

### 📌 Calculating Area

Formula for the area of a rectangle: ```A=(x2−x1)×(y2−y1)```

C++ Code:
```cpp
double area(Rectangle r) {
    return (r.x2 - r.x1) * (r.y2 - r.y1);
}
```

### 📌 Calculating Perimeter
Formula: ```P=2×((x2−x1)+(y2−y1))```

Code:
```cpp
double perimeter(Rectangle r) {
    return 2 * ((r.x2 - r.x1) + (r.y2 - r.y1));
}
```

### 🔎 Checking if a Point is Inside a Rectangle
```cpp
bool isInside(Rectangle r, Point p) {
    return (p.x >= r.x1 && p.x <= r.x2 && p.y >= r.y1 && p.y <= r.y2);
}
```

### 🛑 Checking for Intersection of Two Rectangles
Two rectangles intersect if the condition is met:
```cpp
bool doIntersect(Rectangle r1, Rectangle r2) {
    return !(r1.x1 > r2.x2 || r2.x1 > r1.x2 || r1.y1 > r2.y2 || r2.y1 > r1.y2);
}
```

### 🔄 Union of Two Rectangles
If two rectangles overlap, their union rectangle is found with:
```cpp
Rectangle unionRectangle(Rectangle r1, Rectangle r2) {
    return {min(r1.x1, r2.x1), min(r1.y1, r2.y1), max(r1.x2, r2.x2), max(r1.y2, r2.y2)};
}
```

## 🗺 Square Grids, Mazes, and Regions

### 📋 Square Grids
A square grid represents a two-dimensional array where each cell has a specific state.

Example representation:
```cpp
int grid[5][5] = {
    {0, 1, 0, 0, 0},
    {0, 1, 0, 1, 0},
    {0, 0, 0, 1, 0},
    {1, 1, 0, 0, 0},
    {0, 0, 0, 1, 0}
};
```

### 🏃‍♂️ Finding a Path in a Maze (BFS)
The **BFS** algorithm finds the shortest path in a maze:
```cpp
#include <iostream>
#include <queue>
using namespace std;

struct Cell {
    int x, y, dist;
};

bool isValid(int x, int y, int rows, int cols, int grid[][5], bool visited[][5]) {
    return (x >= 0 && x < rows && y >= 0 && y < cols && grid[x][y] == 0 && !visited[x][y]);
}

int shortestPath(int grid[5][5], int startX, int startY, int endX, int endY) {
    int dx[] = {-1, 1, 0, 0};
    int dy[] = {0, 0, -1, 1};
    bool visited[5][5] = {false};
    queue<Cell> q;
    q.push({startX, startY, 0});
    visited[startX][startY] = true;

    while (!q.empty()) {
        Cell current = q.front(); q.pop();
        if (current.x == endX && current.y == endY) return current.dist;
        for (int i = 0; i < 4; i++) {
            int newX = current.x + dx[i];
            int newY = current.y + dy[i];
            if (isValid(newX, newY, 5, 5, grid, visited)) {
                visited[newX][newY] = true;
                q.push({newX, newY, current.dist + 1});
            }
        }
    }
    return -1;
}

int main() {
    int grid[5][5] = {
        {0, 1, 0, 0, 0},
        {0, 1, 0, 1, 0},
        {0, 0, 0, 1, 0},
        {1, 1, 0, 0, 0},
        {0, 0, 0, 1, 0}
    };
    cout << "Shortest path: " << shortestPath(grid, 0, 0, 4, 4) << endl;
    return 0;
}
```

### 🏁 Conclusion
Computational geometry plays an important role in various spheres of science and technology. Understanding basic algorithms for processing geometric objects and spatial structures is critical for creating efficient software solutions. 🚀
