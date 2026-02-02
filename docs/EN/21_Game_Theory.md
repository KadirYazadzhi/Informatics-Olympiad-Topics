# 🎮 Game Theory: Strategies and Analysis

## 🎯 Introduction

Game theory investigates the strategic interaction between rational players. In competitive programming, we primarily consider **combinatorial games** - games with complete information, deterministic moves, no randomness, and with two players who play in turns.

The main question is: Which player has a winning strategy under optimal play? "Optimal play" means that both players play perfectly - they always make the best possible move.

---

## ♟️ Classification of Games

In competitive programming, we consider **impartial games** and **partizan games**.
- **Impartial**: The permissible moves depend only on the state, not on whose turn it is (e.g., Nim).
- **Partizan**: The moves depend on the player (e.g., Chess - white moves white pieces).

---

## 🔙 Retrograde Analysis

This is a technique for determining winning/losing positions by working backwards from the end to the beginning. It is used for games represented as a state graph.

1. **Terminal states**: All states from which there are no moves are **Losing** (L).
2. If from state $U$ there is at least one move to a Losing state $V$, then $U$ is **Winning** (W).
3. If from state $U$ all moves lead to Winning states, then $U$ is **Losing** (L).

**Algorithm (BFS-like)**:
- Initialize `degree[u]` = number of outgoing edges.
- Push all terminal states into a queue.
- When processing state $V$ (whose status we already know):
  - For each $U$ leading to $V$:
    - If $V$ is losing $\implies$ $U$ becomes winning immediately.
    - If $V$ is winning $\implies$ decrement `degree[U]`. If `degree[U]` becomes 0, it means all moves from $U$ lead to winning states $\implies$ $U$ becomes losing.

---

## ⚖️ The Game of Nim and XOR Sums (Advanced)

### Basic Theory of Nim

The classical game of Nim consists of several piles of stones. On a turn, a player takes an arbitrary number of stones from ONE pile (at least one). The one who cannot make a move (all piles are empty) loses.

**Bouton's Theorem:** A position is **losing** exactly when the XOR sum of all piles is 0.

```cpp
bool isLosingPosition(vector<int>& piles) {
    int xorSum = 0;
    for (int pile : piles) {
        xorSum ^= pile;
    }
    return xorSum == 0;  // true = losing, false = winning
}
```

### Why XOR?
Every non-negative integer $n$ has a unique decomposition into a sum of powers of 2. The game of Nim is equivalent to a sum of several single-pile games. The operation that corresponds to "adding games without interaction" is bitwise addition without carry (XOR).

When combining several independent games, the winning status of the combined game is determined by the XOR sum of the Grundy numbers of the individual games.

### Finding a Winning Move

If the XOR sum is not 0, there is a winning move. How to find it?

```cpp
int findWinningMove(vector<int>& piles) {
    int xorSum = 0;
    for (int pile : piles) {
        xorSum ^= pile;
    }
    
    if (xorSum == 0) {
        return -1;  // No winning move (losing position)
    }
    
    // Find a pile from which we can take
    for (int i = 0; i < piles.size(); i++) {
        int target = piles[i] ^ xorSum;
        if (target < piles[i]) {
            // Take from pile i down to target stones
            cout << "Take from pile " << i 
                 << " down to " << target << " stones" << endl;
            return i;
        }
    }
    return -1;
}
```

### Variations of Nim:
1. **Misere Nim**: The one forced to take the last object wins (the one who takes the last object loses).
   - Strategy: The same as the normal game, unless all piles are of size 1. Then the winning move is to leave an odd number of piles.

2. **Bounded Nim**: At most $K$ stones can be taken.
   - Equivalent to a pile of size $N \bmod (K+1)$.

3. **Staircase Nim**: Coins on steps. Moving from step $i$ to $i-1$ is equivalent to removal.
   - Only the steps at **odd** positions (1, 3, 5...) are considered.

```cpp
// Misere Nim
bool misereNim(vector<int>& piles) {
    int xorSum = 0;
    bool allOnes = true;
    
    for (int pile : piles) {
        xorSum ^= pile;
        if (pile > 1) allOnes = false;
    }
    
    if (allOnes) {
        // All piles are of size 1
        return piles.size() % 2 == 1;  // Odd number = winning
    }
    
    return xorSum != 0;  // Like normal Nim
}

// Staircase Nim
bool staircaseNim(vector<int>& stairs) {
    int xorSum = 0;
    for (int i = 0; i < stairs.size(); i++) {
        if (i % 2 == 1) {  // Only odd steps
            xorSum ^= stairs[i];
        }
    }
    return xorSum != 0;
}
```

---

## 🧮 Grundy Numbers (Sprague-Grundy Theorem)

