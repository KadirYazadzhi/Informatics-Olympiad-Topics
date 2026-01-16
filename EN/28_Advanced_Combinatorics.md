# 🔀 Advanced Combinatorics: Generation and Coding

## 🔢 Generating Permutations

### 1. Built-in Next Permutation (How it works?)
Narayana's algorithm to find the lexicographically next permutation:
1. Find the largest index $k$ such that $A[k] < A[k+1]$. (If none, this is the last permutation).
2. Find the largest index $l > k$ such that $A[k] < A[l]$.
3. Swap $A[k]$ and $A[l]$.
4. Reverse the elements from $A[k+1]$ to the end.

**Complexity**: $O(N)$ worst case, but amortized $O(1)$.

### 2. $K$-th Permutation (Decanting Algorithm)
How to find the $K$-th permutation of $N$ elements directly without generating previous ones?
- Use the factorial number system.
- The first element is at index $idx = K / (N-1)!$.
- Remove this element from the list of available numbers.
- Update $K \leftarrow K \% (N-1)!$ and repeat for $N-2$.

```cpp
string getPermutation(int n, int k) {
    vector<int> numbers;
    long long fact = 1;
    for (int i = 1; i <= n; i++) {
        numbers.push_back(i);
        if (i < n) fact *= i;
    }
    k--; // 0-based index
    string ans = "";
    for (int i = n - 1; i >= 0; i--) {
        int idx = k / fact;
        ans += to_string(numbers[idx]);
        numbers.erase(numbers.begin() + idx);
        if (i > 0) {
            k %= fact;
            fact /= i;
        }
    }
    return ans;
}
```

---

## 📊 The Twelvefold Way of Combinatorics

Classification of problems placing $N$ balls into $K$ boxes, depending on:
1. Are balls distinguishable?
2. Are boxes distinguishable?
3. Can boxes be empty?

Examples:
- **Distinguishable balls, Distinguishable boxes**: $K^N$.
- **Indistinguishable balls, Distinguishable boxes**: Stars and Bars $\binom{N+K-1}{K-1}$.
- **Distinguishable balls, Indistinguishable boxes**: Stirling numbers of the second kind $S(N, K)$.
- **Indistinguishable balls, Indistinguishable boxes**: Integer Partition $P(N, K)$.

---

## 🏁 Conclusion

The ability to convert permutations to numbers (rank) and vice versa is key for problems requiring state indexing. The Stars and Bars formula ($\binom{n+k-1}{k-1}$) is one of the most frequently used in competitions.
