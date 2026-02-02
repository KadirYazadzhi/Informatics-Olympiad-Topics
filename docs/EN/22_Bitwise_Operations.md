# 💻 Bitwise Operations: Optimizations and Algorithms

## 🎯 Introduction

Bitwise operations are among the fastest operations a processor can perform. They work directly with the binary representation of numbers and are key to optimizing algorithms in competitive programming. Knowledge of bitwise techniques can reduce the complexity of a solution from $O(N^2)$ to $O(N \cdot \log N)$ or even to $O(N)$.

### Basic Bitwise Operations

```cpp
int a = 5;  // Binary: 0101
int b = 3;  // Binary: 0011

// AND (&): Both bits must be 1
int c = a & b;  // 0001 = 1

// OR (|): At least one of the bits must be 1
int d = a | b;  // 0111 = 7

// XOR (^): The bits must be different
int e = a ^ b;  // 0110 = 6

// NOT (~): Inverts all bits
int f = ~a;     // ...11111010 (complement)

// Shift left (<<): Multiplication by 2^k
int g = a << 2; // 10100 = 20 (5 * 4)

// Shift right (>>): Division by 2^k
int h = a >> 1; // 0010 = 2 (5 / 2)
```

### Useful Tricks

```cpp
// Check whether n is a power of 2
bool isPowerOfTwo = (n > 0) && ((n & (n - 1)) == 0);

// Setting the i-th bit to 1
n |= (1 << i);

// Setting the i-th bit to 0
n &= ~(1 << i);

// Toggling the i-th bit
n ^= (1 << i);

// Checking whether the i-th bit is set
bool isSet = (n & (1 << i)) != 0;

// Isolating the lowest set bit
int lowestBit = n & (-n);

// Removing the lowest set bit
n &= (n - 1);
```

---

## 🛠️ Built-in Functions in GCC

The GCC compiler provides functions that use processor instructions for bitwise operations (extremely fast, $O(1)$).

- `__builtin_popcount(n)`: Number of set bits (1) in `n`.
- `__builtin_clz(n)`: Count Leading Zeros. Useful for finding the most significant bit ($\lfloor \log_2 n \rfloor = 31 - \text{clz}(n)$).
- `__builtin_ctz(n)`: Count Trailing Zeros. Equivalent to finding the least significant bit.
- `__builtin_parity(n)`: Returns 1 if the number of ones is odd, else 0.
- For `long long`, use the suffix `ll` (e.g., `__builtin_popcountll`).

**Practical examples:**

```cpp
int n = 23;  // Binary: 10111

// Number of ones: 4
int ones = __builtin_popcount(n);

// Most significant bit (position): log2(23) = 4
int msb = 31 - __builtin_clz(n);

// Least significant set bit (trailing zeros): 0
int lsb = __builtin_ctz(n);

// Check for parity of ones
int par = __builtin_parity(n);  // 0 (even count)
```

**Application: Iterating over bits**

```cpp
// Iteration through all set bits
for (int mask = n; mask > 0; mask &= (mask - 1)) {
    int bit = __builtin_ctz(mask);  // Position of the least significant bit
    // Process the bit
}

// Or a faster variant
for (int mask = n; mask; mask ^= (mask & -mask)) {
    int bit = __builtin_ctz(mask);
    // Process
}
```

---

## 🔢 Linear Basis (XOR Basis)

A linear basis is a minimum set of numbers whose XOR combinations can generate any number that can be generated from the original set $S$.
- The size of the basis is at most $\log(\max(S)) \approx 60$.
- Allows for quick checking whether a number $X$ can be obtained via XOR of a subset of $S$.
- Allows for finding the **maximum XOR** of a subset.

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

An array $A$ with $2^N$ elements is given. We want to calculate for every `mask`:
$$F[mask] = \sum_{sub \subseteq mask} A[sub]$$

The naive approach is $O(4^N)$. SOS DP does it in $O(N \cdot 2^N)$.

**Idea**: We process the bits one by one. $dp[mask][i]$ is the sum of subsets of `mask` that differ from `mask` only in the first $i$ bits.

**Optimized implementation (in-place):**
```cpp
for (int i = 0; i < N; ++i) { // For each bit
    for (int mask = 0; mask < (1 << N); ++mask) {
        if (mask & (1 << i)) { // If the i-th bit is set
            F[mask] += F[mask ^ (1 << i)];
        }
    }
}
```

---

## 🎭 Bitmasks for Optimization (Bitset)

`std::bitset<N>` is a template in C++ that allows operations with arrays of bits, using 1 bit of memory per element. Operations `&`, `|`, `^` over entire bitsets are $N/64$ times faster than a loop.

**Application - Knapsack Problem:**
Checking which sums are reachable.
```cpp
std::bitset<100001> reach;
reach[0] = 1;
for (int x : items) {
    reach |= (reach << x); // All current sums are shifted by x
}
```
Complexity: $O(N \cdot W / 64)$.

---

---


## 🎯 Submask Enumeration

Often it is necessary to iterate over all submasks of a given bitmask. The naive approach is $O(2^N)$, but if we want only the submasks of a specific mask, there is a faster way.

