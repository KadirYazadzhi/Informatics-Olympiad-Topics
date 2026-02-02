# 📊 Matrices: Advanced Theorems and Applications

Matrices are fundamental structures in competitive programming, appearing in various contexts - from dynamic programming optimization to graph theory and number theory. Beyond basic operations, several powerful theorems and techniques leverage matrix properties to efficiently solve complex problems.

This comprehensive guide covers:
- **Kirchhoff's Theorem** (Matrix Tree Theorem) for counting spanning trees.
- **Cayley-Hamilton Theorem** for matrix polynomial optimization.
- **Gaussian Elimination** over different fields (real, modular, binary).
- **Matrix Exponentiation** for recurrence relations.
- **Special Matrix Types** and their properties.
- Practical implementations and optimizations.

## 🧮 Matrix Basics

### Basic Matrix Operations

```cpp
struct Matrix {
    int n, m;
    vector<vector<long long>> a;
    
    Matrix(int _n, int _m) : n(_n), m(_m) {
        a.assign(n, vector<long long>(m, 0));
    }
    
    Matrix(vector<vector<long long>> _a) : a(_a) {
        n = a.size();
        m = a[0].size();
    }
    
    // Identity matrix
    static Matrix identity(int n) {
        Matrix I(n, n);
        for (int i = 0; i < n; i++) {
            I.a[i][i] = 1;
        }
        return I;
    }
    
    // Matrix addition
    Matrix operator+(const Matrix& other) const {
        Matrix result(n, m);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                result.a[i][j] = a[i][j] + other.a[i][j];
            }
        }
        return result;
    }
    
    // Matrix multiplication
    Matrix operator*(const Matrix& other) const {
        Matrix result(n, other.m);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < other.m; j++) {
                for (int k = 0; k < m; k++) {
                    result.a[i][j] += a[i][k] * other.a[k][j];
                }
            }
        }
        return result;
    }
    
    // Multiplication by scalar
    Matrix operator*(long long scalar) const {
        Matrix result(n, m);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                result.a[i][j] = a[i][j] * scalar;
            }
        }
        return result;
    }
    
    // Transposition
    Matrix transpose() const {
        Matrix result(m, n);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                result.a[j][i] = a[i][j];
            }
        }
        return result;
    }
};
```

### Matrix Exponentiation

One of the most powerful techniques in competitive programming is matrix exponentiation for solving linear recurrences:

```cpp
const long long MOD = 1e9 + 7;

struct MatrixMod {
    int n;
    vector<vector<long long>> a;
    
    MatrixMod(int _n) : n(_n) {
        a.assign(n, vector<long long>(n, 0));
    }
    
    static MatrixMod identity(int n) {
        MatrixMod I(n);
        for (int i = 0; i < n; i++) I.a[i][i] = 1;
        return I;
    }
    
    MatrixMod operator*(const MatrixMod& other) const {
        MatrixMod result(n);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                for (int k = 0; k < n; k++) {
                    result.a[i][j] = (result.a[i][j] + 
                                     a[i][k] * other.a[k][j]) % MOD;
                }
            }
        }
        return result;
    }
    
    MatrixMod power(long long p) const {
        MatrixMod result = identity(n);
        MatrixMod base = *this;
        
        while (p > 0) {
            if (p & 1) result = result * base;
            base = base * base;
            p >>= 1;
        }
        
        return result;
    }
};

// Example: Fibonacci via matrix exponentiation
// F(n) = F(n-1) + F(n-2)
// [F(n+1)]   [1 1]^n   [F(1)]   [1 1]^n   [1]
// [F(n)  ] = [1 0]   * [F(0)] = [1 0]   * [0]

long long fibonacci(long long n) {
    if (n == 0) return 0;
    if (n == 1) return 1;
    
    MatrixMod M(2);
    M.a[0][0] = M.a[0][1] = M.a[1][0] = 1;
    M.a[1][1] = 0;
    
    M = M.power(n);
    return M.a[1][0];  // F(n)
}

int main() {
    cout << "F(10) = " << fibonacci(10) << "\n";      // 55
    cout << "F(100) = " << fibonacci(100) << "\n";    // A very large number
    return 0;
}
```

**Time complexity:** $O(n^3 \log p)$ where $n$ is the matrix size and $p$ is the power.

