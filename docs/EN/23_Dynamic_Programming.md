# 🔄 Dynamic Programming: Advanced Techniques

## 🔢 Digit DP

Used for problems like "How many numbers in the range $[A, B]$ satisfy condition $X$?".
We solve the problem for $[0, B]$ and subtract the result for $[0, A-1]$.

**State**: `dp(index, count, isLess, isStarted)`
- `index`: Which digit we are considering (left to right).
- `count`: The parameter we track (e.g., sum of digits, count of digit 7, etc.).
- `isLess`: Boolean flag. `true` if the number we are building is already strictly less than the prefix of $N$. If `true`, we can place any digit (0-9). If `false`, we are limited to $N[index]$.
- `isStarted`: To handle leading zeros.

**Template:**
```cpp
long long memo[20][200][2];
string S;

long long solve(int idx, int sum, bool isLess) {
    if (idx == S.size()) return sum; // Base case
    if (memo[idx][sum][isLess] != -1) return memo[idx][sum][isLess];

    long long ans = 0;
    int limit = isLess ? 9 : (S[idx] - '0');

    for (int digit = 0; digit <= limit; digit++) {
        bool nextIsLess = isLess || (digit < limit);
        ans += solve(idx + 1, sum + digit, nextIsLess);
    }
    return memo[idx][sum][isLess] = ans;
}
```

---

## 🌳 Tree DP

Problems on trees are often solved with DP, where the state for node $u$ depends on its children.

**Example: Maximum Independent Set**
Return the maximum number of vertices that are not adjacent.
- `dp[u][0]`: Max for subtree of $u$, if $u$ is **not** chosen.
  - $\sum \max(dp[v][0], dp[v][1])$ for all children $v$.
- `dp[u][1]`: Max for subtree, if $u$ **is** chosen.
  - $1 + \sum dp[v][0]$ for all children $v$ (children cannot be chosen).

---

## 📏 Hirschberg's Algorithm

To find the **LCS itself** (Longest Common Subsequence), not just its length, standard DP requires $O(N \cdot M)$ memory. Hirschberg allows path reconstruction in $O(N \cdot M)$ time and only **$O(\min(N, M))$ memory**.
- Uses Divide and Conquer, finding the midpoint of the optimal path and solving recursively for both halves.

---

## 🎒 Knapsack - Variations

1. **Unbounded Knapsack**: Each item can be used multiple times.
   - $dp[w] = \max(dp[w], dp[w - weight[i]] + value[i])$ (loop goes bottom-up).
2. **Limited Knapsack**: Each item has $C_i$ copies.
   - Optimization: Decompose $C_i$ into powers of two $(1, 2, 4, \dots)$ and reduce to 0/1 Knapsack ($O(N \cdot W \cdot \log C)$).

---

## 🕸️ Broken Profile DP

Used for tiling grids $N \times M$ (where $N$ is small).
We build the grid cell by cell. The profile is the boundary between processed and unprocessed cells.
- If moving row by row, the profile is the state of the current row (bitmask).
- If moving cell by cell, the profile includes $N$ bits (parts of current and next row).

---

## 🏁 Conclusion

Dynamic programming is an art. The most important step is to define a state that contains **enough** information for future decisions but is **minimal** in size. Always check for overlapping subproblems!
