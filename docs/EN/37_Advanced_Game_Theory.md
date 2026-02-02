# 🎮 Advanced Game Theory

Combinatorial game theory considers games that are:
1.  **Impartial**: The available moves depend only on the state of the game, not on which player is taking a turn. (Chess is NOT such a game because whites cannot move black pieces).
2.  **Perfect Information**: No hidden cards or dice.
3.  **Normal Play Convention**: The last player who can make a move wins (or the one who cannot make a move loses).

## 1. Nim Game

This is the "mother" of all impartial games.
*   **Rules**: There are $K$ piles of stones with sizes $a_1, a_2, \ldots, a_k$. Two players take turns choosing one pile and removing an arbitrary number of stones from it (at least one).
*   **Winning Condition**: Whoever takes the last stone wins.

### Strategy (Nim-Sum)
The winner is determined by the **XOR sum** of the pile sizes:
$S = a_1 \oplus a_2 \oplus \ldots \oplus a_k$

*   If $S \neq 0$: The current position is a **Winning** (N-position). The first player can make a move that brings the sum to 0.
*   If $S = 0$: The current position is a **Losing** (P-position). Any move will make the sum different from 0.

**Proof**:
1.  The terminal state (all 0s) has an XOR sum of 0.
2.  From a state with $S=0$, every move leads to $S \neq 0$.
3.  From a state with $S \neq 0$, there exists a move that leads to $S=0$. Let $d$ be the most significant bit of $S$. We choose a pile $a_i$ that has this bit (1). We replace $a_i$ with $a_i \oplus S$. Since $a_i \oplus S < a_i$, this is a valid move.

## 2. Sprague-Grundy Theorem

This theorem states that **every** impartial game is equivalent to a Nim pile of a certain size. This size is called the **Grundy number** (or nim-value) of the state.

### 2.1. Calculating Grundy Numbers (MEX)
For a given state $u$, the Grundy number $g(u)$ is defined recursively as:
$g(u) = \text{MEX}(\{ g(v) \mid u \to v \text{ is a valid move} \})$

The **MEX (Minimum Excluded value)** of a set of non-negative integers is the smallest non-negative integer that is **NOT** present in the set.
*   $\text{MEX}(\{0, 1, 3\}) = 2$
*   $\text{MEX}(\{1, 2, 5\}) = 0$
*   $\text{MEX}(\emptyset) = 0$ (for losing states)

### 2.2. Combining Games
If a game consists of several independent subgames (e.g., several pieces on a board that do not interfere with each other), the Grundy number of the total game is the **XOR sum** of the Grundy numbers of the subgames.
$G_{total} = g(G_1) \oplus g(G_2) \oplus \ldots \oplus g(G_k)$

This is extremely powerful! Instead of analyzing the complex game as a whole, we break it into parts, calculate MEX for each part, and perform XOR.

### Example: Game on a Graph
We have a piece on a vertex of a directed acyclic graph (DAG). Players move the piece along the edges. Whoever cannot move loses.
Solution:
1.  For each vertex $u$, we calculate $g(u)$ using DFS and memoization.
2.  If we have $K$ pieces, the position is equivalent to Nim with piles of sizes $g(u_1), g(u_2), \ldots$.

```cpp
#include <vector>
#include <set>
#include <algorithm>

using namespace std;

const int MAXN = 1000;
vector<int> adj[MAXN];
int memo_grundy[MAXN];

int grundy(int u) {
    if (memo_grundy[u] != -1) return memo_grundy[u];
    
    set<int> reachable_values;
    for (int v : adj[u]) {
        reachable_values.insert(grundy(v));
    }
    
    int mex = 0;
    while (reachable_values.count(mex)) mex++;
    
    return memo_grundy[u] = mex;
}
```

## 3. Game Variations

### 3.1. Staircase Nim
Coins are placed on the steps of a staircase (0, 1, 2...). The move is: move an arbitrary number of coins from step $i$ to step $i-1$. Coins from step 0 are removed.
**Solution**: Only the coins on the **odd positions** (1, 3, 5...) matter.
*   Moving from odd to even ($i \to i-1$) is equivalent to removing coins from a Nim pile (because even steps are not counted).
*   Moving from even to odd ($i \to i-1$) is reversible by the opponent (they can move them to $i-2$).
*   Answer: $a_1 \oplus a_3 \oplus a_5 \ldots$

### 3.2. Green Hackenbush
A game on graphs where edges are removed.
*   Each edge is either "green" (can be removed by anyone) or touches the ground.
*   When an edge is removed, everything not connected to the ground falls (disappears).
*   For trees: $g(u) = (\bigoplus_{v \in children} (g(v) + 1))$.

