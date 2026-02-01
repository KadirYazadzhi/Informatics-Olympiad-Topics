# 📝 String Algorithms: Expert Level

## 🔄 Manacher's Algorithm

Finds the longest palindrome centered at each position of the string in $O(N)$. 
- Transform string to handle even and odd palindromes uniformly: `abc` $\to$ `#a#b#c#`.
- Maintain `L` and `R` (boundaries of rightmost found palindrome).
- For current $i$, use mirror value across center of `L-R` to init length, then expand naively.

---

## 🌲 Palindromic Tree (Eertree)

Structure containing **all unique palindromes** in a string as nodes.
- Has two roots: one for length -1 palindromes (odd extensions) and one for length 0 (even).
- Each node has a "suffix link" to the longest proper palindromic suffix.
- Allows adding characters one by one (online) and maintaining palindrome count.
- Complexity: $O(N)$ time and memory.

---

## 🔠 Lyndon Factorization

A string $S$ is a Lyndon word if it is strictly smaller (lexicographically) than all its proper suffixes.
- **Theorem**: Every string has a unique factorization $S = w_1 w_2 \dots w_k$, where $w_i$ are Lyndon words and $w_1 \ge w_2 \ge \dots \ge w_k$.
- **Duval's Algorithm**: Finds factorization in $O(N)$ and $O(1)$ memory.
- **Application**: Finding minimal cyclic shift (it is simply $w_k$ from factorization of $S+S$).

---

## 🏁 Conclusion

Manacher is a classic. Eertree is a relatively new structure (2014) but became standard for palindrome problems because it's much more intuitive than suffix tree manipulations.
