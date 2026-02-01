# 🌳 Advanced Data Structures

Efficiently handling range queries on arrays.

## 1. Fenwick Tree (Binary Indexed Tree - BIT)

Best for Prefix Sums with Point Updates.
*   **Time**: $O(\log N)$ update/query.
*   **Space**: $O(N)$.
*   **Code**: Extremely short (~10 lines).

## 2. Segment Tree

The Swiss Army Knife of range queries.
*   Can handle Sum, Min, Max, GCD, Matrix Product, etc.
*   **Range Updates**: Possible via **Lazy Propagation**.
*   **Time**: $O(\log N)$.
*   **Space**: $4N$.

### Implementation Tips
*   Use an array `tree[4*N]`.
*   Left child: `2*v`, Right child: `2*v+1`.
*   Mid: `(tl + tr) / 2`.

## 3. Sparse Table

Static Range Minimum Query (RMQ) in $O(1)$.
*   **Precomputation**: $O(N \log N)$ using DP. `st[i][j]` stores min of range starting at $i$ with length $2^j$.
*   **Query**: Combine two overlapping ranges. $\min(\text{st}[L][k], \text{st}[R - 2^k + 1][k])$.

## 4. Sqrt Decomposition

Divide array into $\sqrt{N}$ blocks.
*   **Point Update**: $O(1)$.
*   **Range Query**: $O(\sqrt{N})$ (sum partial blocks + full blocks).

### Mo's Algorithm
For offline queries. Sort queries to minimize movement of left and right pointers.
Solves hard problems like "count distinct elements in range" in $O(N \sqrt{N})$.

## 5. Policy Based Data Structures (PBDS)

C++ extension (GCC) providing `ordered_set`.
Supports:
*   `find_by_order(k)`: k-th smallest element.
*   `order_of_key(x)`: Number of elements strictly smaller than x.

```cpp
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
typedef tree<int, null_type, less<int>, rb_tree_tag, tree_order_statistics_node_update> ordered_set;
```

## 6. Practice
1.  **CSES Static Range Minimum Queries**: Sparse Table.
2.  **CSES Dynamic Range Sum Queries**: BIT / SegTree.
3.  **SPOJ DQUERY**: Distinct Query (Mo's Algo).

```