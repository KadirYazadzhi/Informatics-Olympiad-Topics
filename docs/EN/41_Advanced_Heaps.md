# 🧱 Advanced Heaps

The standard "Binary Heap" structure, used in `std::priority_queue`, is extremely efficient for the `push` ($O(\log N)$) and `pop` ($O(\log N)$) operations. However, it has one significant drawback: merging two heaps takes $O(N)$ time.

In many algorithms (e.g., Minimum Spanning Tree with the Chu-Liu/Edmonds algorithm, Dijkstra on special graphs), efficient merging of queues is required. This is where **Mergeable Heaps** come to the rescue.

---\n

## 1. Leftist Heap

A leftist heap is a binary tree that satisfies two properties:
1.  **Heap Property**: The value in each node is smaller (or larger, for max-heap) than the values of its children.
2.  **Leftist Property**: For each node, the "distance" to the nearest `null` descendant in the right subtree is less than or equal to that in the left.
    *   Let $dist(u)$ be the length of the shortest path from $u$ to a node that does not have two children.
    *   Requirement: $dist(u \to left) \ge dist(u \to right)$.

This property guarantees that the right path (Right Spine) of the tree is short: its length is at most $O(\log N)$. Since most operations are performed along the right path, they are logarithmic.

### 1.1. Merge Operation
This is the basic operation. All others (insert, pop) are defined through it.
To merge two heaps $A$ and $B$:
1.  If one is empty, return the other.
2.  Let the root of $A$ be smaller than the root of $B$ (for min-heap). If not, swap $A$ and $B$.
3.  Merge the right child of $A$ with $B$: `A->right = merge(A->right, B)`.
4.  After merging, if $dist(A \to left) < dist(A \to right)$, swap the left and right children of $A$ to restore the leftist property.
5.  Update $dist(A) = dist(A \to right) + 1$.

Complexity: $O(\log N)$.

```cpp
struct Node {
    int val, dist;
    Node *left, *right;
    Node(int v) : val(v), dist(0), left(nullptr), right(nullptr) {}
};

Node* merge(Node* a, Node* b) {
    if (!a) return b;
    if (!b) return a;
    if (a->val > b->val) swap(a, b); // Min-heap
    
    a->right = merge(a->right, b);
    
    if (!a->left || a->left->dist < a->right->dist) {
        swap(a->left, a->right);
    }
    
    if (a->right) a->dist = a->right->dist + 1;
    else a->dist = 0;
    
    return a;
}
```

---\n

## 2. Skew Heap

A Skew Heap is a simplified version of a Leftist Heap.
*   **Idea**: It is not necessary to store and maintain the exact value of $dist$. Instead, we **always** swap the left and right children after merging.
*   This is a self-adjusting structure (similar to Splay trees).
*   There is no guarantee of logarithmic height in the worst case ($O(N)$), but the **amortized** complexity is $O(\log N)$.

### 2.1. Implementation
The code is extremely short and elegant:

```cpp
struct SkewNode {
    int val;
    SkewNode *left, *right;
    SkewNode(int v) : val(v), left(nullptr), right(nullptr) {}
};

SkewNode* skew_merge(SkewNode* a, SkewNode* b) {
    if (!a) return b;
    if (!b) return a;
    if (a->val > b->val) swap(a, b);
    
    // Merge the right tree with b
    a->right = skew_merge(a->right, b);
    
    // Always swap the children (this ensures amortized balance)
    swap(a->left, a->right);
    
    return a;
}
```
This is the recommended structure for competitions if you need a mergeable heap, due to its simplicity.

---\n

## 3. Binomial Heap

A binomial heap is a collection of **binomial trees**.
*   A binomial tree of order $k$ ($B_k$) is defined recursively:
    *   $B_0$ is a single node.
    *   $B_k$ is obtained from two $B_{k-1}$ trees, with the root of one becoming a child of the root of the other.
*   There are exactly $2^k$ nodes.
*   Each heap is represented as a sum of unique binomial trees (similar to the binary representation of the number of elements).

The `merge` operation resembles binary addition. If both heaps have a tree of order $k$, they are merged into a tree of order $k+1$.
Complexity: $O(\log N)$.
Binomial heaps are the foundation for the more complex **Fibonacci Heaps**, which achieve $O(1)$ for the `decrease_key` operation.

---\n

## 4. Treap as a Heap

Since a Treap (Cartesian Tree) supports `split` and `merge` operations in $O(\log N)$, it can easily be used as a priority queue.
*   We use values as keys for the BST structure.
*   Priorities (random numbers) maintain balance.
*   `merge` of two trees requires all keys in one to be smaller than those in the other (which is not always true for arbitrary heaps), so a standard Treap is not a direct replacement for a Mergeable Heap for arbitrary data unless modified.

