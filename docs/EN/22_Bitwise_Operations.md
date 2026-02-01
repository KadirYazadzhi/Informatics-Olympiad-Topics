# 💻 Bitwise Operations: Optimizations and Algorithms

## 🛠️ Built-in Functions in GCC

The GCC compiler provides functions that use processor instructions for bitwise operations (extremely fast, $O(1)$).

- `__builtin_popcount(n)`: Number of set bits (1s) in `n`.
- `__builtin_clz(n)`: Count Leading Zeros. Useful for finding the most significant bit ($\lfloor \log_2 n \rfloor = 31 - \text{clz}(n)$).
- `__builtin_ctz(n)`: Count Trailing Zeros. Equivalent to finding the least significant bit.
- For `long long`, use suffix `ll` (e.g., `__builtin_popcountll`).

---

## 🔢 Linear Basis (XOR Basis)

A linear basis is a minimal set of numbers whose XOR combinations can generate any number that can be generated from the original set $S$.
- The size of the basis is at most $\log(\max(S)) \approx 60$.
- Allows fast checking if a number $X$ can be formed by XORing a subset of $S$.
- Allows finding the **maximum XOR** of a subset.

**Implementation:**
```cpp
struct LinearBasis {
    std::vector<int> basis;

    void insert(int mask) {
        for (int b : basis) {
            mask = std::min(mask, mask ^ b);
        }
        if (mask > 0) {
            basis.push_back(mask);
            std::sort(basis.rbegin(), basis.rend()); // For convenience
        }
    }

    bool canForm(int mask) {
        for (int b : basis) {
            mask = std::min(mask, mask ^ b);
        }
        return mask == 0;
    }

    int maxXor() {
        int res = 0;
        for (int b : basis) {
            res = std::max(res, res ^ b);
        }
        return res;
    }
};
```

---

## ⚡ SOS DP (Sum Over Subsets)

Given an array $A$ with $2^N$ elements. We want to compute for each mask $mask$:
$$F[mask] = \sum_{sub \subseteq mask} A[sub]$$

Naive approach is $O(4^N)$. SOS DP does it in $O(N \cdot 2^N)$.

**Idea**: Process bits one by one. $dp[mask][i]$ is the sum of subsets of $mask$ that differ from $mask$ only in the first $i$ bits.

**Optimized Implementation (in-place):**
```cpp
for (int i = 0; i < N; ++i) { // For each bit
    for (int mask = 0; mask < (1 << N); ++mask) {
        if (mask & (1 << i)) { // If i-th bit is set
            F[mask] += F[mask ^ (1 << i)];
        }
    }
}
```

---

## 🎭 Bitmasks for Optimization (Bitset)

`std::bitset<N>` is a C++ template allowing operations on bit arrays, using 1 bit of memory per element. Operations `&`, `|`, `^` on entire bitsets are $N/64$ times faster than a loop.

**Application - Knapsack:**
Check which sums are reachable.
```cpp
std::bitset<100001> reach;
reach[0] = 1;
for (int x : items) {
    reach |= (reach << x); // All current sums are shifted by x
}
```
Complexity: $O(N \cdot W / 64)$.

---

## 🏁 Conclusion

Bitwise operations are the essence of machine optimization. Linear Basis is mandatory for XOR maximization tasks, and SOS DP is a standard technique for subset problems (common in Codeforces Div1/Div2 C-D).

```