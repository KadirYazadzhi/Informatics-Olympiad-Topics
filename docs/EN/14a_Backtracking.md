# 🔙 Backtracking

## 📚 Introduction

Backtracking is an algorithmic technique for solving problems via exhaustive search (brute-force) that builds the solution step by step and "goes back" (backtracks) as soon as it determines that the current path does not lead to a valid solution.

The word "backtrack" means returning along the tracks. Imagine you are in a maze - you walk forward until you reach a dead end, then you return to the last fork and try another path. This is the essence of backtracking.

The technique is especially powerful for tasks where we need to find all possible solutions or to check whether a solution satisfying certain constraints exists.

---

## 1. General Scheme

Backtracking is essentially a Depth-First Search (DFS) in the state tree. Each state represents a partial solution, and transitions between states are valid moves.

### 1.1. Basic Template

```cpp
void backtrack(State current) {
    if (isSolution(current)) {
        printSolution(current);
        return;
    }

    for (Option opt : getOptions(current)) {
        if (isValid(current, opt)) {
            makeMove(current, opt); // Forward
            backtrack(current);     // Recursion
            undoMove(current, opt); // Back (Backtrack)
        }
    }
}
```

### 1.2. Key Components

**State:** Represents the current configuration of the problem. It can include:
- Partial solution
- Resources used
- Current position in the decision tree

**Validation (isValid):** Checks whether a given move is permissible in the current state. This is critical for efficiency - the earlier we reject invalid paths, the faster the algorithm will run.

**Solution (isSolution):** Determines whether we have reached a valid solution to the problem.

**Moves (makeMove/undoMove):** Apply and cancel changes in the state. It is important that `undoMove` restores the state exactly as it was before `makeMove`.

---

## 2. Classical Examples

### 2.1. Generating Permutations
Find all orderings of the numbers from 1 to N.

```cpp
int n;
vector<int> p;
bool used[20];

void permutations() {
    if (p.size() == n) {
        for (int x : p) cout << x << " ";
        cout << endl;
        return;
    }
    for (int i = 1; i <= n; i++) {
        if (!used[i]) {
            used[i] = true;
            p.push_back(i);
            permutations();
            p.pop_back(); // Backtrack
            used[i] = false;
        }
    }
}
```

### 2.2. The N-Queens Problem
Arrange $N$ queens on an $N \times N$ board so that they do not attack each other.
*   We use the arrays `col`, `diag1`, `diag2` to track occupied lines in $O(1)$.
*   `diag1`: $row + col = const$
*   `diag2`: $row - col + N = const$

```cpp
int n, ans = 0;
bool col[20], d1[40], d2[40];

void solve(int r) {
    if (r == n) {
        ans++;
        return;
    }
    for (int c = 0; c < n; c++) {
        if (!col[c] && !d1[r + c] && !d2[r - c + n]) {
            col[c] = d1[r + c] = d2[r - c + n] = true;
            solve(r + 1);
            col[c] = d1[r + c] = d2[r - c + n] = false; // Backtrack
        }
    }
}
```

### 2.3. Generating Subsets
For each element, we have two choices: to take it or not to take it.
Complexity: $O(2^N)$.

```cpp
int n;
vector<int> arr;
vector<int> current;

void generateSubsets(int idx) {
    if (idx == n) {
        // Printing the current subset
        for (int x : current) {
            cout << x << " ";
        }
        cout << endl;
        return;
    }
    
    // Include arr[idx]
    current.push_back(arr[idx]);
    generateSubsets(idx + 1);
    current.pop_back();  // Backtrack
    
    // Do not include arr[idx]
    generateSubsets(idx + 1);
}
```

### 2.4. Sudoku Solver

Filling a 9×9 board with digits from 1 to 9 so that in every row, column, and 3×3 square there are no repetitions.

