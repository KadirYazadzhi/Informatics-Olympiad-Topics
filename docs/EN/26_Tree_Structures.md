# 🌳 Tree Structures

A tree is a connected acyclic graph (a graph without cycles). It is one of the most important structures in computer science due to its hierarchical nature and special properties. Trees appear everywhere - from file systems and organizational structures to complex algorithmic tasks.

**Basic properties of a tree:**
- A tree with $n$ vertices has exactly $n-1$ edges.
- Between any two vertices, there is exactly one simple path.
- Removing any edge divides the tree into two components.
- Adding any edge creates exactly one cycle.

---\n

## 1. Basic Concepts

*   **Root**: A special vertex where the hierarchy begins. In unrooted trees, we can choose an arbitrary vertex as the root.
*   **Parent**: The direct predecessor of a given vertex in the hierarchy.
*   **Child**: A direct successor of a given vertex.
*   **Leaf**: A vertex without children. In an unrooted tree, this is a vertex with degree 1.
*   **Subtree**: A tree formed by a given vertex and all its successors.
*   **Depth**: The distance from the root to a given vertex.
*   **Height**: The maximum distance from the root to a leaf. Alternatively, the height of a vertex is the maximum distance to a leaf in its subtree.
*   **Level**: The set of all vertices with the same depth.
*   **Binary Tree**: Each vertex has at most 2 children (left and right).
*   **Complete Binary Tree**: All levels are fully filled, except possibly the last one.

### 1.1. Basic Structure in Memory

```cpp
// Representation using adjacency lists
vector<int> adj[MAXN];

// Representation with parent and children (rooted tree)
int parent[MAXN];
vector<int> children[MAXN];

// Binary tree with pointers
struct Node {
    int value;
    Node *left, *right;
    Node(int val) : value(val), left(nullptr), right(nullptr) {}
};
```

## 2. Tree Traversal

Tree traversal is a fundamental operation. There are several classical ways:

### 2.1. Depth-First Search (DFS-based)

1.  **Pre-order** (Root → Left → Right): We visit the root before its children.
    - Used for: copying the tree, creating prefix notation.
    
```cpp
void preorder(Node* node) {
    if (!node) return;
    process(node->value);      // Process the root
    preorder(node->left);      // Traverse the left subtree
    preorder(node->right);     // Traverse the right subtree
}
```

2.  **In-order** (Left → Root → Right): We visit the root between the two subtrees.
    - Used for: BST traversal yields a sorted sequence.
    
```cpp
void inorder(Node* node) {
    if (!node) return;
    inorder(node->left);       // Traverse the left subtree
    process(node->value);      // Process the root
    inorder(node->right);      // Traverse the right subtree
}
```

3.  **Post-order** (Left → Right → Root): We visit the root after its children.
    - Used for: deleting the tree, calculating postfix expressions, Tree DP.
    
```cpp
void postorder(Node* node) {
    if (!node) return;
    postorder(node->left);     // Traverse the left subtree
    postorder(node->right);    // Traverse the right subtree
    process(node->value);      // Process the root
}
```

### 2.2. Breadth-First Search (BFS - Level Order)

We traverse the tree level by level, from left to right:

```cpp
void levelOrder(Node* root) {
    if (!root) return;
    queue<Node*> q;
    q.push(root);
    
    while (!q.empty()) {
        Node* current = q.front();
        q.pop();
        process(current->value);
        
        if (current->left) q.push(current->left);
        if (current->right) q.push(current->right);
    }
}
```

### 2.3. Traversal of a General Tree (Not necessarily binary)

```cpp
void dfs(int u, int parent = -1) {
    // Pre-order processing
    process(u);
    
    for (int v : adj[u]) {
        if (v != parent) {
            dfs(v, u);
        }
    }
    
    // Post-order processing
    postprocess(u);
}
```

## 3. Lowest Common Ancestor (LCA)

LCA (Lowest Common Ancestor) of vertices $u$ and $v$ is the deepest vertex that is an ancestor of both. This is one of the most important operations on trees.

