# 🔢 Special Number Sequences

Combinatorics often involves famous sequences.

## 1. Catalan Numbers

Sequence: $1, 1, 2, 5, 14, 42, 132, \dots$
Formula: $C_n = \frac{1}{n+1} \binom{2n}{n}$

### Applications
$C_n$ counts:
1.  **Valid Parentheses** of length $2n$.
2.  **Binary Trees** with $n$ nodes.
3.  **Triangulations** of $(n+2)$-gon.
4.  **Dyck Paths** (Grid paths not crossing diagonal).

## 2. Fibonacci Numbers

$F_0=0, F_1=1, F_n = F_{n-1} + F_{n-2}$.

### Matrix Exponentiation
Compute $F_n$ for large $n$ ($10^{18}$) in $O(\log n)$.
$$ \begin{pmatrix} F_{n+1} \\ F_n \end{pmatrix} = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^n \begin{pmatrix} F_1 \\ F_0 \end{pmatrix} $$

## 3. Stirling Numbers

### Second Kind $S(n, k)$
Ways to partition $n$ items into $k$ non-empty subsets.
Recurrence: $S(n, k) = k S(n-1, k) + S(n-1, k-1)$.

### First Kind
Ways to arrange $n$ items into $k$ cycles.

## 4. Bell Numbers
Total ways to partition $n$ items.
$B_n = \sum_{k=0}^n S(n, k)$.

## 5. Derangements
Permutations of $n$ elements where no element appears in its original position.
$D_n = (n-1)(D_{n-1} + D_{n-2})$.

## 6. Practice
1.  **SPOJ CATALAN**: Compute $C_n$.
2.  **UVa 10450**: World Cup Noise (Fibonacci).