However, a **Randomized Meldable Heap** is a variation where during the merge of two nodes, the decision of which becomes the root (if values are equal or close) or in which subtree to continue is made randomly. This works very similarly to a Skew Heap, but with randomness instead of unconditional swapping.

---\n

## 5. Comparison and Application

| Structure | Insert | Pop Min | Merge | Decrease Key | Code Complexity |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Binary Heap** | $O(\log N)$ | $O(\log N)$ | $O(N)$ | $O(\log N)$ | Low (STL) |
| **Leftist Heap** | $O(\log N)$ | $O(\log N)$ | $O(\log N)$ | $O(\log N)$ | Medium |
| **Skew Heap** | $O(\log N)^*$ | $O(\log N)^*$ | $O(\log N)^*$ | $O(\log N)^*$ | Low |
| **Fibonacci** | $O(1)$ | $O(\log N)$ | $O(1)$ | $O(1)$ | Very High |

*$^*$ Amortized complexity.*

**When to use a Mergeable Heap?**
1.  In tasks requiring merging of sets and finding the minimum in each (e.g., "Monkey King" in SPOJ).
2.  In optimized graph algorithms.

For competitive purposes: **Skew Heap** is the best balance between coding speed and performance.

---\n

## 6. Fibonacci Heap

The Fibonacci heap is the most advanced priority queue structure, achieving:
*   `insert`: $O(1)$ amortized.
*   `find_min`: $O(1)$.
*   `decrease_key`: $O(1)$ amortized.
*   `delete_min`: $O(\log N)$ amortized.
*   `merge`: $O(1)$.

### 6.1. Why is it important?
It is used in:
*   **Dijkstra's algorithm**: With a Fibonacci heap, complexity becomes $O(E + V \log V)$ instead of $O((E + V) \log V)$.
*   **Prim's algorithm** for MST: Same optimization.

### 6.2. Problem with practical application
Despite its theoretical elegance, the implementation is very complex and has a large constant factor. In practice, an ordinary Binary Heap or a Pairing Heap is faster for real data.

**Conclusion**: For competitions, a Fibonacci heap is not worth the time for implementation.

---\n

## 7. Pairing Heap

The Pairing Heap is a simpler alternative to the Fibonacci Heap:
*   Almost the same amortized complexity.
*   Much easier to implement.
*   In practice, often faster than a Fibonacci Heap.

### Basic Idea
*   Each node can have an arbitrary number of children (not just 2).
*   The `merge` operation is very simple: connect the root with the smaller value to the other.
*   During `delete_min`, all children are merged anew (two-pass pairing).

```cpp
struct PairingNode {
    int val;
    PairingNode* child;
    PairingNode* sibling;
    
    PairingNode(int v) : val(v), child(nullptr), sibling(nullptr) {}
};

PairingNode* merge(PairingNode* a, PairingNode* b) {
    if (!a) return b;
    if (!b) return a;
    
    if (a->val > b->val) swap(a, b);
    
    b->sibling = a->child;
    a->child = b;
    return a;
}
```

---\n

## 8. Practical Tips

### 8.1. When to use an ordinary Binary Heap?
*   For 95% of tasks - use `std::priority_queue`.
*   Easy implementation, fast in practice, STL optimized.

### 8.2. When to use Skew/Leftist Heap?
*   When **merge** is a critical operation.
*   Examples: Union-Find with priorities, tree algorithms.

### 8.3. When to use Fibonacci/Pairing Heap?
*   If you are implementing Dijkstra/Prim and the edges are **much more** than the vertices ($E >> V \log V$).
*   If you have a ready library (boost::heap in C++).

---\n

## 9. Practice Tasks

1.  **SPOJ RMQSQ**: Range Minimum Query (can be solved with Segment Tree, but also with Heap as an alternative).
2.  **Codeforces 643D**: A problem with merging sets.
3.  **POJ 3784**: Running Median (two heaps - max and min).
4.  **SPOJ KQUERY**: K-th element queries.

---\n

## 🏁 Conclusion

Understanding the different types of heaps gives you flexibility in solving complex tasks. Key points:

*   **Binary Heap**: Your basic tool (via `priority_queue`).
*   **Skew Heap**: The simplest mergeable heap - remember the code!
*   **Leftist Heap**: Slightly more complex, but with guaranteed logarithmic complexity.
*   **Fibonacci Heap**: Theoretically perfect, practically rarely used.

Recommendation: Prepare a template for a Skew Heap and test it in advance. In a competition, just copy it when needed.

Mastering priority queues is critical for algorithms like Dijkstra, Prim, Huffman coding, and many dynamic programming tasks!
