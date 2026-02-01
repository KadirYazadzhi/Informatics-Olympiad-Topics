# 🌐 Graph Concept

Graphs are fundamental data structures used to model relationships between objects. They find application in various fields such as computer science, logistics, network analysis, social networks, and many others. Studying graphs provides a foundation for understanding complex algorithms and applications widely used in practice.


## 📋 Content

1. **Definition and Types of Graphs**  
2. **Basic Components of Graphs**  
3. **Types of Graph Representation**  
    - Adjacency Matrix  
    - Adjacency List  
    - Edge List  
4. **Operations on Graphs**  
5. **Graph Traversal**  
    - BFS (Breadth-First Search)  
    - DFS (Depth-First Search)  
6. **Special Types of Graphs**  
    - Cycles  
    - Trees  
    - Bipartite Graphs  
7. **Graph Applications**  
8. **Examples and Code**  
9. **Conclusion**


## 🖋️ Definition and Types of Graphs

A **Graph** ( G = (V, E) ) consists of:
-  V : A set of vertices (or nodes).
-  E : A set of edges connecting pairs of vertices.

### Types of Graphs
1. **Directed Graphs**:
   - Edges have a direction ((u, v) ≠ (v, u)).
   - Example: Graph of web pages where each page has links to others.

2. **Undirected Graphs**:
   - Edges do not have a direction ((u, v) = (v, u)).
   - Example: Modeling friendships in a social network.

3. **Weighted Graphs**:
   - Each edge has an associated weight or cost (e.g., distance, time, expenses).

4. **Connected Graphs**:
   - There is a path between every pair of vertices.

5. **Trees**:
   - A connected graph without cycles. 
   - A special case of a graph used in many algorithms.

6. **Bipartite Graphs**:
   - Vertices can be divided into two disjoint sets such that every edge connects a vertex in one set to a vertex in the other.


## ⚙️ Basic Components of Graphs

1. **Vertices**: The objects that are connected.
2. **Edges**: The connections between vertices.
3. **Degree of a Vertex**:
   - **Undirected Graph**: Number of incident edges.
   - **Directed Graph**: 
     - **In-degree**: Number of incoming edges.
     - **Out-degree**: Number of outgoing edges.
4. **Paths**: A sequence of vertices connected by edges.
5. **Cycles**: A closed path where the first and last vertex coincide.


## 📊 Types of Graph Representation

### 1. Adjacency Matrix
A 2D array n × n , where A[i][j] is:
- 1 (or the weight) if there is an edge between i and j.
- 0 if there is no edge.

#### Example:
Undirected graph with vertices {A, B, C}:

|     | A | B | C |
|-----|---|---|---|
| **A** | 0 | 1 | 1 |
| **B** | 1 | 0 | 1 |
| **C** | 1 | 1 | 0 |


### 2. Adjacency List
A list where each vertex is connected to a set of adjacent vertices.

#### Example:
```
A: B, C
B: A, C
C: A, B
```


### 3. Edge List
A list of pairs (u, v) representing the edges of the graph.

#### Example:
```
(A, B)
(A, C)
(B, C)
```


## 🔄 Operations on Graphs

1. **Adding Vertices**: Adds a new vertex to the graph.
2. **Removing Vertices**: Removes an existing vertex and its connected edges.
3. **Adding Edges**: Creates a connection between two vertices.
4. **Removing Edges**: Removes an existing connection between two vertices.


## 🔍 Graph Traversal

### BFS (Breadth-First Search)
Traverses the graph level by level.

#### Algorithm:
1. Add the starting vertex to a queue.
2. Mark it as visited.
3. While the queue is not empty:
   - Dequeue a vertex and process it.
   - Add its unvisited neighbors to the queue.

#### Example Code (C++):
```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

void bfs(int start, vector<vector<int>>& graph) {
    vector<bool> visited(graph.size(), false);
    queue<int> q;

    q.push(start);
    visited[start] = true;

    while (!q.empty()) {
        int node = q.front();
        q.pop();
        cout << node << " ";

        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}

int main() {
    vector<vector<int>> graph = {
        {1, 2},
        {0, 3, 4},
        {0, 4},
        {1, 5},
        {1, 2, 5},
        {3, 4}
    };

    bfs(0, graph);
    return 0;
}
```

### DFS (Depth-First Search)
Traverses the graph by exploring as far as possible along each branch before backtracking.

#### Example Code (C++):
```cpp
#include <iostream>
#include <vector>
using namespace std;

void dfs(int node, vector<vector<int>>& graph, vector<bool>& visited) {
    visited[node] = true;
    cout << node << " ";

    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            dfs(neighbor, graph, visited);
        }
    }
}

int main() {
    vector<vector<int>> graph = {
        {1, 2},
        {0, 3, 4},
        {0, 4},
        {1, 5},
        {1, 2, 5},
        {3, 4}
    };

    vector<bool> visited(graph.size(), false);
    dfs(0, graph, visited);
    return 0;
}
```


## 🌟 Special Types of Graphs

1. **Cyclic Graphs**: Graphs containing at least one cycle.
2. **Acyclic Graphs**: Graphs with no cycles.
3. **Planar Graphs**: Graphs that can be drawn on a plane without edges crossing.
4. **Dense and Sparse Graphs**:
   - **Dense**: The number of edges is close to the maximum possible.
   - **Sparse**: The number of edges is small relative to the number of vertices.


## 🌟 Graph Applications

1. **Social Networks**: Modeling connections between users.
2. **Communication Networks**: Route optimization and traffic management.
3. **Search Algorithms**: Binary trees, mazes, and routes.
4. **Artificial Intelligence**: State graphs used in machine learning and planning.
5. **Logistics**: Optimization of deliveries and routes.


## 📚 Conclusion

Graphs are a powerful tool for solving complex problems, providing an intuitive way to represent relationships between objects. Through various techniques for representation, traversal, and analysis, they find application in a wide range of industries. Understanding graphs and related algorithms is key to understanding and developing modern technologies. In-depth study of basic concepts and regular practice with real examples is recommended to master their application.
