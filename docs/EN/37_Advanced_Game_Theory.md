# 🎮 Advanced Game Theory

Combinatorial Game Theory studies impartial games where:
1.  Two players take turns.
2.  Perfect information (no hidden cards).
3.  No chance (no dice).
4.  Last player to move wins (Normal Play) or loses (Misere Play).

## 1. Nim Game
The most fundamental impartial game.
*   **Setup**: $K$ piles of stones with sizes $a_1, a_2, \dots, a_k$.
*   **Move**: Pick a pile and remove any number of stones ($>0$).
*   **Winning Strategy**: Compute the **XOR sum** (Nim-Sum) of pile sizes.
    $S = a_1 \oplus a_2 \oplus \dots \oplus a_k$
    *   If $S \neq 0$: Current state is **Winning** (N-position).
    *   If $S = 0$: Current state is **Losing** (P-position).

## 2. Sprague-Grundy Theorem
Any impartial game under the normal play convention is equivalent to a Nim pile of a certain size. This size is called the **Grundy Number** (or Nim-Value).

### 2.1. Calculating Grundy Numbers (MEX)
For a state $u$, the Grundy number $g(u)$ is the **MEX** (Minimum Excluded value) of the Grundy numbers of all states reachable from $u$.
$g(u) = \text{MEX}(\{ g(v) \mid u \to v \})$

*   $\text{MEX}(S)$ is the smallest non-negative integer NOT in set $S$.
*   Base case: Losing state has $g(u) = 0$.

### 2.2. Composition of Games
If a game consists of several independent subgames $G_1, G_2, \dots, G_k$, the Grundy value of the combined game state is the XOR sum of their Grundy values:
$G_{total} = g(G_1) \oplus g(G_2) \oplus \dots \oplus g(G_k)$.

### Example: Game on a Graph
A token is on a node in a DAG. Players move it along edges.
*   Calculate $g(u)$ for all nodes using DFS/Memoization.
*   $g(u) = \text{MEX}(\{ g(v) \mid (u, v) \in E \})$.

```cpp
int memo[MAXN];
vector<int> adj[MAXN];

int grundy(int u) {
    if (memo[u] != -1) return memo[u];
    
    set<int> reachable_grundy;
    for (int v : adj[u]) {
        reachable_grundy.insert(grundy(v));
    }
    
    int res = 0;
    while (reachable_grundy.count(res)) res++;
    return memo[u] = res;
}
```

## 3. Staircase Nim
Coins are on steps of a staircase. A move consists of moving coins from step $i$ to $i-1$. Coins reaching step 0 are removed.
*   **Strategy**: Only coins on **odd-numbered steps** matter. The moves from even to odd are reversible (opponent can move them back/further).
*   Answer is XOR sum of coins on steps 1, 3, 5...

## 4. Green Hackenbush
A game played on graphs where edges are removed. Equivalent to Nim, but on tree structures. The Grundy value of a tree node can be computed by XOR-ing children + 1 (simplified rule).

## 5. Practice Problems
1.  **SPOJ ADAGAME**: Game on graph.
2.  **Codeforces 768E**: Game of Stones (requires precomputing Grundy).
3.  **HackerRank "Day 2: Nim Game"**: Basic Nim.

## 6. Conclusion
If a game is impartial, **always** think about Grundy Numbers and XOR.