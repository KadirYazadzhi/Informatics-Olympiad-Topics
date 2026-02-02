# 🎲 Advanced Combinatorics: Counting Principles and Techniques

Combinatorics in competitive programming often goes beyond simple calculation of permutations and combinations. It requires an understanding of fundamental principles that allow for solving complex tasks with constraints and symmetries.

---

## 1. Inclusion-Exclusion Principle (PIE)

The principle is used to find the size of the union of a finite number of finite sets.

### 1.1. Formulas for 2 and 3 Sets
*   $|A \cup B| = |A| + |B| - |A \cap B|$
*   $|A \cup B \cup C| = |A| + |B| + |C| - (|A \cap B| + |A \cap C| + |B \cap C|) + |A \cap B \cap C|$

### 1.2. General Formula
For $n$ sets $A_1, A_2, \dots, A_n$:
$$\left| \bigcup_{i=1}^n A_i \right| = \sum_{i} |A_i| - \sum_{i < j} |A_i \cap A_j| + \sum_{i < j < k} |A_i \cap A_j \cap A_k| - \dots + (-1)^{n-1} |A_1 \cap \dots \cap A_n|$$

### 1.3. Application: The Derangement Problem ($D_n$)
A derangement is a permutation where no element is in its "correct" place (e.g., letter $i$ is not in envelope $i$).
Using PIE, where the "bad" property $P_i$ is "element $i$ is at position $i$":
$$D_n = n! \sum_{k=0}^n \frac{(-1)^k}{k!}$$ 
Recursion: $D_n = (n-1)(D_{n-1} + D_{n-2})$.

### 1.4. Implementation of Derangements:

```cpp
#include <iostream>
#include <vector>
using namespace std;

const long long MOD = 1e9 + 7;

// Calculating factorial
long long factorial(int n) {
    long long result = 1;
    for (int i = 2; i <= n; i++) {
        result = (result * i) % MOD;
    }
    return result;
}

// Modular inverse
long long modinv(long long a, long long m) {
    long long result = 1, base = a, exp = m - 2;
    while (exp > 0) {
        if (exp % 2 == 1) result = (result * base) % m;
        base = (base * base) % m;
        exp /= 2;
    }
    return result;
}

// Derangements via PIE
long long derangements_PIE(int n) {
    long long fact = factorial(n);
    long long sum = 0;
    long long fact_k = 1;
    
    for (int k = 0; k <= n; k++) {
        long long term = (fact * modinv(fact_k, MOD)) % MOD;
        if (k % 2 == 0) {
            sum = (sum + term) % MOD;
        } else {
            sum = (sum - term + MOD) % MOD;
        }
        fact_k = (fact_k * (k + 1)) % MOD;
    }
    
    return sum;
}

// Derangements via recursion (faster)
long long derangements_DP(int n) {
    if (n == 0) return 1;
    if (n == 1) return 0;
    
    vector<long long> dp(n + 1);
    dp[0] = 1;
    dp[1] = 0;
    
    for (int i = 2; i <= n; i++) {
        dp[i] = ((i - 1) * ((dp[i-1] + dp[i-2]) % MOD)) % MOD;
    }
    
    return dp[n];
}

int main() {
    int n = 5;
    cout << "Derangements of " << n << " elements: " 
         << derangements_DP(n) << endl;
    return 0;
}
```

### 1.5. Task: Number of Permutations with Exactly k Fixed Points

```cpp
long long permWithKFixed(int n, int k) {
    // C(n, k) * D(n-k)
    // We choose k elements to be in place, the remaining n-k are a derangement
    long long comb = binomial(n, k); // You must implement binomial
    long long der = derangements_DP(n - k);
    return (comb * der) % MOD;
}
```

---

## 2. Stars and Bars

This technique is invaluable for tasks of the type "distributing identical items into different boxes".

### 2.1. Variant 1: Boxes Can Be Empty
The number of ways to distribute $n$ identical balls into $k$ different boxes is:
$$\binom{n + k - 1}{k - 1}$$
*Logic*: We have $n$ stars and $k-1$ bars (separators). In total $n+k-1$ objects. We choose which $k-1$ of them are to be bars.

### 2.2. Variant 2: Each Box Must Have at Least 1 Item
The number of ways is:
$$\binom{n - 1}{k - 1}$$
*Logic*: We place the bars in the empty spaces *between* the stars. There are $n-1$ such spaces.

