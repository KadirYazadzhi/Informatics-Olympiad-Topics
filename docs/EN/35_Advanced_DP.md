# 🧠 Advanced Dynamic Programming

Techniques to optimize DP transitions and handle complex state spaces.

## 1. Tree DP

Solving problems on trees using DFS.

### 1.1. Rerooting Technique
Compute DP for **every node as the root** in $O(N)$.
1.  **Bottom-up**: Compute answers for a fixed root (e.g., node 1).
2.  **Top-down**: Move the root to adjacent nodes, updating values in $O(1)$ (removing old subtree, adding new parent).

## 2. Broken Profile DP (DP on Subsets/Profile)

Used for tiling problems on grids ($N \times M$ where $N$ is small).
The state is a bitmask representing the boundary between processed and unprocessed cells.
Complexity: $O(M \cdot 2^N)$.

## 3. Transition Optimizations

Given $dp[i] = \min_{j < i} \{ dp[j] + cost(j, i) \}$.

### 3.1. Convex Hull Trick (CHT)
If $cost(j, i) = b_j \cdot a_i + c_j$, each $j$ is a line $y = mx + c$.
We query the minimum value at $x = a_i$.
*   Use a Deque if lines are added in sorted order of slope.
*   Use Li Chao Tree or `std::set` (dynamic hull) otherwise.
Reduces $O(N^2)$ to $O(N)$ or $O(N \log N)$.

### 3.2. Divide and Conquer Optimization
Applicable if $opt[i] \le opt[i+1]$.
Compute $dp[mid]$ by checking all valid $j$. Let optimal $j$ be $k$.
Then for $i < mid$, search $j \in [L, k]$. For $i > mid$, search $j \in [k, R]$.
Reduces $O(N^2)$ to $O(N \log N)$.

## 4. SOS DP (Sum Over Subsets)

Computes sum of values for all submasks of a mask.
$F[mask] = \sum_{i \subseteq mask} A[i]$.

Naive iteration is $O(3^N)$. SOS DP does it in $O(N \cdot 2^N)$ by iterating bit by bit.

```cpp
for (int i = 0; i < N; ++i) {
    for (int mask = 0; mask < (1<<N); ++mask) {
        if (mask & (1<<i))
            dp[mask] += dp[mask ^ (1<<i)];
    }
}
```

## 5. Matrix Exponentiation
For linear recurrences (like Fibonacci) with huge $N$ ($10^{18}$).
Construct a transition matrix $M$.
State vector $V_n = M \times V_{n-1} \implies V_n = M^n \times V_0$.
Compute $M^n$ in $O(K^3 \log N)$.

## 6. Practice
1.  **Codeforces 1083A**: Maximum Path Sum (Tree DP + CHT).
2.  **SPOJ NKLEAVES**: Leaves (Convex Hull Trick).
3.  **HackerRank**: Kingdom Division (Tree DP).

## 7. Conclusion
Before implementing $O(N^2)$ DP, always check if the cost function satisfies properties like the Quadrangle Inequality or linearity, which allow optimizations.