# 🔑 Hashing: Expert Techniques

## 🔄 Minimal Cyclic Shift

Given a string $S$. Find the lexicographically smallest string that can be obtained by cyclic shifts of $S$.
- **Approach with Hashing**:
  1. Consider string $S+S$.
  2. Every cyclic shift of $S$ with length $N$ is a substring of $S+S$ with length $N$.
  3. We look for index $i \in [0, N-1]$ for which substring $S[i \dots i+N-1]$ is minimal.
  4. Comparison of two substrings is done via binary search on the length of their Longest Common Prefix (LCP) + hashing. LCP is found in $O(\log N)$, and the next character determines order.
  5. Total complexity: $O(N \log^2 N)$ or $O(N \log N)$ (because we do $N$ comparisons to find minimum).

---

## 🌳 Canonical Tree Representation (Tree Hashing)

How to check if two unrooted trees are isomorphic?
1. **Find Center**: A tree has 1 or 2 centers. This gives us root(s) for canonicalization.
2. **Subtree Hashing**:
   - For each leaf, hash is `()` (or fixed number).
   - For internal node: Sort hashes of its children, concatenate them, and wrap in parens.
   - $H(u) = "(" + \text{sort}(H(v_1), H(v_2), \dots) + ")"$.
   - Instead of strings, use polynomial hashes on sorted children values.
   
   $$H(u) = \left( \sum_{v \in children(u)} P(H(v)) \right) \pmod M$$
   *Warning*: Simple sum is commutative but can lead to collisions for different structures. Better use a complex mixing function.

---

## 🎲 Randomized Hashing

To avoid "killer tests" in Codeforces (generated specifically to break standard moduli like $10^9+7$):
1. Use **two moduli**.
2. Generate base $P$ randomly at program start.
   ```cpp
   mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
   int P = uniform_int_distribution<int>(300, 10000)(rng);
   ```

---

## 🏁 Conclusion

Tree hashing is a standard trick for checking isomorphism. For strings, hashing is often an easier (though probabilistic) alternative to KMP or Suffix Array. Always remember double hashing!
