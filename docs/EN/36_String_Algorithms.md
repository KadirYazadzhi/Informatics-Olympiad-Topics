# 🧵 String Algorithms

Efficient string processing is crucial. Standard search is $O(NM)$, but we aim for $O(N+M)$.

## 1. Rolling Hash (Polynomial Hash)

Converts strings to integers to allow $O(1)$ comparison.
$H(S) = \sum_{i=0}^{n-1} S[i] \cdot P^i \pmod M$
*   $P$: Base (e.g., 31, 53).
*   $M$: Modulo ($10^9+7$, $10^9+9$).

To avoid collisions, use **Double Hash** (two pairs of $P, M$).

### Substring Hash
$H(S[i \dots j]) = (H[j] - H[i-1]) \cdot P^{-i} \pmod M$.

## 2. KMP Algorithm (Knuth-Morris-Pratt)

Uses the **Prefix Function** $\pi$.
$\pi[i]$ is the length of the longest proper prefix of $S[0 \dots i]$ that is also a suffix of this substring.

### Applications
1.  **Pattern Matching**: Construct string $P + \# + T$. If $\pi[i] == |P|$, match found.
2.  **Periodicity**: Minimum period length is $N - \pi[N-1]$ (if valid).

```cpp
vector<int> prefix_function(string s) {
    int n = s.length();
    vector<int> pi(n);
    for (int i = 1; i < n; i++) {
        int j = pi[i-1];
        while (j > 0 && s[i] != s[j])
            j = pi[j-1];
        if (s[i] == s[j])
            j++;
        pi[i] = j;
    }
    return pi;
}
```

## 3. Z-Algorithm

Computes the **Z-function**.
$Z[i]$ is the length of the longest common prefix between $S$ and the suffix starting at $i$.
Often simpler to understand and implement than KMP for pattern matching.

## 4. Manacher's Algorithm

Finds the longest palindrome centered at each position in $O(N)$.
Handles odd and even length palindromes.

## 5. Comparison

| Algorithm | Complexity | Use Case |
| :--- | :--- | :--- |
| **Hash** | $O(N)$ | General purpose, Palindromes, Comparing substrings |
| **KMP** | $O(N)$ | Single pattern search, Periodicity |
| **Z-Algo** | $O(N)$ | Similar to KMP |
| **Aho-Corasick** | $O(N)$ | Multiple pattern search (Dictionary) |

## 6. Practice
1.  **Codeforces 126B**: Password (KMP properties).
2.  **SPOJ EPALIN**: Extend to Palindrome.
3.  **CSES Word Combinations**: Aho-Corasick / Trie DP.