For an arbitrary impartial game, we can define a **Grundy number** (or nimber) for each state.

**Definition:** The Grundy number of a state is the smallest non-negative integer (MEX - Minimal EXcludant) that is not a Grundy number of any of the states reachable in one move.

$$g(position) = \text{mex}(\{g(next) : next \text{ is reachable from } position\})$$

**MEX function:**
```cpp
int mex(set<int>& s) {
    int mex = 0;
    while (s.count(mex)) {
        mex++;
    }
    return mex;
}
```

### Calculating Grundy Numbers

```cpp
map<State, int> grundy;

int computeGrundy(State state) {
    if (isTerminal(state)) return 0;  // No moves
    
    if (grundy.count(state)) return grundy[state];
    
    set<int> reachable;
    for (State next : getNextStates(state)) {
        reachable.insert(computeGrundy(next));
    }
    
    return grundy[state] = mex(reachable);
}
```

### Sprague-Grundy Theorem

The combination of several independent games has a Grundy number equal to the XOR sum of the Grundy numbers of the individual games.

```cpp
// Combination of games
int combinedGrundy(vector<int>& individualGrundies) {
    int result = 0;
    for (int g : individualGrundies) {
        result ^= g;
    }
    return result;
}

// Position is winning if Grundy number != 0
bool isWinning(int grundy) {
    return grundy != 0;
}
```

### Example: Subtraction Game

We have a pile of $N$ stones. On a turn, 1, 2, or 3 stones can be taken. What is the Grundy number?

```cpp
int grundy[MAXN];

void computeGrundyForGame() {
    grundy[0] = 0;  // Terminal position
    
    for (int n = 1; n < MAXN; n++) {
        set<int> reachable;
        
        // We can take 1, 2, or 3 stones
        if (n >= 1) reachable.insert(grundy[n-1]);
        if (n >= 2) reachable.insert(grundy[n-2]);
        if (n >= 3) reachable.insert(grundy[n-3]);
        
        grundy[n] = mex(reachable);
    }
}
```

For this specific game, the Grundy numbers are: $0, 1, 2, 3, 0, 1, 2, 3, \ldots$ (periodic with period 4).

---

## 🏆 Minimax with Memoization

For games that are not impartial (they do not have Grundy numbers), we use recursion with memory.

### Basic Idea

Each state can be:
- **Winning (W):** The current player can win.
- **Losing (L):** The current player inevitably loses under optimal play.
- **Draw (D):** Under optimal play, the result is a draw (rare in combinatorial games).

```cpp
map<State, int> memo;
// 1: Wins, -1: Loses, 0: Unknown

int solve(State state) {
    if (is_terminal(state)) return -1; // No moves -> we lose
    if (memo.count(state)) return memo[state];

    int res = -1; // Assume we lose
    for (State next_state : get_moves(state)) {
        if (solve(next_state) == -1) { // If the opponent loses from the next state
            res = 1; // We win
            break;
        }
    }
    return memo[state] = res;
}
```

### Example: Game on a Graph

Two players move a piece on a graph. The one who cannot make a move loses.

```cpp
vector<int> graph[MAXN];
int dp[MAXN];  // 1 = winning, -1 = losing, 0 = uncalculated

int solveGraph(int node, int depth) {
    if (graph[node].empty()) return -1;  // No moves
    if (dp[node] != 0) return dp[node];
    
    for (int next : graph[node]) {
        if (solveGraph(next, depth + 1) == -1) {
            return dp[node] = 1;  // Found a losing move for the opponent
        }
    }
    
    return dp[node] = -1;  // All moves lead to winning positions for the opponent
}
```

### Minimax with Evaluation (for complex games)

For games like chess or Go, where we cannot calculate all positions, we use heuristic evaluation.

```cpp
const int INF = 1e9;

// Maximize for us, minimize for the opponent
int minimax(State state, int depth, bool maximizing) {
    if (depth == 0 || isTerminal(state)) {
        return evaluate(state);  // Heuristic function
    }
    
    if (maximizing) {
        int maxEval = -INF;
        for (State next : getMoves(state)) {
            int eval = minimax(next, depth - 1, false);
            maxEval = max(maxEval, eval);
        }
        return maxEval;
    } else {
        int minEval = INF;
        for (State next : getMoves(state)) {
            int eval = minimax(next, depth - 1, true);
            minEval = min(minEval, eval);
        }
        return minEval;
    }
}
```

### Alpha-Beta Pruning (Optimization)

We prune branches that cannot influence the result.

