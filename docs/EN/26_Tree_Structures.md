# 🌲 Tree Data Structures: Expert Level

## 🌳 Segment Tree (Advanced)

### 1. Dynamic Segment Tree
In a standard segment tree, we allocate an array of $4N$. If the range is $[0, 10^9]$, memory will not suffice.
- **Solution**: Create nodes only when needed (lazy creation). Each node keeps pointers/indices to left and right children.
- **Complexity**: $O(Q \log (\text{Range}))$ memory and time.

```cpp
struct Node {
    long long sum = 0;
    Node *left = nullptr, *right = nullptr;
    
    void update(int l, int r, int pos, int val) {
        if (l == r) { sum += val; return; } 
        int mid = l + (r - l) / 2;
        if (pos <= mid) {
            if (!left) left = new Node();
            left->update(l, mid, pos, val);
        } else {
            if (!right) right = new Node();
            right->update(mid + 1, r, pos, val);
        }
        sum = (left ? left->sum : 0) + (right ? right->sum : 0);
    }
};
```

### 2. Segment Tree Beats (Ji Driver Segment Tree)
Allows operations like $A_i = \min(A_i, X)$ for a range.
- Maintain `max`, `second_max`, and `count_max` in each node.
- If $X \ge max$, do nothing.
- If $second\_max < X < max$, update only the maximum.
- Otherwise, recurse down.
- Amortized Complexity: $O((N+Q) \log N)$.

---

## 🌳 Fenwick Tree (BIT) - Extensions

### 1. Range Update, Range Query
Standard BIT supports Point Update, Range Query.
To support Range Update $[L, R]$ with $+Val$:
- Use two BITs: $B_1$ and $B_2$.
- $\text{Sum}(x) = \sum_{i=1}^x A_i$. After adding $v$ in $[L, R]$, the sum for $x \ge R$ increases by $v \cdot (R - L + 1)$.
- Formula: $\text{Query}(x) = \text{sum}(B_1, x) \cdot x - \text{sum}(B_2, x)$.
- Update:
  - `update(B1, L, v)`, `update(B1, R+1, -v)`
  - `update(B2, L, v * (L-1))`, `update(B2, R+1, -v * R)`

### 2. 2D Fenwick Tree
For matrix operations.
```cpp
void update(int x, int y, int delta) {
    for (int i = x; i <= N; i += i & -i)
        for (int j = y; j <= M; j += j & -j)
            bit[i][j] += delta;
}
```
Complexity: $O(\log N \log M)$.

---

## 🏁 Conclusion

Dynamic trees are key for "huge" coordinates. Segment Tree Beats is a relatively new and powerful technique allowing solutions to problems previously thought unsolvable without sqrt-decomposition.

```