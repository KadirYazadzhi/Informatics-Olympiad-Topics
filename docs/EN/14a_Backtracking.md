# 🔙 Backtracking

Backtracking is an algorithmic technique for solving problems recursively by trying to build a solution incrementally, one piece at a time, removing those solutions that fail to satisfy the constraints of the problem at any point of time.

## 1. General Template

It is essentially a DFS traversal of the state space tree.

```cpp
void backtrack(State current) {
    if (isSolution(current)) {
        processSolution(current);
        return;
    }

    for (Option opt : getOptions(current)) {
        if (isValid(current, opt)) {
            makeMove(current, opt); // Apply change
            backtrack(current);     // Recurse
            undoMove(current, opt); // Backtrack (Revert change)
        }
    }
}
```

## 2. Classic Problems

### 2.1. Generating Permutations
Generate all orderings of numbers $1 \dots N$. Time complexity $O(N!)$.

```cpp
int n;
vector<int> p;
bool used[20];

void search() {
    if (p.size() == n) {
        // Print p
        return;
    }
    for (int i = 1; i <= n; i++) {
        if (!used[i]) {
            used[i] = true;
            p.push_back(i);
            search();
            p.pop_back(); // Backtrack
            used[i] = false;
        }
    }
}
```

### 2.2. N-Queens Problem
Place $N$ queens on an $N \times N$ board so no two queens attack each other.
Optimization: Use boolean arrays to check columns and diagonals in $O(1)$.
*   `diag1[r+c]` tracks main diagonals.
*   `diag2[r-c+n]` tracks anti-diagonals.

```cpp
void solve(int r) {
    if (r == n) { count++; return; }
    for (int c = 0; c < n; c++) {
        if (!col[c] && !d1[r+c] && !d2[r-c+n]) {
            col[c] = d1[r+c] = d2[r-c+n] = true;
            solve(r + 1);
            col[c] = d1[r+c] = d2[r-c+n] = false; // Backtrack
        }
    }
}
```

### 2.3. Generating Subsets
For each element, decide whether to include it or not. Time complexity $O(2^N)$.

## 3. Pruning

Pruning is the art of cutting off branches of the recursion tree that cannot lead to a valid (or optimal) solution. This dramatically improves performance.
*   **Feasibility Pruning**: Stop if constraints are violated (e.g., sum exceeds target).
*   **Optimality Pruning**: Stop if current cost > best solution found so far (Branch and Bound).

## 4. Complexity Analysis
*   Permutations: $O(N!)$
*   Subsets: $O(2^N)$
*   Partitioning: $O(K^N)$

Use Backtracking only when $N$ is small (typically $N \le 20$).

## 5. Practice Problems
1.  **Sudoku Solver**: Fill a 9x9 grid.
2.  **UVa 524**: Prime Ring Problem.
3.  **LeetCode 37**: Sudoku Solver.
4.  **CSES Apple Division**: Partition numbers into two sets with minimal difference.

## 6. Conclusion
The key to Backtracking is the **Undo** step. Always revert the state to exactly how it was before the recursive call.

```