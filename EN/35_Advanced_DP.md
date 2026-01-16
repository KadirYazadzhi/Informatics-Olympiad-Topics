# 🔄 Dynamic Programming: Expert Optimizations

## 🌳 Li Chao Tree

Structure for maintaining a set of functions (usually lines $y = mx + c$) and finding max/min at a given point $x$.
- Similar to segment tree over coordinates $X$.
- In each node, keep the line that is "best" for the midpoint of the interval.
- When adding a new line, if it is better than current at mid, swap them and push the worse one recursively down.
- Complexity: $O(\log (\text{Coordinate Range}))$.
- More flexible than Convex Hull Trick as it doesn't require sorted queries or slopes.

---

## 👽 Alien's Trick (WQS Binary Search)

Optimization technique for problems like: "Find optimal value using exactly $K$ items/operations".
- If the answer function wrt $K$ is convex/concave, we can remove the $K$ constraint by adding a **cost (penalty) $\lambda$** for each operation use.
- Binary search for $\lambda$ such that optimal operations count at this cost is exactly $K$.
- Reduces $O(N \cdot K)$ DP to $O(N \log (\text{Precision}))$.

---

## 🕸️ Connectivity DP (Plug DP)

Problems counting Hamiltonian paths, domino tilings, or closed loops in a grid.
- Profile state must keep info on which cells are connected to each other.
- Uses **Parenthesis representation** for connectivity.
- Since valid states are few, use a HashMap to store DP states.

---

## 🏁 Conclusion

Li Chao Tree is the standard for linear function optimization today. Alien's Trick is magic that turns hard constraints into simple cost modifiers.