### 2.3. Implementation:

```cpp
// Binomial coefficients with DP
const int MAXN = 1005;
const long long MOD = 1e9 + 7;
long long C[MAXN][MAXN];

void precompute_binomial() {
    for (int i = 0; i < MAXN; i++) {
        C[i][0] = C[i][i] = 1;
        for (int j = 1; j < i; j++) {
            C[i][j] = (C[i-1][j-1] + C[i-1][j]) % MOD;
        }
    }
}

// Variant 1: Boxes can be empty
long long starsAndBars1(int n, int k) {
    return C[n + k - 1][k - 1];
}

// Variant 2: Each box must have at least 1
long long starsAndBars2(int n, int k) {
    if (n < k) return 0; // Impossible
    return C[n - 1][k - 1];
}

// Variant 3: Each box has min and max
long long starsAndBarsWithLimits(int n, int k, vector<int>& minVals, vector<int>& maxVals) {
    // First we separate the minimal values
    int remaining = n;
    for (int i = 0; i < k; i++) {
        remaining -= minVals[i];
    }
    
    if (remaining < 0) return 0;
    
    // Use PIE for the maximal constraints
    // This is more complex and requires generating all subsets
    // ... (left as an exercise)
    return 0; // placeholder
}
```

### 2.4. Sample Task:

**Task**: In how many ways can we divide $n$ identical candies among $k$ children if each child must receive at least 2 candies?

**Solution**: 
1. We give each child 2 candies: $n - 2k$ candies remain.
2. We distribute the remaining candies arbitrarily: $\binom{(n-2k) + k - 1}{k-1} = \binom{n - k - 1}{k - 1}$.

```cpp
long long distributeCandies(int n, int k) {
    if (n < 2 * k) return 0; // Impossible
    return C[n - k - 1][k - 1];
}
```

---

## 3. Burnside's Lemma

Used for counting **distinct configurations** in the presence of a group of symmetries (rotations, reflections).

**Formula**:
$$N = \frac{1}{|G|} \sum_{g \in G} |X^g|$$
Where:
*   $|G|$ is the number of symmetries (e.g., rotation by 0, 90, 180, 270 degrees).
*   $|X^g|$ is the number of colorings that remain unchanged after applying symmetry $g$.

### Example: Necklace with $n$ beads and $m$ colors (rotations only)
$|G| = n$ (all possible rotations).
For each rotation with $k$ steps, the number of fixed colorings is $m^{\gcd(k, n)}$.
In total: $N = \frac{1}{n} \sum_{k=0}^{n-1} m^{\gcd(k, n)}$.

### Implementation:

```cpp
#include <iostream>
#include <cmath>
using namespace std;

long long gcd(long long a, long long b) {
    return b == 0 ? a : gcd(b, a % b);
}

long long power(long long base, long long exp, long long mod) {
    long long result = 1;
    base %= mod;
    while (exp > 0) {
        if (exp & 1) result = (result * base) % mod;
        base = (base * base) % mod;
        exp >>= 1;
    }
    return result;
}

long long modinv(long long a, long long m) {
    return power(a, m - 2, m);
}

// Counting necklaces with n beads and m colors (rotations only)
long long necklaceRotations(int n, int m, long long MOD) {
    long long sum = 0;
    for (int k = 0; k < n; k++) {
        long long g = gcd(k, n);
        sum = (sum + power(m, g, MOD)) % MOD;
    }
    return (sum * modinv(n, MOD)) % MOD;
}

// Necklace with rotations AND reflections (Polya enumeration)
long long necklaceWithReflections(int n, int m, long long MOD) {
    long long rotations = 0;
    for (int k = 0; k < n; k++) {
        rotations = (rotations + power(m, gcd(k, n), MOD)) % MOD;
    }
    
    long long reflections = 0;
    if (n % 2 == 1) {
        // n axes through one vertex
        reflections = (n * power(m, (n + 1) / 2, MOD)) % MOD;
    } else {
        // n/2 axes through two vertices + n/2 axes between vertices
        long long axis1 = (n / 2) * power(m, n / 2 + 1, MOD) % MOD;
        long long axis2 = (n / 2) * power(m, n / 2, MOD) % MOD;
        reflections = (axis1 + axis2) % MOD;
    }
    
    long long total = (rotations + reflections) % MOD;
    long long groupSize = 2 * n; // n rotations + n reflections
    
    return (total * modinv(groupSize, MOD)) % MOD;
}

int main() {
    int n = 6; // 6 beads
    int m = 3; // 3 colors
    long long MOD = 1e9 + 7;
    
    cout << "Necklaces (rotations only): " 
         << necklaceRotations(n, m, MOD) << endl;
    cout << "Necklaces (rotations + reflections): " 
         << necklaceWithReflections(n, m, MOD) << endl;
    
    return 0;
}
```

