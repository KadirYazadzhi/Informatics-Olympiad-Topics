# 🎲 Advanced Combinatorics: Principles and Counting Techniques

Combinatorics is the study of counting, arrangement, and permutation. In competitive programming, advanced combinatorics involves moving beyond simple binomial coefficients to handle complex constraints, symmetries, and modular arithmetic at scale.

---

## 1. Principle of Inclusion-Exclusion (PIE)

The Principle of Inclusion-Exclusion is a technique to compute the size of the union of multiple sets.

### 1.1. Two and Three Sets
For two sets: $|A \cup B| = |A| + |B| - |A \cap B|$
For three sets: $|A \cup B \cup C| = |A| + |B| + |C| - (|A \cap B| + |A \cap C| + |B \cap C|) + |A \cap B \cap C|$

### 1.2. General Formula
For $n$ sets $A_1, A_2, \dots, A_n$:
$$| \bigcup_{i=1}^n A_i | = \sum_{i} |A_i| - \sum_{i < j} |A_i \cap A_j| + \sum_{i < j < k} |A_i \cap A_j \cap A_k| - \dots + (-1)^{n-1} |A_1 \cap \dots \cap A_n|$$

### 1.3. Application: Derangements ($D_n$)
A derangement is a permutation of the elements of a set in which no element appears in its original position.
Using PIE, we define "bad" sets $S_i$ where element $i$ is in its correct position. We want to find $n! - |S_1 \cup S_2 \cup \dots \cup S_n|$.
The result is:
$$D_n = n! \sum_{k=0}^n \frac{(-1)^k}{k!}$$Recurrence: $D_n = (n-1)(D_{n-1} + D_{n-2})$.

---

## 2. Stars and Bars (Balls and Urns)

This technique counts the number of ways to put $n$ identical items into $k$ distinct bins.

### 2.1. Theorem 1 (Bins can be empty)
The number of ways is:
$$\binom{n + k - 1}{k - 1}$$
*Proof*: Imagine $n$ stars and $k-1$ bars. The number of permutations of these $n+k-1$ objects is the answer.

### 2.2. Theorem 2 (Bins must be non-empty)
The number of ways is:
$$\binom{n - 1}{k - 1}$$
*Proof*: Place $k-1$ bars in the $n-1$ gaps between stars.

---

## 3. Burnside's Lemma

Burnside's Lemma is used to count distinct objects under a group of symmetries (like rotations or reflections).

**Formula**:
$$N = \frac{1}{|G|} \sum_{g \in G} |X^g|$$
*   $G$: The group of symmetries.
*   $|G|$: Number of symmetries.
*   $|X^g|$: The number of configurations that remain unchanged (fixed) when symmetry $g$ is applied.

### Example: Necklace Coloring
How many distinct necklaces of $n$ beads can be made with $m$ colors, considering only rotations?
The rotations are by $0, 1, \dots, n-1$ positions.
For a rotation by $k$, the number of fixed colorings is $m^{\gcd(k, n)}$.
$$N = \frac{1}{n} \sum_{k=0}^{n-1} m^{\gcd(k, n)}$$

---

## 4. Polya Enumeration Theorem (PET)
An extension of Burnside's Lemma that uses generating functions to count objects with a specific distribution of colors (e.g., "necklaces with 3 red and 2 blue beads").

---

## 5. Lucas Theorem

To calculate $\binom{n}{k} \pmod p$ where $p$ is a small prime, but $n$ and $k$ are very large (up to $10^{18}$).
1.  Express $n$ and $k$ in base $p$:
    $n = n_k p^k + \dots + n_1 p + n_0$
    $k = k_k p^k + \dots + k_1 p + k_0$
2.  The formula:
    $$\binom{n}{k} \equiv \prod_{i=0}^k \binom{n_i}{k_i} \pmod p$$

```cpp
long long lucas(long long n, long long k, int p) {
    if (k == 0) return 1;
    return (lucas(n / p, k / p, p) * nCr_small(n % p, k % p, p)) % p;
}
```

---

## 6. Cayley's Formula
The number of spanning trees in a complete labeled graph $K_n$ is $n^{n-2}$.
This is also the number of ways to form a tree with $n$ labeled vertices.
Can be proven using **Prüfer sequences**, which establish a bijection between trees and sequences of length $n-2$.

---

## 7. Practice Problems
1.  **CSES Distributing Apples**: Stars and Bars.
2.  **CSES Christmas Party**: Derangements.
3.  **Codeforces 451E**: Devu and Flowers (PIE with large constraints).
4.  **UVa 10601**: Cubes (Burnside's Lemma on a cube).

## 8. Conclusion
Advanced combinatorics requires a shift from manual counting to applying high-level theorems. Mastering PIE and Stars and Bars covers 80% of contest problems, while Burnside's and Lucas Theorem handle the remaining "expert" level challenges.