```cpp
bool isValid(int board[9][9], int row, int col, int num) {
    // Row check
    for (int c = 0; c < 9; c++) {
        if (board[row][c] == num) return false;
    }
    
    // Column check
    for (int r = 0; r < 9; r++) {
        if (board[r][col] == num) return false;
    }
    
    // 3×3 square check
    int startRow = (row / 3) * 3;
    int startCol = (col / 3) * 3;
    for (int r = 0; r < 3; r++) {
        for (int c = 0; c < 3; c++) {
            if (board[startRow + r][startCol + c] == num) {
                return false;
            }
        }
    }
    
    return true;
}

bool solveSudoku(int board[9][9]) {
    // Find the next empty cell
    int row = -1, col = -1;
    for (int r = 0; r < 9 && row == -1; r++) {
        for (int c = 0; c < 9; c++) {
            if (board[r][c] == 0) {
                row = r;
                col = c;
                break;
            }
        }
    }
    
    // If there are no empty cells, the solution is ready
    if (row == -1) return true;
    
    // Try digits from 1 to 9
    for (int num = 1; num <= 9; num++) {
        if (isValid(board, row, col, num)) {
            board[row][col] = num;  // Place the number
            
            if (solveSudoku(board)) {
                return true;  // Solution found
            }
            
            board[row][col] = 0;  // Backtrack
        }
    }
    
    return false;  // No solution with the current configuration
}
```

### 2.5. Generating Combinations

Choosing $k$ elements from $n$ elements.

```cpp
int n, k;
vector<int> combination;

void generateCombinations(int start) {
    if (combination.size() == k) {
        // Printing the combination
        for (int x : combination) {
            cout << x << " ";
        }
        cout << endl;
        return;
    }
    
    for (int i = start; i <= n; i++) {
        combination.push_back(i);
        generateCombinations(i + 1);  // The next element is larger
        combination.pop_back();  // Backtrack
    }
}
```

---

## 3. Pruning

The most important part of backtracking is optimization. If we are sure that the current branch will not yield a solution (or a better one than already found), we stop immediately.

### 3.1. Types of Pruning

**Feasibility Pruning:** We stop when a solution is impossible.
*   Example: In the knapsack problem, if the current weight exceeds the capacity, we backtrack.

**Optimality Pruning:** We stop when we cannot achieve a better solution than the one already found.
*   Example: In TSP (Traveling Salesperson Problem), if the current path is already longer than the best found so far, we stop.

**Bound Pruning:** We use a lower/upper bound to decide whether it makes sense to continue.
*   Example: In the knapsack problem, if even by taking all remaining items (relaxation), we cannot improve the current maximum, we stop.

### 3.2. Example: Knapsack problem with backtracking

```cpp
int n, capacity;
int weight[100], value[100];
int bestValue = 0;

void knapsack(int idx, int currentWeight, int currentValue) {
    if (currentWeight > capacity) return;  // Feasibility pruning
    
    if (idx == n) {
        bestValue = max(bestValue, currentValue);
        return;
    }
    
    // Optimality pruning: check with relaxation
    int remainingValue = 0;
    for (int i = idx; i < n; i++) {
        remainingValue += value[i];
    }
    if (currentValue + remainingValue <= bestValue) {
        return;  // We cannot achieve a better solution
    }
    
    // Take the item
    knapsack(idx + 1, currentWeight + weight[idx], currentValue + value[idx]);
    
    // Do not take the item
    knapsack(idx + 1, currentWeight, currentValue);
}
```

### 3.3. Heuristics

The order in which we explore solutions can dramatically influence performance. Good heuristics find the solution earlier.

**Example: Variable Selection in Sudoku**
Instead of traversing the cells in order, we choose the cell with the fewest possible values (Most Constrained Variable).

```cpp
pair<int, int> findBestCell(int board[9][9]) {
    int minOptions = 10;
    pair<int, int> bestCell = {-1, -1};
    
    for (int r = 0; r < 9; r++) {
        for (int c = 0; c < 9; c++) {
            if (board[r][c] != 0) continue;
            
            int options = 0;
            for (int num = 1; num <= 9; num++) {
                if (isValid(board, r, c, num)) options++;
            }
            
            if (options < minOptions) {
                minOptions = options;
                bestCell = {r, c};
            }
        }
    }
    
    return bestCell;
}
```

---

## 4. Complexity

