# #️⃣ Hashing: Custom Tables and String Hashing

Hashing is a technique to map data of arbitrary size to a fixed-size value (a hash). It allows for $O(1)$ average-case lookup and comparison.

---

## 1. Hash Tables

### 1.1. Why `unordered_map` is Dangerous
The C++ `std::unordered_map` uses a deterministic hash function for integers (often the identity function `f(x) = x`). This allows attackers (or test creators) to generate values that all result in the same modulo (collisions), turning $O(1)$ operations into $O(N)$, leading to $O(N^2)$ total runtime.

### 1.2. High-Performance Custom Hash
To prevent hacks, use a random seed and a robust mixer like `SplitMix64`.

```cpp
#include <chrono>
#include <unordered_map>

struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        // http://xorshift.di.unimi.it/splitmix64.c
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }

    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

// Usage
unordered_map<long long, int, custom_hash> safe_map;
```

---

## 2. Policy-Based Data Structures (PBDS) Hashing

`gp_hash_table` is significantly faster than `std::unordered_map` because it uses **Open Addressing** with a better probing strategy.

```cpp
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;

gp_hash_table<int, int, custom_hash> fast_table;
```

---

## 3. String Hashing (Rolling Hash)

Polynomial rolling hash allows checking if two strings (or substrings) are equal in $O(1)$.

### 3.1. Formula
$H(S) = (S[0] \cdot P^{n-1} + S[1] \cdot P^{n-2} + \dots + S[n-1] \cdot P^0) \pmod M$

*   **P (Base)**: A prime number larger than the alphabet size (e.g., 31 for lowercase, 53 for mixed).
*   **M (Modulo)**: A large prime like $10^9+7$ or $10^9+9$.

### 3.2. Substring Hash
$Hash(S[i \dots j]) = (PrefixHash[j+1] - PrefixHash[i] \cdot P^{j-i+1}) \pmod M$

### 3.3. Double Hashing
To reduce the probability of collisions to nearly zero, compute two hashes with different $(P, M)$ pairs. Two strings are considered equal only if **both** hashes match.

---

## 4. Zobrist Hashing (XOR Hashing)

Used for hashing **sets** or board configurations (like Chess).
1.  Assign a unique random 64-bit number to each possible element.
2.  Hash of a set is the **XOR sum** of the numbers assigned to its members.
3.  Adding/removing an element is just `current_hash ^= element_value`.

---

## 5. Tree Hashing

To check if two trees are isomorphic (identical structure):
1.  Compute a hash for each node based on the sorted hashes of its children.
2.  $H(u) = f(\text{sort}(H(v_1), H(v_2), \dots))$.
3.  A robust $f$ is using polynomial values or primes.

---

## 6. Practice Problems
1.  **Codeforces 1418C**: Using Hashing for DP states.
2.  **CSES String Matching**: Rolling Hash.
3.  **Codeforces 271D**: Good Substrings (Counting distinct substrings via Hashing).
4.  **SPOJ TREEISO**: Tree Isomorphism.

## 7. Conclusion
Hashing is a "probabilistic" tool. While it doesn't guarantee correctness like KMP, its flexibility makes it invaluable for complex comparisons in strings, trees, and sets. Always use a `custom_hash` to stay safe.