**Iteration over all submasks of a mask:**

```cpp
// Iterates over all submasks of mask (including the empty one)
for (int sub = mask; sub > 0; sub = (sub - 1) & mask) {
    // Process sub
}
// Don't forget the empty set (0) if needed
```

**Iteration over the complements of submasks:**

```cpp
// For each submask, find its complement
for (int sub = mask; sub > 0; sub = (sub - 1) & mask) {
    int complement = mask ^ sub;
    // Process
}
```

**Complexity:** The total time is $O(3^N)$ if we iterate over the submasks of all $2^N$ masks, because each element can be in the mask, in the submask, or in neither.

---

## 🎨 Application: Checking Mask Properties

**Example 1: Checking whether two masks are disjoint**

```cpp
bool areDisjoint(int mask1, int mask2) {
    return (mask1 & mask2) == 0;
}
```

**Example 2: Union and Intersection**

```cpp
int unionMask = mask1 | mask2;      // Union
int intersectMask = mask1 & mask2;  // Intersection
int diffMask = mask1 & (~mask2);    // Difference (elements in mask1 but not in mask2)
```

**Example 3: Generating all k-bit masks**

```cpp
// Generates all masks with exactly k set bits
void generateKBitMasks(int n, int k) {
    int mask = (1 << k) - 1;  // First mask: k ones on the right
    while (mask < (1 << n)) {
        // Process mask
        
        // Gosper's Hack for the next mask
        int c = mask & -mask;
        int r = mask + c;
        mask = (((r ^ mask) >> 2) / c) | r;
    }
}
```

---

## 💡 Optimizations with Bitmasks in Tasks

### Task: Traveling Salesman Problem (TSP)

Finding the shortest path that visits all cities exactly once.

**DP solution:** $dp[mask][i]$ = the minimum cost to visit the cities in the mask and finish in city $i$.

```cpp
const int INF = 1e9;
int n;  // Number of cities
int dist[20][20];  // Distances between cities
int dp[1 << 20][20];

int tsp() {
    for (int i = 0; i < (1 << n); i++)
        for (int j = 0; j < n; j++)
            dp[i][j] = INF;
    
    dp[1][0] = 0;  // Start from city 0
    
    for (int mask = 1; mask < (1 << n); mask++) {
        for (int i = 0; i < n; i++) {
            if (!(mask & (1 << i))) continue;  // i must be in the mask
            
            for (int j = 0; j < n; j++) {
                if (mask & (1 << j)) continue;  // j must not have been visited
                
                int newMask = mask | (1 << j);
                dp[newMask][j] = min(dp[newMask][j], 
                                     dp[mask][i] + dist[i][j]);
            }
        }
    }
    
    // Return to the starting city
    int ans = INF;
    int fullMask = (1 << n) - 1;
    for (int i = 0; i < n; i++) {
        ans = min(ans, dp[fullMask][i] + dist[i][0]);
    }
    return ans;
}
```

### Task: Subset Sum with Bitmasks

Checking which sums can be formed from elements of an array.

```cpp
bitset<100001> possible;
possible[0] = 1;

for (int x : arr) {
    possible |= (possible << x);
}

// possible[s] is true if the sum s is reachable
```

---

## 🧮 Bitwise Operations and GCD/LCM

**Optimization of GCD with bitwise operations (Binary GCD):**

```cpp
int gcd(int a, int b) {
    if (a == 0) return b;
    if (b == 0) return a;
    
    // Removal of common powers of 2
    int shift = __builtin_ctz(a | b);
    a >>= __builtin_ctz(a);
    
    do {
        b >>= __builtin_ctz(b);
        if (a > b) swap(a, b);
        b -= a;
    } while (b != 0);
    
    return a << shift;
}
```

This is faster than the classical Euclidean algorithm on some architectures because it uses bitwise operations instead of division.

---

## 📝 Practice Tasks

1. **Codeforces 165E** - Compatible Numbers (Linear Basis)
2. **Codeforces 449D** - Jzzhu and Numbers (SOS DP)
3. **Codeforces 1208F** - Bits and Pieces (Bitwise operations)
4. **SPOJ TSUM** - Triple Sums (SOS DP variation)
5. **AtCoder DP Contest Z** - Frog 3 (Convex Hull Trick with bitset)

---

## 🏁 Conclusion

Bitwise operations are the essence of machine optimization. Linear Basis is mandatory for tasks with XOR maximization, and SOS DP is a standard technique for tasks on subsets (frequently encountered in Codeforces Div1/Div2 C-D). Mastering these techniques can turn a task impossible to solve into a trivial one.

**Key points to remember:**
- Bitwise operations are $O(1)$ and very fast.
- Use the `__builtin_*` functions instead of writing your own.
- SOS DP reduces complexity from $O(4^N)$ to $O(N \cdot 2^N)$.
- Linear Basis allows XOR optimizations with size $O(\log \max(A))$.
- Bitset operations are 64 times faster than ordinary loops.
- Always look for a bitwise interpretation of the task before writing an $O(N^2)$ solution.