Backtracking algorithms often have exponential complexity ($O(N!)$, $O(2^N)$, $O(K^N)$).
*   They are suitable for $N \le 20$ (for subsets) or $N \le 10$ (for permutations).
*   If $N$ is larger, search for Dynamic Programming or Greedy algorithms.

### 4.1. Complexity Analysis

**Permutations:** 
- Number of nodes: $1 + n + n(n-1) + n(n-1)(n-2) + \dots + n!$
- Approximately $O(n \cdot n!)$

**Subsets:**
- Number of nodes: $2^n$ (each element - take/do not take)
- Total time: $O(2^n \cdot T)$ where $T$ is the processing time for a single node.

**N-Queens:**
- Worst case: $O(n^n)$
- With pruning in practice: much faster.

### 4.2. Practical Constraints

| Task Type | Maximum N | Time (approximate) |
|-----------|-----------|-------------------|
| Permutations | 10-11 | ~1 second |
| Subsets | 20-25 | ~1 second |
| N-Queens | 15-20 | ~1 second (with good pruning) |
| Sudoku 9×9 | Fixed | Usually fast with heuristics |

---

## 5. Backtracking vs. Dynamic Programming

### When to use Backtracking?
- We need to find ALL solutions.
- The problem does not have optimal substructure.
- Small constraints ($N \leq 20$).
- There are good pruning possibilities.

### When to use DP?
- We seek ONE optimal solution (or the count of solutions).
- There are overlapping subproblems.
- We can define a clear state.
- Larger constraints.

### Example: Subset Sum
- **Backtracking:** Generates all subsets and checks the sums ($O(2^n)$ without memory).
- **DP:** Stores which sums are reachable ($O(n \cdot sum)$ with memory).


---

## 5. Tasks
1.  **UVa 750**: 8 Queens Chess Problem.
2.  **Sudoku Solver**: Classical backtracking.
3.  **Codeforces 1285B**: Just Eat It! (can be solved with Greedy, but small constraints allow BT).
4.  **Knight's Tour**: Traversing a board with a knight.
5.  **CSES - Chessboard and Queens**: N-Queens variation.
6.  **LeetCode 51**: N-Queens (classical task).
7.  **LeetCode 37**: Sudoku Solver.
8.  **Codeforces 580C**: Kefa and Park (DFS/Backtracking in a tree).

---

## 6. Tips and Common Errors

### 6.1. Common Errors

**1. Forgetting undoMove:**
```cpp
// WRONG
void backtrack(State s) {
    makeMove(s);
    backtrack(s);
    // Forgotten: undoMove(s);
}
```

**2. Modifying global variables without restoring them:**
```cpp
vector<int> path;

void solve() {
    path.push_back(x);
    solve();
    // Forgotten: path.pop_back();
}
```

**3. Incorrect base check:**
```cpp
// WRONG: we might miss valid solutions
if (idx > n) return;  // Should be idx == n
```

### 6.2. Optimization Techniques

**1. Bitmasks instead of boolean arrays:**
```cpp
// Instead of bool used[20];
int usedMask = 0;

// Check
if (usedMask & (1 << i)) { /* used */ }

// Set
usedMask |= (1 << i);

// Remove
usedMask &= ~(1 << i);
```

**2. Precalculation:**
If checks are expensive, precalculate them once.

**3. Sorting the input:**
Sometimes sorting allows for earlier pruning.

### 6.3. Debugging Tips

1.  **Print the path:** Show the solution at each step.
2.  **Limit the depth:** Test with small inputs first.
3.  **Count the calls:** How many recursive calls are made?
4.  **Visualize the tree:** Draw the first few levels.

---

## 🏁 Conclusion

Always remember to restore the state (`undoMove`) after returning from recursion. This is the most common error.

Backtracking is a powerful technique but requires attention to detail. The key to success is:
1.  **Correct state definition** - what do we need to know at each step?
2.  **Efficient validation** - the earlier we reject invalid paths, the faster.
3.  **Correct backtracking** - every change must be undone.
4.  **Intelligent pruning** - good heuristics make the difference between a fast and a slow solution.

Practice with classical tasks (N-Queens, Sudoku, permutations) before moving to more complex applications. Understanding these foundations will help you solve much harder tasks in competitions.

```