# 🌊 Network Flows: Expert Problems

## 🔄 Circulation with Demands

Each edge has capacity $[L, R]$ (lower and upper bound).
1. Create new network with capacities $R - L$.
2. Maintain balance for each node $in[v] - out[v]$ (based on $L$).
3. Add $S_{new}$ and $T_{new}$.
   - If balance $>0$, add edge $S_{new} \to v$.
   - If $<0$, add $v \to T_{new}$.
4. If max flow saturates all edges from $S_{new}$, a valid circulation exists.

---

## ✂️ Global Min-Cut (Stoer-Wagner Algorithm)

Finds min-cut in **undirected, weighted graph** (without fixing $S$ and $T$).
- Works without flows!
- Complexity: $O(VE + V^2 \log V)$.
- In each phase finds "Minimum s-t cut" for arbitrary $s, t$ and then merges $s$ and $t$.

---

## 🏁 Conclusion

Circulation with Demands is a standard technique for reducing problems ("is there a matrix with given row/col sums") to flows. Stoer-Wagner is faster than $V$ times Max-Flow for global cut.
