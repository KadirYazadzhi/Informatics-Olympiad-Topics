# 🎲 Random Numbers and Randomization

Random numbers are indispensable for simulations, hashing, and solving problems via randomized algorithms.

## ⚠️ The Problem with `rand()`
The standard `rand()` function in C++ often has a small range (up to 32767) and poor distribution. **Not recommended for competitions.**

## 🚀 The Modern Way: `<random>`
The library provides high-quality generators like **Mersenne Twister (mt19937)**.

### Initialization
Always use current time for seeding to get different numbers every run.
```cpp
#include <random>
#include <chrono>

// Global generator
mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());

// Function for number in range [l, r]
int getRandom(int l, int r) {
    uniform_int_distribution<int> dist(l, r);
    return dist(rng);
}
```

## 📊 Applications
1. **Randomized QuickSort**: Random pivot selection prevents $O(N^2)$.
2. **Monte Carlo Methods**: Calculating areas or probabilities through mass point generation.
3. **Hashing**: Generating a random base for polynomial hashes (prevents collisions).

---

## 🏁 Conclusion
Always prefer `mt19937` over `rand()`. Quality randomness is the difference between a working algorithm and one that fails on specific tests.
