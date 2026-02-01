# 🌳 Advanced Data Structures: Range Queries and Updates

In competitive programming, many problems require processing queries on an array (e.g., sum, minimum, or maximum in a range $[L, R]$). While naive solutions take $O(N)$ per query, advanced structures achieve $O(\log N)$ or $O(1)$.

---

## 1. Fenwick Tree (Binary Indexed Tree - BIT)

The BIT is an extremely compact structure for range sum queries and point updates.

*   **Update ($O(\log N)$)**: `add(idx, val)`
*   **Query ($O(\log N)$)**: `sum(idx)` (prefix sum up to `idx`)

```cpp
int bit[MAXN];
int n;

void add(int idx, int val) {
    for (++idx; idx <= n; idx += idx & -idx)
        bit[idx] += val;
}

int sum(int idx) {
    int res = 0;
    for (++idx; idx > 0; idx -= idx & -idx)
        res += bit[idx];
    return res;
}
```

---

## 2. Segment Tree

The Segment Tree is the most versatile data structure for range queries. It can handle any associative operation (Sum, Min, Max, GCD, Matrix Multiplication).

### 2.1. Basic Implementation (Point Update)
*   **Space**: $4N$.
*   **Time**: $O(\log N)$ for both update and query.

### 2.2. Lazy Propagation (Range Updates)
Allows updating an entire range $[L, R]$ in $O(\log N)$.
*   **Concept**: Instead of updating all nodes in the range, we store a "lazy" tag at the highest possible nodes and push it down only when necessary.

---

## 3. Sparse Table (RMQ)

Sparse Table is a static data structure used for Range Minimum Query (RMQ) in $O(1)$ time with $O(N \log N)$ precomputation.

*   **Logic**: It stores the results for all intervals of length $2^k$.
*   **Overlap**: Since $\min(a, b, c) = \min(\min(a, b), \min(b, c))$, we can cover any range with two overlapping power-of-2 intervals.

```cpp
int st[MAXN][20];
void build(int n, int a[]) {
    for (int i = 0; i < n; i++) st[i][0] = a[i];
    for (int j = 1; j < 20; j++)
        for (int i = 0; i + (1 << j) <= n; i++)
            st[i][j] = min(st[i][j-1], st[i + (1 << (j-1))][j-1]);
}
int query(int L, int R) {
    int j = 31 - __builtin_clz(R - L + 1); // log2
    return min(st[L][j], st[R - (1 << j) + 1][j]);
}
```

---

## 4. Mo's Algorithm (Sqrt Decomposition)

A technique for **offline** range queries (where all queries are known in advance).
1.  Divide the array into blocks of size $\sqrt{N}$.
2.  Sort queries based on block index of the left pointer, then by the right pointer.
3.  Move the range pointers $[L, R]$ from one query to the next. Total movement is $O((N+Q)\sqrt{N})$.

---

## 5. Policy Based Data Structures (PBDS)

The GCC compiler provides a built-in "Ordered Set" that supports finding the $k$-th smallest element and the rank of an element in $O(\log N)$.

```cpp
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
typedef tree<int, null_type, less<int>, rb_tree_tag, tree_order_statistics_node_update> ordered_set;

ordered_set s;
s.insert(10);
s.order_of_key(11); // Rank of 11 (number of elements < 11)
s.find_by_order(0); // Iterator to the 0-th smallest element (10)
```

---

## 6. Practice Problems
1.  **CSES Range Queries**: The entire section.
2.  **SPOJ GSS1/GSS3**: Range maximum sum subsegment.
3.  **Codeforces 86D**: Mo's Algorithm example.
4.  **UVa 11423**: Cache Simulation (BIT application).

## 7. Conclusion
Range queries are the bread and butter of intermediate competitive programming. Master the BIT for its simplicity, and the Segment Tree for its immense power.
