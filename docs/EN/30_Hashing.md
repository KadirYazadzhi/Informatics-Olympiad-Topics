# #️⃣ Hashing and Hash Tables

Hashing maps data to integers for $O(1)$ lookups.

## 1. Hash Tables

### 1.1. `std::unordered_map`
Standard implementation using chaining.
Weakness: Deterministic hashing for integers makes it vulnerable to anti-hash tests ($O(N^2)$).

### 1.2. Custom Hash (SplitMix64)
Essential for Codeforces.

```cpp
struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
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
unordered_map<int, int, custom_hash> m;
```

### 1.3. `gp_hash_table` (PBDS)
Faster than `unordered_map` (uses open addressing with quadratic probing).
Requires `#include <ext/pb_ds/assoc_container.hpp>`.

## 2. String Hashing (Rolling Hash)
Covered in Topic 36.
Allows checking string equality in $O(1)$.

## 3. XOR Hashing (Zobrist Hashing)
Used for checking set equality or subsets.
Assign a random 64-bit number to each element type.
Hash of a set = XOR sum of elements.
*   Add element: `H ^= val`
*   Remove element: `H ^= val` (same!)
*   Two sets are equal iff their XOR sums are equal (with high probability).

## 4. Geometric Hashing
Mapping 2D points to 1D keys (e.g., for finding collinear points).
Often involves careful handling of slopes/fractions.

## 5. Practice
1.  **Codeforces 1418C**: Mortal Kombat Tower.
2.  **CSES Array Description**: Count arrays.