**Applications:**
- Linear recurrences: Fibonacci, tribonacci, etc.
- Counting paths in a graph: Number of paths of length $k$.
- Dynamic programming optimization.
- Linear transformations.

## 🌳 Kirchhoff's Theorem (Matrix Tree Theorem)

**Kirchhoff's Theorem** is one of the most elegant results in graph theory, providing a direct algebraic method for counting the spanning trees in a graph.

### Formulation of the Theorem

The number of spanning trees in a connected graph $G = (V, E)$ is equal to **any cofactor** of its Laplacian matrix $L$.

### Laplacian Matrix

For a graph with $n$ vertices:

**Degree matrix $D$**: A diagonal matrix where $D_{i,i} = \deg(v_i)$.

**Adjacency matrix $A$**: $A_{i,j} = $ number of edges between $v_i$ and $v_j$.

**Laplacian matrix**: $L = D - A$.

Explicitly:
$$L_{i,j} = \begin{cases}
\deg(v_i) & \text{if } i = j \\
-(\text{number of edges between } i \text{ and } j) & \text{if } i \neq j
\end{cases}
$$

### Calculating the Number of Spanning Trees

```cpp
#include <bits/stdc++.h>
using namespace std;

const double EPS = 1e-9;

// Calculate determinant via Gaussian elimination
double determinant(vector<vector<double>> A) {
    int n = A.size();
    double det = 1.0;
    
    for (int i = 0; i < n; i++) {
        // Find pivot
        int pivot = i;
        for (int j = i + 1; j < n; j++) {
            if (abs(A[j][i]) > abs(A[pivot][i])) {
                pivot = j;
            }
        }
        
        if (abs(A[pivot][i]) < EPS) {
            return 0;  // Singular matrix
        }
        
        if (pivot != i) {
            swap(A[i], A[pivot]);
            det *= -1;  // Sign change on row swap
        }
        
        det *= A[i][i];
        
        // Eliminate the column
        for (int j = i + 1; j < n; j++) {
            double factor = A[j][i] / A[i][i];
            for (int k = i; k < n; k++) {
                A[j][k] -= factor * A[i][k];
            }
        }
    }
    
    return det;
}

// Number of spanning trees using Kirchhoff's theorem
long long countSpanningTrees(int n, vector<pair<int, int>>& edges) {
    // Build Laplacian matrix
    vector<vector<double>> L(n, vector<double>(n, 0));
    
    for (auto [u, v] : edges) {
        L[u][u]++;
        L[v][v]++;
        L[u][v]--;
        L[v][u]--;
    }
    
    // Remove the first row and column (calculate cofactor)
    vector<vector<double>> cofactor(n - 1, vector<double>(n - 1));
    for (int i = 1; i < n; i++) {
        for (int j = 1; j < n; j++) {
            cofactor[i - 1][j - 1] = L[i][j];
        }
    }
    
    // The determinant gives the number of spanning trees
    double result = determinant(cofactor);
    return (long long)(result + 0.5);  // Round to the nearest integer
}

int main() {
    // Example: K4 (complete graph with 4 vertices)
    // Known answer: 16 spanning trees
    int n = 4;
    vector<pair<int, int>> edges = {
        {0, 1}, {0, 2}, {0, 3},
        {1, 2}, {1, 3}, {2, 3}
    };
    
    cout << "Spanning trees in K4: " << countSpanningTrees(n, edges) << "\n";
    // Output: 16
    
    return 0;
}
```

**Time complexity:** $O(n^3)$ for Gaussian elimination.

### Modular Version

For large graphs, calculate the determinant modulo a prime number:

```cpp
const long long MOD = 1e9 + 7;

long long modPow(long long base, long long exp, long long mod) {
    long long result = 1;
    while (exp > 0) {
        if (exp % 2 == 1) result = (result * base) % mod;
        base = (base * base) % mod;
        exp /= 2;
    }
    return result;
}

long long modInv(long long a, long long mod) {
    return modPow(a, mod - 2, mod);  // Fermat's Little Theorem
}

long long determinantMod(vector<vector<long long>> A, long long mod) {
    int n = A.size();
    long long det = 1;
    
    for (int i = 0; i < n; i++) {
        int pivot = -1;
        for (int j = i; j < n; j++) {
            if (A[j][i] % mod != 0) {
                pivot = j;
                break;
            }
        }
        
        if (pivot == -1) return 0;
        
        if (pivot != i) {
            swap(A[i], A[pivot]);
            det = (mod - det) % mod;
        }
        
        det = (det * A[i][i]) % mod;
        long long inv = modInv(A[i][i], mod);
        
        for (int j = i + 1; j < n; j++) {
            long long factor = (A[j][i] * inv) % mod;
            for (int k = i; k < n; k++) {
                A[j][k] = ((A[j][k] - factor * A[i][k]) % mod + mod) % mod;
            }
        }
    }
    
    return det;
}

long long countSpanningTreesMod(int n, vector<pair<int, int>>& edges) {
    vector<vector<long long>> L(n, vector<long long>(n, 0));
    
    for (auto [u, v] : edges) {
        L[u][u]++;
        L[v][v]++;
        L[u][v] = (L[u][v] - 1 + MOD) % MOD;
        L[v][u] = (L[v][u] - 1 + MOD) % MOD;
    }
    
    vector<vector<long long>> cofactor(n - 1, vector<long long>(n - 1));
    for (int i = 1; i < n; i++) {
        for (int j = 1; j < n; j++) {
            cofactor[i - 1][j - 1] = L[i][j];
        }
    }
    
    return determinantMod(cofactor, MOD);
}
```

### Applications

1. **Counting labeled trees**: $n^{n-2}$ (Cayley's formula, provable via Kirchhoff's theorem).
2. **Network reliability**: Number of ways to maintain connectivity.
3. **Electric networks**: Related to effective resistance.
4. **Random spanning trees**: Algorithms for uniform sampling.

## 🏁 Gaussian Elimination Modulo 2 (XOR Systems)

When working over $GF(2)$ (the binary field), addition becomes XOR and we can use bitwise operations for massive speedups.

### Binary Gaussian Elimination with Bitset

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MAXN = 2000;
bitset<MAXN> bit_matrix[MAXN];  // Each row is a bitset
bitset<MAXN> rhs_bit;           // Right-hand side (for Ax = b)

int gaussianEliminationBinary(int n, int m) {
    // n = rows, m = columns
    int rank = 0;
    
    for (int col = 0; col < m && rank < n; col++) {
        // Find pivot
        int pivot = -1;
        for (int row = rank; row < n; row++) {
            if (bit_matrix[row][col]) {
                pivot = row;
                break;
            }
        }
        
        if (pivot == -1) continue;  // The column is all zeros
        
        // Swap rows
        swap(bit_matrix[rank], bit_matrix[pivot]);
        
        // Eliminate all other 1s in this column
        for (int row = 0; row < n; row++) {
            if (row != rank && bit_matrix[row][col]) {
                bit_matrix[row] ^= bit_matrix[rank];  // XOR entire rows!
                rhs_bit[row] = rhs_bit[row] ^ rhs_bit[rank];
            }
        }
        
        rank++;
    }
    
    return rank;
}

// Example: Lights Out Puzzle
// n x n grid, clicking a light toggles it along with its neighbors
// Determine whether the configuration is solvable

bool lightsOut(int n, vector<vector<int>>& target) {
    int N_total = n * n;
    
    // Build system of equations
    // Variable x[i][j] = 1 if we click at position (i,j)
    // Each equation: sum of clicks affecting position = target state
    
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            int eq = i * n + j;  // Equation number
            rhs_bit[eq] = target[i][j];
            
            // Clicking (i,j) affects itself
            bit_matrix[eq][i * n + j] = 1;
            
            // And neighbors
            if (i > 0) bit_matrix[eq][(i-1) * n + j] = 1;      // up
            if (i < n-1) bit_matrix[eq][(i+1) * n + j] = 1;    // down
            if (j > 0) bit_matrix[eq][i * n + (j-1)] = 1;      // left
            if (j < n-1) bit_matrix[eq][i * n + (j+1)] = 1;    // right
        }
    }
    
    int rank = gaussianEliminationBinary(N_total, N_total);
    
    // Check consistency: if any equation became 0 = 1, no solution exists
    for (int i = rank; i < N_total; i++) {
        if (rhs_bit[i]) return false;
    }
    
    return true;
}