## 4. Misere Play
The variant where **the last player loses**.
*   For simple Nim: The strategy is the same as Normal Play, unless all piles are of size 1.
    *   If all are 1: The player wins if their number is odd (XOR = 1).
    *   Otherwise: We play according to the normal strategy.

## 5. Practice Tasks
1.  **HackerRank "Game of Stones"**: Simple subtraction game, solved with DP/Grundy.
2.  **SPOJ ADAGAME**: Game on a graph with cycles (requires careful analysis).
3.  **Codeforces 768E**: Game of Stones (precalculation of Grundy numbers).
4.  **UVa 10165**: Stone Game (pure Nim).

## 🏁 Conclusion
When you see a game in a competition:
1.  Check if it is impartial.
2.  If yes $\to$ Grundy theorem (XOR).
3.  Break it into independent subgames.
4.  Calculate MEX.

---

## 6. Memoized Implementation of Grundy Numbers

For complex games with many states, we use `map` or `unordered_map` for memoization:

```cpp
#include <unordered_map>
#include <set>

unordered_map<int, int> memo_map;

int compute_grundy(int state) {
    if (memo_map.count(state)) return memo_map[state];
    
    // Base case: losing position
    if (isLosing(state)) return memo_map[state] = 0;
    
    set<int> reachable;
    for (int nextState : getNextStates(state)) {
        reachable.insert(compute_grundy(nextState));
    }
    
    // MEX
    int mex = 0;
    while (reachable.count(mex)) mex++;
    
    return memo_map[state] = mex;
}
```

---

## 7. Impartial Games with Additional Rules

### 7.1. Anti-Nim (Misère Nim)
In this variation **the last player loses** (opposite of normal Nim).

**Strategy**:
*   If all piles have size 1: The player wins if their number is **odd**.
*   Otherwise: We play by the standard strategy (XOR sum), but aim to leave the opponent in a position with XOR = 1.

### 7.2. Moore's Nim
Removing stones from an **arbitrary number of piles** at once is allowed (not just from one).

**Solution**: The Grundy number of the position is the same as in ordinary Nim - the XOR sum of the sizes.

---

## 8. Games with Partial Information

If the game does not have perfect information (has randomness or hidden elements), the Grundy theorem does not apply directly. Then the following are used:
*   **Probabilistic methods**.
*   **Minimax algorithm** (for games like Chess, Go).
*   **Alpha-Beta Pruning** (Minimax optimization).

---

## 9. Advanced Examples

### 9.1. "Subtract Set" Game
You have a number $N$. On a turn, you can subtract $a_1, a_2, \ldots, a_k$ (a given set of numbers). Whoever reaches 0 wins.

**Solution**:
```cpp
set<int> S_set = {1, 3, 4}; // Allowed subtractions
int memo_table[1001];

int calculate_grundy(int n) {
    if (n == 0) return 0; // Losing position
    if (memo_table[n] != -1) return memo_table[n];
    
    set<int> reachable;
    for (int s : S_set) {
        if (n >= s) {
            reachable.insert(calculate_grundy(n - s));
        }
    }
    
    int mex = 0;
    while (reachable.count(mex)) mex++;
    return memo_table[n] = mex;
}
```

For a game with $K$ independent positions: $G = g(n_1) \oplus g(n_2) \oplus \ldots \oplus g(n_k)$.

### 9.2. "Divisor Game"
You have a number $N$. On a turn, you choose a divisor $d$ of $N$ (where $d < N$) and replace $N$ with $N - d$. Whoever reaches 0 (or cannot make a move) loses.

**Observation**: This is equivalent to checking the parity of $N$!
*   If $N$ is even, you win.
*   If $N$ is odd, you lose.

However, if the conditions are more complex, use Grundy numbers.

---

## 10. Game Sums

If you have several games played in parallel (each player chooses which game to make a move in), the total game is the **sum** of the individual games:
$$G_{total} = G_1 + G_2 + \ldots + G_k$$

The Grundy number of the sum is the XOR of the Grundy numbers:
$$g(G_{total}) = g(G_1) \oplus g(G_2) \oplus \ldots \oplus g(G_k)$$

---

## 🏁 Final Tips

*   Always first check if the game is impartial and with perfect information.
*   Draw a state graph for small examples to understand Grundy numbers.
*   Remember that XOR is the key operation in game theory.
*   For competitions under time pressure: Learn standard games (Nim, Staircase Nim) by heart.

Game theory is one of the most beautiful areas in competitive programming - it combines mathematics, logic, and programming in a unique way!
