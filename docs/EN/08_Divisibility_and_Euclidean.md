# ➗ Divisibility, GCD, and the Euclidean Algorithm

Number Theory is a cornerstone of algorithm design. Understanding how integers divide and interact is crucial for everything from basic loops to advanced cryptography (RSA).

---

## 1. Fundamental Properties

### 1.1. Divisibility Rules
Quickly checking if $N$ is divisible by $K$ without performing the division:
*   **2**: Last digit is even ($0, 2, 4, 6, 8$).
*   **3**: Sum of digits is divisible by 3.
*   **4**: The number formed by the last two digits is divisible by 4.
*   **5**: Ends in 0 or 5.
*   **6**: Divisible by both 2 and 3.
*   **8**: Last three digits form a number divisible by 8.
*   **9**: Sum of digits is divisible by 9.
*   **10**: Ends in 0.
*   **11**: Alternating sum of digits is divisible by 11. (e.g., $132 \to 1-3+2 = 0$).

---

## 2. Greatest Common Divisor (GCD)

The GCD of $a$ and $b$ is the largest positive integer that divides both $a$ and $b$ without a remainder.

### 2.1. The Euclidean Algorithm
Based on the property: $\gcd(a, b) = \gcd(b, a \pmod b)$.
It has a logarithmic time complexity $O(\log(\min(a, b)))$, making it extremely efficient.

```cpp
// Recursive
long long gcd(long long a, long long b) {
    return b == 0 ? a : gcd(b, a % b);
}

// Iterative
long long gcd_iter(long long a, long long b) {
    while (b) {
        a %= b;
        swap(a, b);
    }
    return a;
}
```

### 2.2. Properties of GCD
*   $\\gcd(a, b) = \gcd(a, b-a)$.
*   $\\gcd(ka, kb) = k \cdot \gcd(a, b)$.
*   $\\gcd(a, b, c) = \gcd(a, \gcd(b, c))$.

---

## 3. Least Common Multiple (LCM)

The smallest positive integer that is divisible by both $a$ and $b$.
**Formula**:
$$\text{lcm}(a, b) = \frac{|a \cdot b|}{\gcd(a, b)}$$

*Important*: In code, always divide first to prevent `long long` overflow:
`lcm = (a / gcd(a, b)) * b;`

---

## 4. Extended Euclidean Algorithm

Given $a$ and $b$, it finds integers $x$ and $y$ such that:
$$ax + by = \gcd(a, b)$$
These $x$ and $y$ are known as Bézout coefficients.

**Application**: Finding the **Modular Multiplicative Inverse**.
If $\\gcd(a, m) = 1$, then $ax \equiv 1 \pmod m$. Solving $ax + my = 1$ gives $x$ as the inverse of $a$ modulo $m$.

```cpp
long long extended_gcd(long long a, long long b, long long &x, long long &y) {
    if (b == 0) {
        x = 1; y = 0;
        return a;
    }
    long long x1, y1;
    long long d = extended_gcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - y1 * (a / b);
    return d;
}
```

---

## 5. Practice Problems
1.  **CSES Common Divisors**: Finding max GCD of any pair in an array.
2.  **Codeforces 1152C**: Minimizing LCM.
3.  **UVa 10104**: Bézout's Identity implementation.
4.  **SPOJ GCDEX**: Sum of GCDs (requires more advanced techniques).

## 6. Conclusion
The Euclidean algorithm is often a component of more complex problems. Always use `long long` for number theory tasks, as intermediate products frequently exceed $2 \cdot 10^9$.

```
