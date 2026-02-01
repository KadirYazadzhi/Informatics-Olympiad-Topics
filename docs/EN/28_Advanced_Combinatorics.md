# 🎲 Advanced Combinatorics

Beyond basic counting ($N \choose K$), we explore deeper principles.

## 1. Principle of Inclusion-Exclusion (PIE)

To find the size of the union of multiple sets:
$|A \cup B| = |A| + |B| - |A \cap B|$
General: $| \cup A_i | = \sum |A_i| - \sum |A_i \cap A_j| + \sum |A_i \cap A_j \cap A_k| \dots$
The sign is $(-1)^{k-1}$ for an intersection of $k$ sets.

### Application: Derangements
Counting permutations where no element $i$ is at position $i$.
$D_n = n! \sum_{k=0}^n \frac{(-1)^k}{k!}$.

## 2. Stars and Bars

Number of ways to distribute $N$ identical items into $K$ distinct boxes.
*   **Formula**: $\binom{N+K-1}{K-1}$.
*   If each box must have at least one: $\binom{N-1}{K-1}$.

## 3. Burnside's Lemma

Counts **distinct** objects under symmetry (rotation, reflection).
Example: Colorings of a necklace.

**Formula**: $N = \frac{1}{|G|} \sum_{g \in G} |X^g|$
Where $|X^g|$ is the number of configurations fixed by symmetry $g$.

## 4. Cayley's Formula
The number of labeled trees with $N$ vertices is $N^{N-2}$.
Computed using **Prüfer Sequences**.

## 5. Lucas Theorem
To compute $\binom{n}{k} \pmod p$ when $p$ is small prime but $n, k$ are huge.
Write $n, k$ in base $p$: $n = n_k p^k + \dots + n_0$, $k = k_k p^k + \dots + k_0$.
$\binom{n}{k} \equiv \prod \binom{n_i}{k_i} \pmod p$.

## 6. Practice
1.  **CSES Binomial Coefficients**: Basics.
2.  **CSES Creating Strings II**: Multinomial coefficients.
3.  **Codeforces 451E**: PIE.