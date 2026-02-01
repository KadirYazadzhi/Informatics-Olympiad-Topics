# 📐 Complex Numbers and FFT: Expert Level

## 🔢 Number Theoretic Transform (NTT)

FFT works with complex numbers (`double`), leading to rounding errors.
NTT is the equivalent of FFT for modular arithmetic.
- Instead of complex roots of unity, use **primitive roots** modulo $P$.
- Requires $P$ to be of form $c \cdot 2^k + 1$ (e.g., $998244353 = 119 \cdot 2^{23} + 1$).
- Primitive root for $998244353$ is $g=3$.

**Advantage**: Works with integers, no errors, exact result modulo $P$.

## 🏗️ Fast Walsh-Hadamard Transform (FWHT)

Algorithm for fast polynomial multiplication, but instead of $x^i \cdot x^j = x^{i+j}$, the operation in exponent is $i \oplus j$ (XOR), $i \& j$ (AND) or $i | j$ (OR).
- Used for problems: "Given arrays $A$ and $B$. Find $C[k] = \sum_{i \oplus j = k} A[i] B[j]$".
- Complexity: $O(N \log N)$.

---

## 🏁 Conclusion

If the problem is "convolution" (sum of products with fixed sum/XOR of indices), the answer is FFT/NTT or FWHT. This is standard technique for counting combinations.
