# 🎲 Random Numbers and Randomization

High-quality random numbers are crucial for randomized algorithms, hashing, and simulations.

## 1. Why `rand()` is bad

The standard `rand()` in C/C++ (`<cstdlib>`) has flaws:
1.  **Small Range**: Often up to 32767 (`RAND_MAX`). `rand() % 100000` is impossible.
2.  **Bias**: Modulo arithmetic introduces bias.
3.  **Predictability**: Linear Congruential Generator (LCG) is weak.

## 2. The Modern Way: `<random>` (C++11)

Use the **Mersenne Twister** engine (`mt19937`).
*   Period: $2^{19937}-1$.
*   Fast and statistically strong.

### 2.1. Initialization (Seeding)
To get different sequences each run, seed with high-resolution time. In Codeforces, use a combination of time and heap address to avoid anti-hash hacks.

```cpp
#include <iostream>
#include <vector>
#include <random>
#include <chrono>
#include <algorithm>

using namespace std;

// Initialize generator
mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
// For 64-bit numbers: mt19937_64

// Function for range [L, R]
int get_rand(int l, int r) {
    uniform_int_distribution<int> dist(l, r);
    return dist(rng);
}

int main() {
    cout << get_rand(1, 100) << endl;

    // Shuffling a vector
    vector<int> v = {1, 2, 3, 4, 5};
    shuffle(v.begin(), v.end(), rng); // Do NOT use random_shuffle
    
    return 0;
}
```

## 3. Applications

### 3.1. Hashing
When using Rolling Hash for strings, use a **random base** (and potentially random mod) to make it impossible for test creators to generate collisions specifically for your code.

```cpp
int BASE = get_rand(300, 1000);
```

### 3.2. Randomized QuickSort
Pick a random pivot to guarantee $O(N \log N)$ expected time, avoiding the $O(N^2)$ worst case on sorted arrays.

### 3.3. Treap (Cartesian Tree)
Uses random priorities to keep the tree balanced with high probability. This makes it a powerful alternative to Splay/AVL trees, and much easier to implement (supports Split/Merge).

### 3.4. Stress Testing
If your solution fails but you can't find the bug:
1.  Write a brute force solution.
2.  Write a test generator using `mt19937`.
3.  Loop: Generate test -> Run both -> Compare.

## 4. Distributions
*   `uniform_int_distribution`: Integers in range.
*   `uniform_real_distribution`: Floats in range $[0, 1)$.
*   `bernoulli_distribution`: True/False with probability $p$.

## 5. Conclusion
Forget `rand()`. Always include `<random>` and use `mt19937`. It's the standard for competitive programming.