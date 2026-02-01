# 🧠 Advanced Dynamic Programming: Optimizations and Structures

Dynamic Programming (DP) is often the key to solving complex optimization problems. Once you master the basics (Knapsack, LCS), the next level involves optimizing transitions and handling larger state spaces.

---

## 1. Tree DP

Trees are recursive by nature, making them perfect for DP.

### 1.1. Core Concept
Compute $DP[u]$ using values from $DP[v_1], DP[v_2], \dots$ where $v_i$ are the children of $u$ in a rooted tree.

### 1.2. Rerooting Technique
If you need the DP answer for every node as if it were the root:
1.  **First DFS**: Compute results for an arbitrary root (e.g., node 1).
2.  **Second DFS**: Transition from $DP(parent)$ to $DP(child)$ by "removing" the child's subtree from the parent and "adding" the rest of the tree as a new child of the current child.

---

## 2. Bitmask DP

Used when the state involves subsets of a small set ($N \le 20$).
*   **State**: $DP[mask]$ where `mask` is an integer bitmask.
*   **TSP (Traveling Salesperson)**: $DP[mask][u]$ is the min cost to visit nodes in `mask` ending at $u$. Complexity: $O(2^N \cdot N^2)$.

---

### 3. Transition Optimizations

Many DP relations look like: $DP[i] = \min_{j < i} \{ DP[j] + Cost(j, i) \}$.

### 3.1. Convex Hull Trick (CHT)
If $Cost(j, i) = m_j \cdot x_i + c_j$, each $j$ defines a line $y = mx + c$.
We need to find the minimum $y$ for a given $x_i$.
*   **Static**: If slopes are monotonic, use a Deque ($O(N)$).
*   **Dynamic**: Use Li Chao Tree or `std::set` with line intersections ($O(N \log N)$).

### 3.2. Divide and Conquer Optimization
Applicable if $opt[i] \le opt[i+1]$, where $opt[i]$ is the index $j$ that minimizes $DP[i]$.
Complexity reduces from $O(N^2)$ to $O(N \log N)$.

### 3.3. Knuth Optimization
Applicable for interval DP ($DP[i][j]$) if the optimal splitting point $opt[i][j]$ satisfies:
$$opt[i][j-1] \le opt[i][j] \le opt[i+1][j]$$
Complexity: $O(N^2)$ instead of $O(N^3)$.

---

## 4. SOS DP (Sum Over Subsets)

Efficiently calculates $F[mask] = \sum_{sub \subseteq mask} A[sub]$ for all masks.
Instead of $O(3^N)$, SOS DP takes $O(N \cdot 2^N)$.

```cpp
for(int i = 0; i < N; ++i) {
    for(int mask = 0; mask < (1<<N); ++mask) {
        if(mask & (1<<i))
            dp[mask] += dp[mask^(1<<i)];
    }
}
```

---

## 5. Matrix Exponentiation

For linear recurrences where $N$ is massive ($10^{18}$).
If $DP_n = a \cdot DP_{n-1} + b \cdot DP_{n-2}$, we use:
$$\begin{pmatrix} DP_n \\ DP_{n-1} \end{pmatrix} = \begin{pmatrix} a & b \\ 1 & 0 \end{pmatrix} \begin{pmatrix} DP_{n-1} \\ DP_{n-2} \end{pmatrix}$$
Raise the transition matrix to the power $n$.

---

## 6. Digit DP

Used for counting numbers in range $[L, R]$ with specific digit properties (e.g., "sum of digits is prime").
*   State: `(index, isLess, isStarted, current_property)`.

---

## 7. Practice Problems
1.  **CSES Tree Distances I & II**: Rerooting.
2.  **CSES Tree Matching**: Tree DP.
3.  **Codeforces 319C**: Kalila and Dimna (CHT).
4.  **CSES Counting Tilings**: Broken Profile DP.

## 8. Conclusion
Advanced DP is about recognizing patterns. Whether it's a tree structure, a subset problem, or a linear recurrence, there's likely an optimization to bring the complexity down to acceptable limits.
