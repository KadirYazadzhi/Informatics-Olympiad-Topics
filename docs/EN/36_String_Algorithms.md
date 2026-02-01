# 🧵 String Algorithms: Efficient Pattern Matching and Hashing

String processing is a fundamental area of competitive programming. Standard searching ($O(NM)$) is often too slow for modern constraints ($N, M \le 10^6$). We require linear time algorithms.

---

## 1. Polynomial Rolling Hash

Hashing allows us to compare strings or substrings in $O(1)$ time by representing them as large integers.

### 1.1. The Hash Function
For a string $S$ of length $n$:
$$H(S) = (S[0] \cdot P^{n-1} + S[1] \cdot P^{n-2} + \dots + S[n-1] \cdot P^0) \pmod M$$
*   **P (Base)**: Usually a prime slightly larger than the alphabet size (e.g., 31, 53, or a random prime around 300).
*   **M (Modulo)**: A large prime like $10^9+7$, $10^9+9$, or $2^{61}-1$.

### 1.2. Substring Comparison
By precomputing prefix hashes and powers of $P$, we can find the hash of $S[i \dots j]$ in $O(1)$:
$$H(S[i \dots j]) = (PrefixH[j+1] - PrefixHash[i] \cdot P^{j-i+1}) \pmod M$$

**Double Hashing**: Use two different $(P, M)$ pairs to virtually eliminate collisions.

---

## 2. Knuth-Morris-Pratt (KMP) Algorithm

The KMP algorithm performs linear time pattern matching by avoiding redundant comparisons.

### 2.1. Prefix Function ($\pi$)
The prefix function $\pi[i]$ is the length of the longest proper prefix of $S[0 \dots i]$ that is also a suffix of $S[0 \dots i]$.

```cpp
vector<int> prefix_function(string s) {
    int n = s.length();
    vector<int> pi(n);
    for (int i = 1; i < n; i++) {
        int j = pi[i-1];
        while (j > 0 && s[i] != s[j])
            j = pi[j-1];
        if (s[i] == s[j]) j++;
        pi[i] = j;
    }
    return pi;
}
```

### 2.2. Periodicity
A string $S$ has a period $T$ if $S[i] = S[i+T]$ for all $i$. The smallest period of a string is $n - \pi[n-1]$ if $n$ is divisible by $n - \pi[n-1]$.

---

## 3. Z-Algorithm

The Z-algorithm computes the **Z-function**, where $Z[i]$ is the length of the longest common prefix (LCP) between $S$ and the suffix of $S$ starting at $i$.
Like KMP, it allows for $O(N)$ pattern matching by constructing the string $P + \# + T$.

---

## 4. Manacher's Algorithm

Manacher's algorithm finds the longest palindromic substring in $O(N)$ time.
It works by exploiting the symmetry of palindromes and maintaining the "rightmost" palindrome found so far.

---

## 5. Aho-Corasick Algorithm

Used for searching for a **dictionary** of patterns $\{P_1, P_2, \dots, P_k\}$ in a text $T$ in $O(N + \sum |P_i|)$.
It is essentially a BFS-based extension of a **Trie** data structure, adding "failure links" similar to KMP's prefix function.

---

## 6. Practice Problems
1.  **Codeforces 126B**: Password (KMP + Properties).
2.  **CSES String Matching**: Basic pattern search.
3.  **CSES Finding Periods**: Using Z-algorithm or KMP.
4.  **CSES Palindrome Queries**: Rolling Hash with Segment Tree.

## 7. Conclusion
Hashing is the most flexible tool, while KMP and Z-algorithm are deterministic and faster for simple pattern matching. For multiple patterns, Aho-Corasick is the industry standard.
