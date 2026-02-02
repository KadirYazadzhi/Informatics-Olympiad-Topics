# 🎲 Random Numbers and Randomization

## 🎯 Introduction

Generating high-quality random numbers is critically important for many algorithms: randomized data structures (Treaps), hashing, Monte Carlo simulations, and stress tests. In competitive programming, randomization can turn a deterministic algorithm with a worst-case $O(N^2)$ into an algorithm with an expected time of $O(N \log N)$.

Randomness is not just for statistics - it is a powerful tool for:
- Avoiding specially constructed tests (anti-hash)
- Balancing data structures (Treap, Skip List)
- Randomized algorithms (QuickSort, Karger's Min Cut)
- Probabilistic structures (Bloom Filter, HyperLogLog)
- Stress-testing solutions

---

## 1. Why is `rand()` a poor choice?

The traditional C function `rand()` (from `<cstdlib>`) has several serious problems:
1.  **Small range**: `RAND_MAX` is often only 32767 (`SHRT_MAX` = $2^{15}-1$). If you need a number up to $10^9$, the formula `rand() % N` is useless and gives a strongly skewed distribution.
2.  **Poor distribution**: The remainder of division (`%`) introduces "bias" (distortion) if `RAND_MAX` is not exactly divisible by $N$. For example, if `RAND_MAX = 32767` and we want a number from 0 to 10000, some numbers will appear more often than others.
3.  **Predictability**: The Linear Congruential Generator (LCG) behind `rand()` is statistically weak. The sequence can be predicted after a few observations.
4.  **Lack of thread-safety**: In multi-threaded programs, `rand()` is not thread-safe.

### Example of the bias problem:

```cpp
// BAD: Non-uniform distribution
int bad_random(int n) {
    return rand() % n;  // Bias if RAND_MAX % n != n-1
}

// Why is it bias?
// If RAND_MAX = 32767 and n = 10000:
// Numbers 0-767 will appear 4 times, while 768-9999 only 3 times.
```

---

## 2. The Modern Approach: C++11 `<random>`

The `<random>` library introduces the **Mersenne Twister** generator, which is the industry standard for non-scientific applications.

### 2.1. Generator (`mt19937`)
Generates 32-bit numbers with a huge period ($2^{19937}-1$). For 64-bit numbers, use `mt19937_64`.

### 2.2. Seed (Initial Value)
If you do not specify a seed, the generator will provide the same sequence every time (useful for debugging, harmful for submission).
The best way to seed in competitions is a combination of time and memory address (to avoid hacks on Codeforces).

```cpp
#include <iostream>
#include <vector>
#include <random>
#include <chrono>
#include <algorithm>

using namespace std;

// Initialization of a high-quality generator
mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
// For 64-bit numbers:
// mt19937_64 rng64(chrono::steady_clock::now().time_since_epoch().count());

// Helper function for range [L, R]
int get_rand(int l, int r) {
    uniform_int_distribution<int> dist(l, r);
    return dist(rng);
}

int main() {
    // 1. Generating a number
    cout << "Random [1, 100]: " << get_rand(1, 100) << endl;

    // 2. Shuffling an array (Shuffle)
    vector<int> v = {1, 2, 3, 4, 5};
    // random_shuffle is deprecated! Use shuffle with your rng.
    shuffle(v.begin(), v.end(), rng);

    cout << "Shuffled: ";
    for (int x : v) cout << x << " ";
    cout << endl;

    return 0;
}
```

## 3. Applications of Randomization

### 3.1. Protection Against "Anti-Hash" Tests
In string hashing (Rolling Hash), if you use a fixed base (e.g., 31) and modulo, someone can generate collisions specifically for them.
**Solution**: Generate a random base at the beginning of the program.

```cpp
int BASE = get_rand(256, 1000);
```

### 3.2. Randomized QuickSort
To avoid $O(N^2)$ on a sorted array, choose the pivot randomly. `std::sort` usually takes care of this, but if you are writing your own QuickSelect (for the k-th element), it is mandatory.

### 3.3. Treap (Cartesian Tree)
A Cartesian tree uses random priorities for each node to keep the tree balanced with high probability ($O(\log N)$).

### 3.4. Stress Tests (Generator)
When your solution gives a "Wrong Answer" and you don't see why:
1.  Write a "slow" but sure solution (Brute force).
2.  Write a generator of random tests (using `mt19937`).
3.  Run a loop that generates a test, runs both solutions, and compares the results.

## 4. Distributions

Besides `uniform_int_distribution` (uniform), `<random>` supports:
*   `uniform_real_distribution<double>(0.0, 1.0)`: For probabilities.
*   `normal_distribution`: Gaussian distribution (less common in competitions).
*   `bernoulli_distribution`: Heads/tails with probability $p$.

### Examples of different distributions:

```cpp
mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());

// Real numbers from 0.0 to 1.0
uniform_real_distribution<double> prob_dist(0.0, 1.0);
double probability = prob_dist(rng);

// Normal distribution (Gaussian)
normal_distribution<double> norm_dist(0.0, 1.0);  // mean=0, stddev=1
double gaussian_val = norm_dist(rng);

// Bernoulli (coin)
bernoulli_distribution coin(0.5);  // p=0.5
bool heads = coin(rng);

// Exponential distribution
exponential_distribution<double> exp_dist(1.0);
double exp_val = exp_dist(rng);
```

---

## 5. Randomized Algorithms

### 5.1. QuickSelect with Randomization

Finding the k-th smallest element with expected complexity $O(N)$.

```cpp
mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());

int partition(vector<int>& arr, int left, int right) {
    // We choose a random pivot
    uniform_int_distribution<int> dist(left, right);
    int pivotIndex = dist(rng);
    swap(arr[pivotIndex], arr[right]);
    
    int pivot = arr[right];
    int i = left - 1;
    
    for (int j = left; j < right; j++) {
        if (arr[j] <= pivot) {
            i++;
            swap(arr[i], arr[j]);
        }
    }
    
    swap(arr[i + 1], arr[right]);
    return i + 1;
}

int quickSelect(vector<int>& arr, int left, int right, int k) {
    if (left == right) return arr[left];
    
    int pivotIndex = partition(arr, left, right);
    
    if (k == pivotIndex) {
        return arr[k];
    } else if (k < pivotIndex) {
        return quickSelect(arr, left, pivotIndex - 1, k);
    } else {
        return quickSelect(arr, pivotIndex + 1, right, k);
    }
}
```

### 5.2. Reservoir Sampling

Choosing a random element from a stream of unknown length.

```cpp
// Choosing k elements from a stream
vector<int> reservoirSample(vector<int>& stream, int k) {
    vector<int> reservoir(k);
    
    // Fill the first k elements
    for (int i = 0; i < k; i++) {
        reservoir[i] = stream[i];
    }
    
    // Process the remaining elements
    for (int i = k; i < stream.size(); i++) {
        uniform_int_distribution<int> dist(0, i);
        int j = dist(rng);
        
        if (j < k) {
            reservoir[j] = stream[i];
        }
    }
    
    return reservoir;
}
```

### 5.3. Monte Carlo Method

Approximate calculation through random simulations.

```cpp
// Approximate calculation of PI
double estimatePi(int samples) {
    int inside_circle = 0;
    uniform_real_distribution<double> dist(0.0, 1.0);
    
    for (int i = 0; i < samples; i++) {
        double x = dist(rng);
        double y = dist(rng);
        
        if (x*x + y*y <= 1.0) {
            inside_circle++;
        }
    }
    
    return 4.0 * inside_circle / samples;
}
```

---

## 6. Stress Testing

One of the most powerful tools for finding bugs.

### 6.1. Structure of a Stress Test

```cpp
#include <iostream>
#include <cassert>
#include <random>
#include <chrono>
using namespace std;

mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());

// Your solution (may have bugs)
int fastSolution(vector<int>& arr) {
    // ...
}

// Slow but sure solution (brute force)
int slowSolution(vector<int>& arr) {
    // ...
}

// Random test generator
vector<int> generateTest(int n, int maxVal) {
    vector<int> arr(n);
    uniform_int_distribution<int> dist(1, maxVal);
    
    for (int i = 0; i < n; i++) {
        arr[i] = dist(rng);
    }
    
    return arr;
}

int main() {
    int testCount = 1000;
    
    for (int test = 1; test <= testCount; test++) {
        // Generate random test
        int n = rng() % 100 + 1;  // n from 1 to 100
        vector<int> arr = generateTest(n, 1000);
        
        // Run both solutions
        int fast = fastSolution(arr);
        int slow = slowSolution(arr);
        
        // Compare
        if (fast != slow) {
            cout << "WA on test " << test << endl;
            cout << "n = " << n << endl;
            cout << "arr = ";
            for (int x : arr) cout << x << " ";
            cout << endl;
            cout << "Fast: " << fast << ", Slow: " << slow << endl;
            return 1;
        }
        
        if (test % 100 == 0) {
            cout << "Passed " << test << " tests" << endl;
        }
    }
    
    cout << "All tests passed!" << endl;
    return 0;
}
```

### 6.2. Generator for Different Cases

```cpp
// Generator for strings
string randomString(int length, string alphabet = "abcdefghijklmnopqrstuvwxyz") {
    string result;
    uniform_int_distribution<int> dist(0, alphabet.size() - 1);
    
    for (int i = 0; i < length; i++) {
        result += alphabet[dist(rng)];
    }
    
    return result;
}

// Generator for trees
vector<pair<int, int>> randomTree(int n) {
    vector<pair<int, int>> edges;
    
    for (int i = 2; i <= n; i++) {
        uniform_int_distribution<int> dist(1, i - 1);
        int parent = dist(rng);
        edges.push_back({parent, i});
    }
    
    return edges;
}

// Generator for graphs
vector<pair<int, int>> randomGraph(int n, int m) {
    set<pair<int, int>> edges_set;
    uniform_int_distribution<int> dist(1, n);
    
    while (edges_set.size() < m) {
        int u = dist(rng);
        int v = dist(rng);
        if (u != v) {
            edges_set.insert({min(u, v), max(u, v)});
        }
    }
    
    return vector<pair<int, int>>(edges_set.begin(), edges_set.end());
}
```

---

## 🏁 Conclusion

Forget about `rand()` and `srand(time(0))`. Use `mt19937` for speed, quality, and reliability.

**Summary of Key Points:**

1. **Always use mt19937**: Better period, better distribution, more difficult to predict.
2. **Seed correctly**: Time + address or `random_device` for a cryptographically random seed.
3. **Use distributions**: `uniform_int_distribution` instead of `%` for proper distribution.
4. **Shuffle with std::shuffle**: Always pass your rng as the third parameter.
5. **Stress-test**: The best way to find bugs before the judge does.
6. **Randomize critical parameters**: Hash bases, pivots in QuickSort, priorities in Treap.

**Common Errors:**

```cpp
// WRONG: Using rand()
int x = rand() % n;

// CORRECT: Using mt19937
uniform_int_distribution<int> dist(0, n-1);
int x = dist(rng);

// WRONG: Poor seed
mt19937 rng(1);  // Constant seed

// CORRECT: Good seed
mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());

// WRONG: Deprecated shuffle
random_shuffle(v.begin(), v.end());

// CORRECT: Modern shuffle
shuffle(v.begin(), v.end(), rng);
```

**Applications in Competitions:**

1. **Anti-hash protection**: Randomize constants in hash functions.
2. **Treap**: Randomized priorities for balancing.
3. **QuickSelect/QuickSort**: Randomized pivot.
4. **Geometry**: Small random perturbations to avoid edge cases.
5. **Stress testing**: Automatic generation of tests.

**Additional Resources:**

- C++ Reference: `<random>` library.
- "The Art of Computer Programming" Vol. 2 (Knuth) - The seminal book on random numbers.
- Codeforces blogs on anti-hash techniques.
- Stress testing examples in GitHub repos.

Remember: Randomization is not a replacement for a correct algorithm, but it can turn a good algorithm into a great one or save you from malicious tests!
