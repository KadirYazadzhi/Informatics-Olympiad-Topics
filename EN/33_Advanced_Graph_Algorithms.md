# 🌐 Advanced Graphs: Deep Theory

## 🤝 Bipartite Matching

### Hall's Marriage Theorem
A bipartite graph with parts $X$ and $Y$ has a complete matching covering $X$ if and only if for every subset $S \subseteq X$, the number of its neighbors $|N(S)| \ge |S|$.

### Hopcroft-Karp Algorithm
Finds maximum matching in $O(E \sqrt{V})$.
- Works in phases. In each phase, finds **all** shortest augmenting paths simultaneously using BFS and uses them via DFS.

---

## 🛤️ Strongly Connected Components (SCC) - 2-SAT

The **2-Satisfiability (2-SAT)** problem:
Given a logical expression in Conjunctive Normal Form where each clause has 2 literals: $(x_1 \lor \neg x_2) \land (\neg x_1 \lor x_3) \dots$
Can we assign True/False values so the expression is True?

**Solution via SCC**:
1. Build implication graph. For $(A \lor B)$ add edges $\neg A \to B$ and $\neg B \to A$.
2. Find SCCs.
3. If for any variable $x$, $x$ and $\neg x$ are in the same component $\to$ **No solution**.
4. Otherwise, a solution exists. If $topological(SCC(x)) > topological(SCC(\neg x))$, then $x = \text{True}$.

---

## 🌳 Dominator Tree

In a flow graph with start node $S$, node $U$ dominates $V$ if every path from $S$ to $V$ passes through $U$.
- The dominator tree shows the hierarchy of domination.
- Used for compiler optimization and memory analysis.
- Lengauer-Tarjan Algorithm: $O(E \alpha(V))$.

---

## 🏁 Conclusion

2-SAT is a classic problem that reduces directly to SCC. Hopcroft-Karp is rarely needed (Kuhn/Flow usually suffice), but it is the only solution for $V, E \approx 10^5$.
