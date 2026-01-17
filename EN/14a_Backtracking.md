# 🔍 Backtracking

Backtracking is a general algorithmic method for finding all (or some) solutions to tasks by gradually building solution candidates.

## ⚙️ How it works
If the current path cannot lead to a valid solution, the algorithm "backtracks", undoing the last changes and trying another possibility.

## ♟️ Classic Tasks
1. **N-Queens Problem**: Placing $N$ queens on a chessboard so none attack each other.
2. **Permutation Generation**: Using a `used[]` array to track chosen elements.
3. **Solving Sudoku**: Filling empty cells through trial and error.

## 🛠️ Backtracking Function Structure
```cpp
void solve(State current) {
    if (isGoal(current)) {
        processSolution(current);
        return;
    }
    for (Option next : getOptions(current)) {
        if (isValid(next)) {
            apply(next);
            solve(next); // Recursion
            undo(next);  // Backtrack (Cleanup)
        }
    }
}
```

## 🏁 Conclusion
Backtracking is powerful but usually slow (exponential). Success lies in "pruning" branches of the search tree that surely don't lead to a solution.