### Another example: Coloring a cube

A cube has 24 symmetries (elements of the rotation group):
- 1 identity: all $m^6$ colorings are fixed.
- 6 rotations of 90° about axes through the centers of faces: $m^3$ fixed.
- 3 rotations of 180° about the same axes: $m^4$ fixed.
- 8 rotations of 120° about diagonals: $m^2$ fixed.
- 6 rotations of 180° about axes through the midpoints of edges: $m^3$ fixed.

$$N = \frac{1}{24}(m^6 + 6m^3 + 3m^4 + 8m^2 + 6m^3) = \frac{1}{24}(m^6 + 3m^4 + 12m^3 + 8m^2)$$

---

## 4. Lucas Theorem

Allows for the fast calculation of $\binom{n}{k} \pmod p$, where $p$ is a **small prime number**, while $n$ and $k$ are huge (up to $10^{18}$).
If we write $n$ and $k$ in the $p$-ary numeral system:
$n = n_k p^k + \dots + n_0$
$k = k_k p^k + \dots + k_0$
Then:
$$\binom{n}{k} \equiv \prod_{i=0}^k \binom{n_i}{k_i} \pmod p$$

### Implementation:

```cpp
const long long MOD = 1000000007; // Must be a prime number

// Precompute factorials and inverses for small values
long long fact[MOD], inv_fact[MOD];

void precompute_factorials() {
    fact[0] = 1;
    for (int i = 1; i < MOD; i++) {
        fact[i] = (fact[i-1] * i) % MOD;
    }
    
    inv_fact[MOD - 1] = power(fact[MOD - 1], MOD - 2, MOD);
    for (int i = MOD - 2; i >= 0; i--) {
        inv_fact[i] = (inv_fact[i + 1] * (i + 1)) % MOD;
    }
}

// Binomial for small n, k
long long binomial_small(long long n, long long k) {
    if (k > n || k < 0) return 0;
    return (fact[n] * inv_fact[k] % MOD) * inv_fact[n - k] % MOD;
}

// Lucas Theorem
long long lucas(long long n, long long k, long long p) {
    if (k == 0) return 1;
    return (binomial_small(n % p, k % p) * lucas(n / p, k / p, p)) % p;
}

int main() {
    precompute_factorials();
    
    long long n = 1000000000000LL;
    long long k = 500000000000LL;
    
    cout << "C(" << n << ", " << k << ") mod " << MOD 
         << " = " << lucas(n, k, MOD) << endl;
    
    return 0;
}
```

### Application: Checking whether a binomial is even/odd

For $p = 2$, Lucas theorem gives a quick way to check whether $\binom{n}{k}$ is even:
$inom{n}{k}$ is odd if and only if $(k \& n) = k$ (all bits of $k$ are present in $n$).

---

## 5. Cayley's Formula

The number of different labeled trees with $n$ vertices is $n^{n-2}$.
It is related to **Prüfer Sequences** – each string of $n-2$ numbers in the range $[1, n]$ encodes a unique tree.

### 5.1. Proof via Prüfer Encoding:

