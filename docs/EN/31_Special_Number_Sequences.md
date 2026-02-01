# 🔢 Special Number Sequences: Deep Theory

## 🔢 Catalan Numbers

### Recurrence Relations
$$C_0 = 1, \quad C_{n+1} = \sum_{i=0}^{n} C_i C_{n-i}$$
$$C_n = \frac{4n-2}{n+1} C_{n-1}$$

### Proof for Parentheses (Reflection Principle)
The number of paths from $(0,0)$ to $(n,n)$ that do not go above the diagonal $y=x$ is the total number of paths $\binom{2n}{n}$ minus the "bad" paths. Any bad path touches the line $y=x+1$. If we reflect the part of the path up to the first touch across this line, we get a path to $(n-1, n+1)$. Their number is $\binom{2n}{n-1}$.
$$C_n = \binom{2n}{n} - \binom{2n}{n-1}$$

---

## 🏗️ Prüfer Sequences

Encode a tree with $n$ labeled vertices as a sequence of $n-2$ numbers.
- **Cayley's Formula**: The number of labeled trees on $n$ vertices is $n^{n-2}$.
- **Encoding**: Repeat $n-2$ times: find the leaf with the smallest label, record its neighbor, and remove the leaf.

---

## 🏁 Gray Codes: Hamiltonian Path in Hypercube

A Gray code for $N$ bits describes a Hamiltonian cycle in an $N$-dimensional hypercube (a graph where vertices are $N$-bit numbers and edges connect numbers differing by 1 bit).

**Application**: Tower of Hanoi problem. The movement of disks exactly follows bit changes in Gray code.

---

## 📂 Integer Partition

**Euler's Pentagonal Number Theorem**:
$$P(n) = \sum_{k \neq 0, (-1)^k \cdot \text{pent}(k) \le n} (-1)^{k-1} P(n - \text{pent}(k))$$
where $\text{pent}(k) = \frac{k(3k-1)}{2}$ are generalized pentagonal numbers ($1, 2, 5, 7, 12, 15, \dots$).
This allows calculating $P(n)$ in $O(n \sqrt{n})$, which is much faster than $O(n^2)$.

---

## 🏁 Conclusion

These sequences are the "alphabet" of combinatorial counting. If you see 14 for $N=4$ or 42 for $N=5$ in a problem, it is almost certainly Catalan.
