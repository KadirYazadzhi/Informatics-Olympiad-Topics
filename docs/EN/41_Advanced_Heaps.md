# 🧱 Advanced Heaps: Mergeable Heaps

Standard `std::priority_queue` (Binary Heap) is great for push/pop ($O(\log N)$), but merging two heaps takes $O(N)$.
**Mergeable Heaps** allow merging in $O(\log N)$.

## 1. Leftist Heap

A binary tree that maintains the **Heap Property** and the **Leftist Property**.
*   **Dist**: Distance to the nearest null node.
*   **Property**: $dist(left) \ge dist(right)$. This forces the tree to lean to the left.
*   The right spine is short ($O(\log N)$).
*   **Merge**: Recursively merge the right spines. Swap children if property is violated.

```cpp
struct Node {
    int val, dist;
    Node *left, *right;
    Node(int v) : val(v), dist(0), left(0), right(0) {}
};

Node* merge(Node* a, Node* b) {
    if (!a) return b;
    if (!b) return a;
    if (a->val > b->val) swap(a, b); // Min-heap
    
    a->right = merge(a->right, b);
    
    if (!a->left || a->left->dist < a->right->dist)
        swap(a->left, a->right);
        
    a->dist = (a->right ? a->right->dist : 0) + 1;
    return a;
}
```

## 2. Skew Heap

A self-adjusting version of Leftist Heap.
*   No `dist` value needed.
*   **Always swap** left and right children after merging.
*   Amortized complexity $O(\log N)$.
*   Very simple to implement.

```cpp
Node* skew_merge(Node* a, Node* b) {
    if (!a) return b;
    if (!b) return a;
    if (a->val > b->val) swap(a, b);
    
    a->right = skew_merge(a->right, b);
    swap(a->left, a->right);
    return a;
}
```

## 3. Binomial Heap

A collection of Binomial Trees.
*   Supports merge in $O(\log N)$.
*   Structure similar to binary representation of the size.

## 4. Randomized Meldable Heap (Treap)

A Treap can act as a mergeable heap.
*   Use values as keys.
*   Priorities maintain balance.
*   Merge by splitting/joining based on random priorities.

## 5. Applications
1.  **Chu-Liu/Edmonds Algorithm**: MST in directed graphs.
2.  **Shortest Path**: Dijkstra with special constraints.
3.  **Graph Contraction**: Merging sets of nodes efficiently.

## 6. Conclusion
For contests, **Skew Heap** is the best choice due to its simplicity and efficiency.