```cpp
// Transforming a tree into a Prüfer sequence
vector<int> treeToPrufer(int n, vector<pair<int,int>>& edges) {
    vector<int> degree(n + 1, 0);
    vector<set<int>> adj(n + 1);
    
    for (auto [u, v] : edges) {
        adj[u].insert(v);
        adj[v].insert(u);
        degree[u]++;
        degree[v]++;
    }
    
    vector<int> prufer;
    for (int i = 0; i < n - 2; i++) {
        // Find the leaf with the smallest label
        for (int leaf = 1; leaf <= n; leaf++) {
            if (degree[leaf] == 1) {
                int neighbor = *adj[leaf].begin();
                prufer.push_back(neighbor);
                
                // Remove edge
                degree[leaf]--;
                degree[neighbor]--;
                adj[leaf].erase(neighbor);
                adj[neighbor].erase(leaf);
                break;
            }
        }
    }
    
    return prufer;
}

// Transforming a Prüfer sequence into a tree
vector<pair<int,int>> pruferToTree(vector<int>& prufer) {
    int n = prufer.size() + 2;
    vector<int> degree(n + 1, 1);
    
    for (int node : prufer) {
        degree[node]++;
    }
    
    vector<pair<int,int>> edges;
    for (int i : prufer) {
        // Find the smallest leaf
        for (int leaf = 1; leaf <= n; leaf++) {
            if (degree[leaf] == 1) {
                edges.push_back({i, leaf});
                degree[i]--;
                degree[leaf]--;
                break;
            }
        }
    }
    
    // Connect the last two leaves
    for (int u = 1; u <= n; u++) {
        if (degree[u] == 1) {
            for (int v = u + 1; v <= n; v++) {
                if (degree[v] == 1) {
                    edges.push_back({u, v});
                    break;
                }
            }
            break;
        }
    }
    
    return edges;
}
```

### 5.2. Generalization of Cayley's Formula:

The number of forests with $n$ vertices and $k$ components (trees) is: $k \cdot n^{n-k-1}$.

### 5.3. Application: Number of ways to connect cities

**Task**: We have $n$ cities. How many ways are there to connect them with $n-1$ paths such that each city is accessible from every other?

**Answer**: $n^{n-2}$ (Cayley's formula).

---

## 6. Practice Tasks
1.  **CSES Distributing Apples**: Stars and Bars.
2.  **Codeforces 451E**: PIE with large constraints (requires Lucas or clever counting).
3.  **CSES Christmas Party**: Derangements.
4.  **UVa 10294**: Necklace (Burnside).
5.  **SPOJ ADACABAA**: Binomial coefficients modulo composite (Lucas).
6.  **Codeforces 711D**: Colored Tree (Burnside + Prüfer).

## 7. Catalan Numbers

Catalan numbers $C_n$ have numerous applications:

$$C_n = \frac{1}{n+1}\binom{2n}{n} = \frac{(2n)!}{(n+1)!n!}$$

### Recursion:
$$C_0 = 1, \quad C_{n+1} = \sum_{i=0}^{n} C_i C_{n-i}$$

### Applications:
1. Number of valid bracket sequences with $n$ pairs of brackets.
2. Number of binary trees with $n$ internal nodes.
3. Number of ways to triangulate a polygon with $n+2$ vertices.
4. Number of paths in a grid from $(0,0)$ to $(n,n)$ that do not cross the diagonal.

```cpp
vector<long long> catalan(int n, long long MOD) {
    vector<long long> C(n + 1);
    C[0] = 1;
    
    for (int i = 0; i < n; i++) {
        C[i + 1] = 0;
        for (int j = 0; j <= i; j++) {
            C[i + 1] = (C[i + 1] + C[j] * C[i - j]) % MOD;
        }
    }
    
    return C;
}

// More efficient version with binomial coefficients
long long catalan_fast(int n, long long MOD) {
    long long binom = binomial(2 * n, n); // C(2n, n)
    return (binom * modinv(n + 1, MOD)) % MOD;
}
```

## 8. Stirling Numbers

### Stirling Numbers of the First Kind $S_1(n, k)$:
The number of ways to partition $n$ elements into $k$ cycles.

### Stirling Numbers of the Second Kind $S_2(n, k)$:
The number of ways to partition $n$ elements into $k$ non-empty subsets.

Recursion: $S_2(n, k) = k \cdot S_2(n-1, k) + S_2(n-1, k-1)$

```cpp
long long stirling2[MAXN][MAXN];

void compute_stirling2(int n) {
    stirling2[0][0] = 1;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= i; j++) {
            stirling2[i][j] = (j * stirling2[i-1][j] + stirling2[i-1][j-1]) % MOD;
        }
    }
}
```

### Stirling Application:
The number of surjective functions from a set with $n$ elements to a set with $k$ elements is: $k! \cdot S_2(n, k)$.

## 🏁 Conclusion

Advanced combinatorics often reduces to recognizing which principle to apply. PIE is for tasks with "at least one" or "none", Stars and Bars is for distributing resources, and Burnside is for symmetry. Practice with various tasks to develop intuition.

```