```cpp
int alphaBeta(State state, int depth, int alpha, int beta, bool maximizing) {
    if (depth == 0 || isTerminal(state)) {
        return evaluate(state);
    }
    
    if (maximizing) {
        int maxEval = -INF;
        for (State next : getMoves(state)) {
            int eval = alphaBeta(next, depth - 1, alpha, beta, false);
            maxEval = max(maxEval, eval);
            alpha = max(alpha, eval);
            if (beta <= alpha) break;  // Beta cut-off
        }
        return maxEval;
    } else {
        int minEval = INF;
        for (State next : getMoves(state)) {
            int eval = alphaBeta(next, depth - 1, alpha, beta, true);
            minEval = min(minEval, eval);
            beta = min(beta, eval);
            if (beta <= alpha) break;  // Alpha cut-off
        }
        return minEval;
    }
}
```

---

## 📊 Practical Tips and Tricks

### 1. Symmetric Games

In symmetric games (the same board for both players), the second player can often copy the moves of the first.

**Example:** On a board with central symmetry, the second player plays symmetrically to the first player's move.

### 2. Parity

Many games depend on the parity of the state.

**Example:** A game where we can take coins from two ends of a row. If the total count is odd, the first player can always take the larger coin.

### 3. Invariants

Look for quantities that are preserved or change predictably.

**Example:** In the game of Nim, the invariant is the XOR sum of the piles.

### 4. Modeling as a Graph

It is often useful to represent the game as a state graph.

---

## 🎯 Sample Tasks

### Task 1: Bachet's Game

We have a number $N$. On a turn, we can subtract 1, 2, or 3. The one who reaches 0 loses.

```cpp
bool canWin(int n) {
    // Grundy numbers are periodic: 0,1,2,3,0,1,2,3,...
    return (n % 4) != 0;
}
```

### Task 2: Division Game

We have a number $N$. On a turn, we can divide it by any of its divisors (other than 1 and $N$). The one who cannot make a move loses.

```cpp
map<int, bool> memo;

bool canWin(int n) {
    if (isPrime(n)) return false;  // Prime numbers are losing
    if (memo.count(n)) return memo[n];
    
    for (int d = 2; d * d <= n; d++) {
        if (n % d == 0) {
            if (!canWin(d) || !canWin(n / d)) {
                return memo[n] = true;
            }
        }
    }
    
    return memo[n] = false;
}
```

### Task 3: Combined Games

We have three piles: (3, 4, 5). We can play Nim on each pile.

```cpp
int result = 3 ^ 4 ^ 5;  // XOR sum of Grundy numbers
if (result != 0) {
    cout << "First player wins" << endl;
} else {
    cout << "Second player wins" << endl;
}
```

---

## 🏁 Conclusion

The key to "Game" type tasks is to determine:
1. Does the game have Grundy numbers? (Is it impartial?) $\implies$ Use XOR/mex.
2. Are there few states? $\implies$ Use Minimax/Retrograde analysis.
3. Is there symmetry? $\implies$ Look for mirror strategies.

**Summary of Techniques:**

| Game Type | Approach | Complexity | Application |
|-----------|----------|------------|-------------|
| Nim and variations | XOR sum of piles | $O(N)$ | Classical Nim, Misere Nim, Staircase Nim |
| Impartial games | Grundy numbers + XOR | $O(States \cdot Moves)$ | Composition of games |
| Partizan games | Minimax | $O(States)$ | Games with different moves for players |
| Complex games | Alpha-Beta | $O(b^{d/2})$ | Chess, Checkers (with heuristic) |
| Graph games | DP/BFS | $O(V + E)$ | Games on graphs |

**Steps in Solving:**

1. **Determine the game type:**
   - Impartial or partizan?
   - Finite or infinite?
   - Are there cyclic states?

2. **Find the base cases:**
   - Which positions are losing (no moves)?
   - Which positions are directly winning?

3. **Calculate Grundy numbers or minimax:**
   - For impartial games: use mex.
   - For partizan games: use minimax.

4. **Combine subgames (if any):**
   - XOR sum of Grundy numbers.

5. **Optimize:**
   - Memoization.
   - Alpha-beta pruning.
   - Searching for a pattern/period.

**Common Errors:**

- Forgetting that both play optimally.
- Confusing winning/losing positions.
- Incorrect calculation of mex.
- Omitting special cases (empty state, cycles).

**Useful Tasks for Practice:**

1. **CSES - Nim Game I**: Classical Nim.
2. **CSES - Nim Game II**: Variation of Nim.
3. **Codeforces 768C**: Jon Snow and his Favourite Number.
4. **CodeChef - GAMENEW**: Grundy numbers.
5. **SPOJ - MCOINS**: Game with coins.
6. **UVa 10165**: Stone Game (Nim variation).
7. **Codeforces 768B**: Code For 1 (Grundy on a tree).

Game theory requires practice and intuition. Start with simple tasks and gradually increase the complexity. Understanding Nim is the foundation - after that, everything else is an application and extension of the same principles.