**Applications of LCA:**
- Finding the distance between two vertices: $dist(u, v) = depth[u] + depth[v] - 2 \times depth[LCA(u, v)]$.
- Checking whether a vertex is on the path between two others.
- Range queries on paths in the tree.

### 3.1. Naive Approach

We lift both vertices up until they meet. Complexity: $O(h)$ where $h$ is the height.

```cpp
int lca_naive(int u, int v) {
    // First make them equal in depth
    while (depth[u] > depth[v]) u = parent[u];
    while (depth[v] > depth[u]) v = parent[v];
    
    // Then lift them simultaneously
    while (u != v) {
        u = parent[u];
        v = parent[v];
    }
    return u;
}
```

### 3.2. Binary Lifting

Allows for finding the LCA in $O(\log N)$ after $O(N \log N)$ preprocessing. This is the standard and most popular method.

**Idea:** For each vertex, we store its $2^i$-th ancestor for $i = 0, 1, 2, \ldots, \log N$.

```cpp
const int MAXN = 100005;
const int LOGN = 20;  // log2(100000) ≈ 17

int up[MAXN][LOGN];    // up[u][i] = 2^i-th ancestor of u
int depth[MAXN];
vector<int> adj[MAXN];

void dfs(int u, int p = 0) {
    up[u][0] = p;  // Direct parent
    
    // Calculate 2^i-th ancestors
    for (int i = 1; i < LOGN; i++) {
        up[u][i] = up[up[u][i-1]][i-1];
    }
    
    for (int v : adj[u]) {
        if (v != p) {
            depth[v] = depth[u] + 1;
            dfs(v, u);
        }
    }
}

// Returns the k-th ancestor of u
int kth_ancestor(int u, int k) {
    for (int i = 0; i < LOGN; i++) {
        if (k & (1 << i)) {
            u = up[u][i];
        }
    }
    return u;
}

bool is_ancestor(int u, int v) {
    // Check whether u is an ancestor of v
    return kth_ancestor(v, depth[v] - depth[u]) == u;
}

int lca(int u, int v) {
    // Ensure u is the deeper one
    if (depth[u] < depth[v]) swap(u, v);
    
    // Lift u to the level of v
    u = kth_ancestor(u, depth[u] - depth[v]);
    
    if (u == v) return u;
    
    // Binary search for LCA
    for (int i = LOGN - 1; i >= 0; i--) {
        if (up[u][i] != up[v][i]) {
            u = up[u][i];
            v = up[v][i];
        }
    }
    
    return up[u][0];  // Parent of the current position
}

// Initialization
void init(int root, int n) {
    depth[root] = 0;
    dfs(root);
}
```

### 3.3. Other Approaches for LCA

**Eulerian Tour + RMQ:** Complexity $O(N)$ preprocessing, $O(1)$ per query with Sparse Table.

**Tarjan's Offline LCA:** Useful when all queries are known in advance.

## 4. Tree Diameter

The diameter is the longest path between two vertices in the tree. This is an important tree characteristic with many applications.

### 4.1. Algorithm with two DFSs

The most efficient way to find the diameter:

```cpp
int farthest_node;
int max_dist;

void dfs(int u, int parent, int dist) {
    if (dist > max_dist) {
        max_dist = dist;
        farthest_node = u;
    }
    
    for (int v : adj[u]) {
        if (v != parent) {
            dfs(v, u, dist + 1);
        }
    }
}

int findDiameter(int n) {
    // Step 1: Find the farthest node from an arbitrary node (e.g., 1)
    max_dist = 0;
    dfs(1, -1, 0);
    
    int endpoint1 = farthest_node;
    
    // Step 2: Find the farthest node from endpoint1
    max_dist = 0;
    dfs(endpoint1, -1, 0);
    
    // max_dist now contains the diameter
    return max_dist;
}
```

**Explanation of why it works:**
- After the first DFS, we find one of the ends of the diameter.
- A second DFS from that end gives us the other end and the length.

### 4.2. Diameter with DP (single DFS)

Alternative approach using dynamic programming:

```cpp
int diameter = 0;

int dfs_diameter(int u, int parent) {
    int max1 = 0, max2 = 0;  // Two longest paths down
    
    for (int v : adj[u]) {
        if (v != parent) {
            int h = dfs_diameter(v, u);
            
            if (h > max1) {
                max2 = max1;
                max1 = h;
            } else if (h > max2) {
                max2 = h;
            }
        }
    }
    
    // The diameter through u is max1 + max2
    diameter = max(diameter, max1 + max2);
    
    // Return the longest path down from u
    return max1 + 1;
}
```

### 4.3. Finding the Tree Center

The center is the vertex/vertices that minimize the maximum distance to all other vertices. The center always lies on the diameter.

```cpp
vector<int> findCenter(int n) {
    // Find the diameter and its two ends
    // ... (code from above)
    
    int mid = max_dist / 2;
    // Walk mid steps from one end
    // This/these is/are the center(s)
}
```

## 5. Specialized Trees and Techniques

### 5.1. Binary Search Tree (BST)

A BST maintains elements in sorted order. For each vertex:
- All elements in the left subtree are smaller.
- All elements in the right subtree are larger.

```cpp
struct BST {
    int value;
    BST *left, *right;
    
    BST(int val) : value(val), left(nullptr), right(nullptr) {}
};

BST* insert(BST* root, int val) {
    if (!root) return new BST(val);
    
    if (val < root->value) {
        root->left = insert(root->left, val);
    } else {
        root->right = insert(root->right, val);
    }
    return root;
}

bool search(BST* root, int val) {
    if (!root) return false;
    if (root->value == val) return true;
    
    if (val < root->value) return search(root->left, val);
    return search(root->right, val);
}
```

**Note:** Usually, a BST can become unbalanced. For balanced BSTs, see AVL trees, Red-Black trees, or use `set` in C++.

### 5.2. Trie (Prefix Tree)

An efficient structure for working with strings:

```cpp
struct TrieNode {
    map<char, TrieNode*> children;
    bool isEnd = false;
};

class Trie {
    TrieNode* root;
public:
    Trie() { root = new TrieNode(); }
    
    void insert(string word) {
        TrieNode* curr = root;
        for (char c : word) {
            if (!curr->children[c]) {
                curr->children[c] = new TrieNode();
            }
            curr = curr->children[c];
        }
        curr->isEnd = true;
    }
    
    bool search(string word) {
        TrieNode* curr = root;
        for (char c : word) {
            if (!curr->children[c]) return false;
            curr = curr->children[c];
        }
        return curr->isEnd;
    }
};
```

### 5.3. Segment Tree and Fenwick Tree

These trees are used for range queries and updates. Detailed information in Topic 34.

### 5.4. Heavy-Light Decomposition

Divides the tree into "heavy" and "light" paths, allowing efficient queries on paths. Complexity: $O(\log^2 N)$ per query.

### 5.5. Centroid Decomposition

Recursive partitioning of the tree by a centroid (a vertex whose removal makes all components of size ≤ n/2). Used for complex problems with paths in the tree.

## 6. Dynamic Programming on Trees

DP on trees is a powerful technique. Usually, we use post-order traversal:

### 6.1. Example: Maximum Independent Set

Finding the maximum number of vertices such that no two are connected by an edge.

```cpp
int dp[MAXN][2];  // dp[u][0] = not including u, dp[u][1] = including u

void tree_dp(int u, int parent) {
    dp[u][0] = 0;
    dp[u][1] = 1;  // The value of u
    
    for (int v : adj[u]) {
        if (v != parent) {
            tree_dp(v, u);
            
            // If we don't take u, we can either take or not take v
            dp[u][0] += max(dp[v][0], dp[v][1]);
            
            // If we take u, we CANNOT take v
            dp[u][1] += dp[v][0];
        }
    }
}
```

### 6.2. Rerooting technique

Sometimes we want DP results for every possible root. We use rerooting:

```cpp
int down[MAXN];  // DP downwards (towards leaves)
int up_dp[MAXN]; // DP upwards (towards root)

// First calculate down[] with an ordinary DFS
void dfs_down(int u, int parent) {
    down[u] = 0;
    for (int v : adj[u]) {
        if (v != parent) {
            dfs_down(v, u);
            down[u] += down[v] + 1;  // Example
        }
    }
}

// Then calculate up_dp[] with rerooting
void dfs_up(int u, int parent) {
    for (int v : adj[u]) {
        if (v != parent) {
            // up_dp[v] is calculated from up_dp[u] and the down of other children
            up_dp[v] = up_dp[u] + down[u] - down[v] - 1;
            dfs_up(v, u);
        }
    }
}
```

## 7. Euler Tour and Other Techniques

### 7.1. Euler Tour of the Tree

Transforming the tree into an array, allowing the use of range structures:

```cpp
int timer = 0;
int tin[MAXN], tout[MAXN];
int euler[2 * MAXN];  // Euler tour

void euler_tour(int u, int parent) {
    tin[u] = timer;
    euler[timer++] = u;
    
    for (int v : adj[u]) {
        if (v != parent) {
            euler_tour(v, u);
        }
    }
    
    tout[u] = timer;
    euler[timer++] = u;
}
```

Now the subtree of $u$ corresponds to interval $[tin[u], tout[u]]$.

### 7.2. Flattening the Tree

Similar to the Euler tour but simpler - each subtree is a continuous interval:

```cpp
int timer = 0;
int tin[MAXN], tout[MAXN];

void flatten(int u, int parent) {
    tin[u] = timer++;
    for (int v : adj[u]) {
        if (v != parent) flatten(v, u);
    }
    tout[u] = timer - 1;
}
```

## 8. Practice Tasks

### Basic tasks
1.  **CSES Tree Diameter**: Finding the diameter - excellent for starters.
2.  **CSES Subordinates**: Counting subtrees - basic tree DP.
3.  **CSES Tree Distances I**: Distances in a tree - a combination of techniques.

### Intermediate tasks
4.  **CSES Company Queries II**: Finding LCA with binary lifting.
5.  **Codeforces 191C**: Fools and Roads - LCA + differences on a tree.
6.  **CSES Path Queries**: Path queries - Euler tour + range structures.
7.  **CSES Tree Distances II**: Rerooting technique.

### Advanced tasks
8.  **Codeforces 609E**: Minimum Spanning Tree for Each Edge.
9.  **CSES Distinct Colors**: Heavy-Light Decomposition or another advanced technique.
10. **IOI 2011 Race**: Centroid decomposition classical task.

## 9. Practical Tips

### 9.1. Common Errors

1. **Forgetting to check for parent** during DFS in an unrooted tree.
2. **Incorrect initialization** of the binary lifting array.
3. **Off-by-one errors** when calculating depth/height.
4. **Stack overflow** with very deep trees - use iterative DFS or increase stack size.

### 9.2. Optimizations

- In binary lifting, don't forget that `up[0][i] = 0` (or a sentinel value).
- For Euler tour, the array size should be `2*n`.
- In rerooting, be careful with updating values.
- Use `vector<int>` instead of `vector<vector<int>>` for better cache locality.

### 9.3. Debugging Tips

- Visualize small trees (5-7 vertices).
- Check if the root is processed correctly.
- Print the intermediate values of DP.
- Test with a line tree (worst case) and a star tree (special case).

## 🏁 Conclusion

Trees are a fundamental structure in competitive programming and computer science. The basic techniques you must master are:

**Must-know techniques:**
- **Traversals:** DFS (pre/in/post-order) and BFS.
- **LCA:** Binary lifting is the standard.
- **Diameter:** Double DFS or DP.
- **Tree DP:** Post-order traversal for value calculation.

**Advanced techniques:**
- **Rerooting:** For DP with all possible roots.  
- **Euler Tour:** Transforms a tree into an array for range queries.
- **Heavy-Light Decomposition:** For complex path queries.
- **Centroid Decomposition:** For tasks with distances in the tree.

Trees allow many operations to be performed in logarithmic time. Understanding LCA and dynamic programming on trees is critical for advanced competitors. Practice regularly with different types of tasks to develop an intuition for the correct approach!