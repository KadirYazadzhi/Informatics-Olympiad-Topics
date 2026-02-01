# 🔢 Special Number Sequences: Detailed Theory and Applications

In combinatorics and number theory, certain integer sequences appear repeatedly in diverse problems. Understanding their recurrence relations, closed-form formulas, and generating functions allows for immediate insights into complex counting problems.

---

## 1. Catalan Numbers

The Catalan numbers $C_n$ form a sequence: $1, 1, 2, 5, 14, 42, 132, 429, 1430, \dots$ for $n = 0, 1, 2, \dots$.

### 1.1. Formula
The explicit formula for the $n$-th Catalan number is:
$$C_n = \frac{1}{n+1} \binom{2n}{n} = \frac{(2n)!}{(n+1)!n!}$$ 

For large $n$, we can use Stirling's approximation to see that $C_n \sim \frac{4^n}{n^{3/2} \sqrt{\pi}}$.

### 1.2. Recurrence Relation
The most useful recurrence for DP is the convolution:
$$C_{n+1} = \sum_{i=0}^{n} C_i C_{n-i}$$ 
Base case: $C_0 = 1$.

### 1.3. Combinatorial Interpretations
Catalan numbers are famous for being the answer to hundreds of different counting problems:
1.  **Valid Parentheses**: The number of correctly matched sequences of $n$ pairs of parentheses.
    *   Example $n=3$: `((()))`, `(()())`, `(())()`, `()(())`, `()()()`.
2.  **Binary Trees**: The number of distinct binary trees with $n$ nodes.
3.  **Polygon Triangulation**: The number of ways to divide a convex polygon with $n+2$ sides into triangles by connecting non-adjacent vertices.
4.  **Dyck Paths**: The number of paths from $(0,0)$ to $(2n, 0)$ using only steps $(1,1)$ and $(1,-1)$ that never drop below the x-axis.
5.  **Mountain Ranges**: Similar to Dyck paths, paths from $(0,0)$ to $(n,n)$ in a grid using steps $(1,0)$ and $(0,1)$ that never cross the diagonal $y=x$.

### 1.4. Implementation (Modular Arithmetic)
In contests, you often need $C_n \pmod M$.

```cpp
long long nCr(int n, int r, int mod) {
    if (r < 0 || r > n) return 0;
    // Precompute factorials and their inverses
    return (fact[n] * invFact[r] % mod * invFact[n-r] % mod);
}

long long catalan(int n, int mod) {
    long long res = nCr(2 * n, n, mod);
    // C_n = binom(2n, n) / (n + 1)
    // Division by (n+1) is multiplication by modular inverse
    return res * power(n + 1, mod - 2, mod) % mod;
}
```

---

## 2. Fibonacci Numbers

$0, 1, 1, 2, 3, 5, 8, 13, 21, 34, \dots$

### 2.1. Recurrence
$F_n = F_{n-1} + F_{n-2}$, with $F_0=0, F_1=1$.

### 2.2. Matrix Exponentiation ($O(\log N)$)
To find $F_n$ for $n = 10^{18}$, DP is too slow.
$$ \begin{pmatrix} F_{n+1} \\ F_n \end{pmatrix} = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix} \begin{pmatrix} F_n \\ F_{n-1} \end{pmatrix} \implies \begin{pmatrix} F_{n+1} \\ F_n \end{pmatrix} = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^n \begin{pmatrix} F_1 \\ F_0 \end{pmatrix} $$ 

```cpp
typedef vector<vector<long long>> matrix;

matrix multiply(matrix A, matrix B, int mod) {
    matrix C(2, vector<long long>(2));
    for (int i = 0; i < 2; i++)
        for (int j = 0; j < 2; j++)
            for (int k = 0; k < 2; k++)
                C[i][j] = (C[i][j] + A[i][k] * B[k][j]) % mod;
    return C;
}

matrix power(matrix A, long long p, int mod) {
    matrix res = {{1, 0}, {0, 1}};
    while (p > 0) {
        if (p & 1) res = multiply(res, A, mod);
        A = multiply(A, A, mod);
        p >>= 1;
    }
    return res;
}
```

### 2.3. Binet's Formula
$F_n = \frac{\phi^n - \psi^n}{\sqrt{5}}$, where $\phi = \frac{1+\sqrt{5}}{2}$ and $\psi = \frac{1-\sqrt{5}}{2}$.
Note: Useful for estimation, but floating point precision fails for large $n$.

---

## 3. Stirling Numbers

### 3.1. Stirling Numbers of the Second Kind $S(n, k)$
The number of ways to partition a set of $n$ elements into $k$ non-empty subsets.
*   **Recurrence**: $S(n, k) = k \cdot S(n-1, k) + S(n-1, k-1)$.
    *   Logic: The $n$-th element either joins one of the $k$ existing sets (in $k$ ways) or starts a new set.
*   **Closed form**: $S(n, k) = \frac{1}{k!} \sum_{j=0}^k (-1)^{k-j} \binom{k}{j} j^n$.

### 3.2. Stirling Numbers of the First Kind
The number of ways to arrange $n$ elements into $k$ disjoint cycles.
*   **Recurrence**: $c(n, k) = (n-1)c(n-1, k) + c(n-1, k-1)$.

---

## 4. Bell Numbers $B_n$

The total number of ways to partition a set of $n$ elements into any number of subsets.
$$B_n = \sum_{k=0}^n S(n, k)$$
Recurrence: $B_{n+1} = \sum_{k=0}^n \binom{n}{k} B_k$.

---

## 5. Bernoulli Numbers $B_n^+$

Used in number theory for sum of powers: $\sum_{i=1}^n i^k$ (Faulhaber's Formula).
They are also related to the Riemann Zeta function.

---

## 6. Practice Problems
1.  **CSES Bracket Sequences I**: Count valid parentheses (Catalan).
2.  **CSES Fibonacci Numbers**: Large $n$ (Matrix Exp).
3.  **Codeforces 911D**: Inversions and permutations (Cycles).
4.  **UVa 10229**: Modular Fibonacci.

## 7. Conclusion
Recognizing these sequences is often the "Aha!" moment in a competitive programming problem. If you see a counting problem with small $n$, try computing the first few values ($1, 1, 2, 5, \dots$) and check OEIS or your knowledge of these sequences.
