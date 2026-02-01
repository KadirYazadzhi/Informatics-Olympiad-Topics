# ➕ Modular Arithmetic: Theory and Practice

## 📜 Deep Theory of Modular Arithmetic

Modular arithmetic is arithmetic of remainders. When we say $A \equiv B \pmod M$, we mean that $A$ and $B$ yield the same remainder when divided by $M$, or equivalently: $M$ divides the difference $(A - B)$.

### Properties of Congruences
1. **Reflexivity**: $A \equiv A \pmod M$.
2. **Symmetry**: If $A \equiv B \pmod M$, then $B \equiv A \pmod M$.
3. **Transitivity**: If $A \equiv B$ and $B \equiv C$, then $A \equiv C \pmod M$.
4. **Addition/Multiplication**: We can add and multiply congruences.
   - If $A \equiv B$ and $C \equiv D$, then $A+C \equiv B+D$ and $A \cdot C \equiv B \cdot D \pmod M$.

---

## 🚀 Modular Exponentiation

Calculating $A^B \pmod M$ naively takes $O(B)$ time, which is slow for $B \approx 10^{18}$. We use the "binary exponentiation" method, which works in $O(\log B)$.

**Idea**: $A^{2k} = (A^k)^2$ and $A^{2k+1} = A \cdot (A^k)^2$.

```cpp
long long power(long long base, long long exp, long long mod) {
    long long res = 1;
    base %= mod;
    while (exp > 0) {
        if (exp % 2 == 1) res = (__int128)res * base % mod; // Use __int128 to avoid overflow during multiplication
        base = (__int128)base * base % mod;
        exp /= 2;
    }
    return res;
}
```
*Note: If `mod` is up to $10^9$, `long long` is sufficient. If `mod` is up to $10^{18}$, intermediate multiplication can overflow `long long`, so `__int128` is used.*

---

## 🔄 Extended Euclidean Algorithm

The algorithm finds integers $x$ and $y$ satisfying Bezout's identity:
$$A \cdot x + B \cdot y = \gcd(A, B)$$

**How it works (Step-by-step)**:
We know that $\gcd(A, B) = \gcd(B, A \% B)$.
Let's assume we found $x_1, y_1$ for the pair $(B, A \% B)$, i.e.:
$$B \cdot x_1 + (A \% B) \cdot y_1 = \gcd(A, B)$$
Substitute $A \% B = A - \lfloor A/B \rfloor \cdot B$:
$$B \cdot x_1 + (A - \lfloor A/B \rfloor \cdot B) \cdot y_1 = \gcd$$
$$A \cdot y_1 + B \cdot (x_1 - \lfloor A/B \rfloor \cdot y_1) = \gcd$$
Therefore, the new coefficients are: $x = y_1$ and $y = x_1 - \lfloor A/B \rfloor \cdot y_1$.

```cpp
long long extendedGCD(long long a, long long b, long long &x, long long &y) {
    if (a == 0) {
        x = 0; y = 1;
        return b;
    }
    long long x1, y1;
    long long gcd = extendedGCD(b % a, a, x1, y1);
    x = y1 - (b / a) * x1;
    y = x1;
    return gcd;
}
```

---

## ➗ Modular Division and Modular Inverse

Division $A / B \pmod M$ is defined as $A \cdot B^{-1} \pmod M$.
The inverse element $B^{-1}$ exists only if $\gcd(B, M) = 1$.

### Finding $B^{-1}$:
1. **If $M$ is prime**: According to Fermat's Little Theorem, $B^{M-2} \pmod M$.
2. **If $M$ is not prime**: Use Extended Euclidean Algorithm to solve $B \cdot x + M \cdot y = 1$. Here $x$ is the sought inverse element (can be negative, so take $(x \% M + M) \% M$).

---

## 🇨🇳 Chinese Remainder Theorem (CRT)

Allows recovering a number $x$ if we know its remainders when divided by several **pairwise coprime** numbers.

System:
$$x \equiv a_1 \pmod {m_1}$$
$$x \equiv a_2 \pmod {m_2}$$
...
$$x \equiv a_k \pmod {m_k}$$

Let $M = m_1 \cdot m_2 \cdot \dots \cdot m_k$. The solution is unique modulo $M$ and is given by the formula:
$$x = \left( \sum_{i=1}^{k} a_i \cdot M_i \cdot y_i \right) \pmod M$$
Where $M_i = M / m_i$, and $y_i$ is the modular inverse of $M_i$ modulo $m_i$ ($M_i \cdot y_i \equiv 1 \pmod {m_i}$).

**Application**: Used when working with very large numbers, operations are performed in parallel with small moduli and finally the result is reconstructed.

---

## 🔢 Discrete Logarithm (Baby-step Giant-step)

Task: Find $x$ such that $a^x \equiv b \pmod m$.
This is a hard problem, but the Baby-step Giant-step algorithm solves it in $O(\sqrt{m} \log m)$.

**Idea**: Represent $x = i \cdot k - j$, where $k = \lceil \sqrt{m} \rceil$.
The equation becomes $a^{ik - j} \equiv b \implies (a^k)^i \equiv b \cdot a^j$.
1. Compute the right side for all $0 \le j < k$ and store them in a hash table (Baby steps).
2. Compute the left side for $i = 1, \dots, k$ and check for a match in the table (Giant steps).

---

## 🏁 Conclusion

Modular arithmetic is at the core of encryption algorithms (RSA) and many competitive programming problems. Ensure you always handle negative results correctly (`(a % n + n) % n`) and use `long long` for intermediate calculations.

```