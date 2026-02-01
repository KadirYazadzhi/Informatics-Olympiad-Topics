# 🎮 Game Theory: Strategies and Analysis

## ♟️ Classification of Games

In competitive programming, we consider **impartial games** and **partizan games**.
- **Impartial**: Allowable moves depend only on the state, not on whose turn it is (e.g., Nim).
- **Partizan**: Moves depend on the player (e.g., Chess - White moves white pieces).

---

## 🔙 Retrograde Analysis

This is a technique for determining winning/losing positions by working backwards from the end. Used for games represented as a state graph.

1. **Terminal States**: All states with no moves are **Losing** (L).
2. If from state $U$ there is at least one move to a Losing state $V$, then $U$ is **Winning** (W).
3. If from state $U$ all moves lead to Winning states, then $U$ is **Losing** (L).

**Algorithm (BFS-like)**:
- Initialize `degree[u]` = number of outgoing edges.
- Add all terminal states to a queue.
- When processing state $V$ (whose status is known):
  - For each $U$ leading to $V$:
    - If $V$ is losing $	o$ $U$ becomes winning immediately.
    - If $V$ is winning $	o$ decrease `degree[U]`. If `degree[U]` becomes 0, it means all moves from $U$ lead to winning states $	o$ $U$ becomes losing.

---

## ⚖️ Game of Nim and XOR Sums (Advanced)

### Why XOR?
Every non-negative integer $n$ has a unique decomposition into a sum of powers of 2. The game of Nim is equivalent to the sum of several single-pile games. The operation corresponding to "adding games without interaction" is bitwise addition without carry (XOR).

### Variations of Nim:
1. **Misere Nim**: The player forced to take the last object wins (the one taking the last object loses).
   - Strategy: Same as normal play, unless all piles are of size 1. Then the winning move is to leave an odd number of piles.
2. **Nim with Limit**: You can take at most $K$ stones.
   - Equivalent to a pile of size $N % (K+1)$.
3. **Staircase Nim**: Coins on steps. Moving from step $i$ to $i-1$ is equivalent to removing.
   - Only steps at **odd** positions (1, 3, 5...) are counted.

---

## 🏆 Minimax with Memoization

For games that are not impartial (no Grundy numbers), use recursion with memory.

```cpp
int memo[MAX_STATE];
// 1: Win, -1: Lose
int solve(int state) {
    if (is_terminal(state)) return -1; // No moves -> lose
    if (memo[state] != 0) return memo[state];

    int res = -1; // Assume we lose
    for (int next_state : get_moves(state)) {
        if (solve(next_state) == -1) { // If opponent loses from next state
            res = 1; // We win
            break;
        }
    }
    return memo[state] = res;
}
```

---

## 🏁 Conclusion

The key to "Game" type problems is to determine:
1. Does the game have Grundy numbers? (Is it impartial?) $	o$ Use XOR/mex.
2. Are there few states? $	o$ Use Minimax/Retrograde Analysis.
3. Is there symmetry? $	o$ Look for mirror strategies.

```