int main() {
    // Example: 3x3 lights out
    vector<vector<int>> target = {
        {1, 0, 1},
        {0, 1, 0},
        {1, 0, 1}
    };
    
    if (lightsOut(3, target)) {
        cout << "Solvable!\n";
    } else {
        cout << "No solution\n";
    }
    
    return 0;
}
```

**Speedup**: Operations are $64 \times$ faster due to bitset optimizations!
- Time complexity: $O(n^3 / 64)$ instead of $O(n^3)$.

### Applications of Binary Gaussian Elimination

1. **Lights Out Puzzle**: Classical application.
2. **XOR Subsets**: Find a subset with a specific XOR.
3. **Linear Codes**: Error-correcting codes.
4. **Cryptanalysis**: Breaking linear ciphers.
5. **Game Theory**: Analysis of XOR games (Nim variants).

### Finding an XOR Subset

```cpp
// Given an array, find whether a subset with XOR equal to target exists
bool findXORSubset(vector<int>& arr, int target) {
    const int BITS = 30;  // Assume 30-bit integers
    int n = arr.size();
    
    // Each number is a row in a binary matrix
    for (int i = 0; i < n; i++) {
        for (int bit = 0; bit < BITS; bit++) {
            bit_matrix[i][bit] = (arr[i] >> bit) & 1;
        }
    }
    
    // Target as right-hand side
    for (int bit = 0; bit < BITS; bit++) {
        rhs_bit[bit] = (target >> bit) & 1;
    }
    
    int rank = gaussianEliminationBinary(n, BITS);
    
    // Check whether the system is consistent
    for (int i = rank; i < n; i++) {
        bool all_zero = true;
        for (int j = 0; j < BITS; j++) {
            if (bit_matrix[i][j]) {
                all_zero = false;
                break;
            }
        }
        if (all_zero && rhs_bit[i]) return false;
    }
    
    return true;
}
```

## 🎯 Cayley-Hamilton Theorem

The **Cayley-Hamilton Theorem** states that every square matrix satisfies its own characteristic equation.

### Formulation of the Theorem

If $p(\lambda) = \det(A - \lambda I)$ is the characteristic polynomial of matrix $A$, then:
$$p(A) = 0$$

### Application: Fast Matrix Exponentiation

For a $k \times k$ matrix, instead of calculating $A^n$ directly, we can:
1. Find the characteristic polynomial $p(\lambda)$.
2. Reduce high powers using $p(A) = 0$.
3. Express $A^n$ as a polynomial of degree $< k$.

```cpp
// For a 2x2 matrix the characteristic polynomial is:
// p(λ) = λ² - tr(A)λ + det(A)
// Thus A² = tr(A)·A - det(A)·I

MatrixMod fastPower2x2(MatrixMod A, long long n) {
    // For n > 2 use: A^n = c₁A + c₀I
    // Calculate c₁, c₀ using recurrence relation
    
    long long trace = (A.a[0][0] + A.a[1][1]) % MOD;
    long long det = (A.a[0][0] * A.a[1][1] - A.a[0][1] * A.a[1][0]) % MOD;
    det = (det % MOD + MOD) % MOD;
    
    // Use matrix exponentiation on the recurrence of coefficients
    // This is complex, but saves a factor in practice
    return A.power(n);  // Fallback to standard method
}
```

**Note**: In practice, direct matrix exponentiation is often simpler, unless the matrix is very large.

## 💡 Special Matrix Types

### Symmetric Matrices

$A = A^T$ (the transpose is equal to itself).

**Properties:**
- Always diagonalizable.
- Eigenvalues are real.
- Eigenvectors are orthogonal.

### Orthogonal Matrices

$A^T A = I$ (the transpose is inverse).

**Properties:**
- Preserve distances and angles.
- Determinant is $\pm 1$.
- Rotations and reflections.

### Permutation Matrices

Exactly one 1 in each row and column, others 0.

**Properties:**
- Represent permutations.
- $P^T = P^{-1}$.
- Determinant is $\pm 1$.

## 🚀 Advanced Techniques

### Block Matrix Multiplication

For very large matrices, divide into blocks:

```cpp
// Strassen algorithm: O(n^2.807) instead of O(n^3)
// Practical for n > 64, but complex to implement
```

### Sparse Matrices

For matrices with mostly zeros:

```cpp
struct SparseMatrix {
    map<pair<int,int>, long long> data;
    int n, m;
    
