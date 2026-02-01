# 🔤 Suffix Automaton (SAM)

## ⚙️ Construction and Properties

SAM is a DAG where nodes are equivalence classes of substrings (based on their `endpos` sets), and edges are character transitions.
- Each node has `len` (length of longest substring in class) and `link` (suffix link to class of longest suffix in another class).

**Incremental Construction**:
1. Have `last` (node for entire current string). Create `cur` with `len = len(last) + 1`.
2. Walk `link` from `last` back. If no transition with new char $c$, add to `cur`.
3. If reach node $p$ having transition $q$ with $c$:
   - If $len(p) + 1 == len(q)$, then `link(cur) = q`.
   - Else clone $q$ to $clone$ (with `len = len(p) + 1`) and redirect links.

**Complexity**: $O(N)$ time and $O(N \Sigma)$ memory (or $O(N)$ with map).

## 🚀 Applications

1. **Count distinct substrings**: $\sum (len(u) - len(link(u)))$.
2. **Longest Common Substring of multiple strings**: Pass strings through automaton and track current length.
3. **K-th Lexicographical Substring**: Count paths in DAG.

---

## 🏁 Conclusion

SAM is the most powerful string structure. Code is short (~30 lines), but logic is abstract. Memorize it.