    long long get(int i, int j) {
        auto it = data.find({i, j});
        return (it == data.end()) ? 0 : it->second;
    }
    
    void set(int i, int j, long long val) {
        if (val != 0) {
            data[{i, j}] = val;
        } else {
            data.erase({i, j});
        }
    }
};
```

### Matrix Chain Multiplication

Find the optimal order for matrix multiplication to minimize operations:

```cpp
// Classical DP task
// dp[i][j] = minimum cost for multiplying matrices from i to j
int matrixChainOrder(vector<int>& dims) {
    int n = dims.size() - 1;
    vector<vector<int>> dp(n, vector<int>(n, 0));
    
    for (int len = 2; len <= n; len++) {
        for (int i = 0; i < n - len + 1; i++) {
            int j = i + len - 1;
            dp[i][j] = INT_MAX;
            
            for (int k = i; k < j; k++) {
                int cost = dp[i][k] + dp[k+1][j] + 
                          dims[i] * dims[k+1] * dims[j+1];
                dp[i][j] = min(dp[i][j], cost);
            }
        }
    }
    
    return dp[0][n-1];
}
```

## 📝 Practice Tasks

### Task 1: Fibonacci Variant
Calculate $F(n) = 2F(n-1) + 3F(n-2)$ with $F(0) = 1, F(1) = 1$.

```cpp
long long fibVariant(long long n) {
    if (n == 0) return 1;
    if (n == 1) return 1;
    
    MatrixMod M(2);
    M.a[0][0] = 2; M.a[0][1] = 3;
    M.a[1][0] = 1; M.a[1][1] = 0;
    
    M = M.power(n);
    return M.a[1][0];
}
```

### Task 2: Path Counting
Count paths of exact length $k$ in a graph.

```cpp
long long countPaths(vector<vector<int>>& adj, int k, int start, int end) {
    int n = adj.size();
    MatrixMod A(n);
    
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            A.a[i][j] = adj[i][j];
        }
    }
    
    A = A.power(k);
    return A.a[start][end];
}
```

### Task 3: Spanning Trees in a Grid
Count spanning trees in an $n \times m$ grid graph.

```cpp
// Use Kirchhoff's theorem on the Laplacian matrix of the grid graph
// The answer has a closed form for small grids
```

---

## 🏁 Conclusion

Matrix theory provides powerful tools for competitive programming:

### Key Techniques:

1. **Matrix Exponentiation**: $O(\log n)$ solution for linear recurrences.
   - Fibonacci, DP optimization, path counting.
   - Time: $O(k^3 \log n)$ for a $k \times k$ matrix.

2. **Kirchhoff's Theorem**: Counting spanning trees in $O(n^3)$.
   - Turning combinatorics into linear algebra.
   - Precise counts modulo a prime number.

3. **Binary Gaussian Elimination**: XOR systems in $O(n^3/64)$.
   - Lights Out, XOR subset, game theory.
   - Bitset for 64× speedup.

4. **Cayley-Hamilton**: Polynomial representation of matrix powers.
   - Reduces calculations for special matrices.
   - More of a theoretical tool than a practical one.

### Complexity Summary:

| Operation | Time Complexity | Memory |
|-----------|-----------------|--------|
| Matrix multiplication | $O(n^3)$ | $O(n^2)$ |
| Matrix exponentiation | $O(n^3 \log k)$ | $O(n^2)$ |
| Determinant | $O(n^3)$ | $O(n^2)$ |
| Gaussian elimination | $O(n^3)$ or $O(n^3/64)$ | $O(n^2)$ |
| Number of spanning trees | $O(n^3)$ | $O(n^2)$ |

### When to Use Matrices:

- **Linear recurrences** with large $n$: Use matrix exponentiation.
- **Path counting** in graphs: Powers of the adjacency matrix.
- **Counting spanning trees**: Kirchhoff's theorem.
- **XOR tasks**: Binary Gaussian elimination.
- **DP optimization**: Matrix representation of transitions.

Matrix techniques transform seemingly complex tasks into standard linear algebra operations, providing elegant and efficient solutions. Master these tools and you will have a significant advantage in advanced competitive programming!