# 🧮 Linear Algebra: Bases, Rank, and Linear Systems

Linear algebra is a fundamental mathematical tool in competitive programming, especially when working with systems of equations, matrix operations, and optimization tasks. While the full depth of linear algebra is rarely necessary in competitions, understanding key concepts such as matrix rank, linear independence, Gaussian elimination, and XOR basis is crucial for solving advanced problems.

This topic covers the essential concepts from linear algebra that appear in competitive programming:
- **Matrix Rank** and linear independence.
- **Gaussian Elimination** for solving linear systems.
- **XOR Basis** as a special case of Gaussian elimination.
- **Determinants** and their applications.
- Practical implementations and optimizations.

## 📚 Basic Concepts

### Linear Independence and Dependence

A set of vectors (or rows/columns in a matrix) is **linearly independent** if no vector can be expressed as a linear combination of the others.

**Example:**
```cpp
// The vectors v1 = [1, 2], v2 = [2, 4] are linearly dependent
// because v2 = 2 * v1

// The vectors v1 = [1, 2], v2 = [3, 5] are linearly independent
// because neither can be expressed as a scalar multiplication of the other
```

**Key properties:**
- If the vectors are linearly dependent, one can be removed without loss of information.
- The maximum number of linearly independent vectors in $\mathbb{R}^n$ is $n$.
- Linear independence is key to determining the rank of a matrix.

### Vector Spaces and Span

The **span** of a set of vectors is all possible linear combinations of those vectors.

```cpp
// Example: The span of {[1,0], [0,1]} is the entire 2D space R^2
// The span of {[1,2], [2,4]} is only a line (because they are dependent)
```

A **basis** is a minimum set of linearly independent vectors that span a space.

## 🎹 Matrix Rank

The **rank** of a matrix is one of its most important properties, representing the maximum number of linearly independent rows (or equivalently, columns).

### Definition and Properties

The **rank** can be defined in several equivalent ways:
1. Number of linearly independent rows.
2. Number of linearly independent columns.
3. Dimension of the column space (or row space).
4. Size of the largest non-zero minor (determinant of a submatrix).
5. Number of non-zero rows after Gaussian elimination.

**Key properties:**
- $\text{rank}(A) \leq \min(\text{rows}, \text{columns})$.
- $\text{rank}(A) = \text{rank}(A^T)$ (the transpose has the same rank).
- $\text{rank}(AB) \leq \min(\text{rank}(A), \text{rank}(B))$.
- Full rank: the rank is equal to the smaller dimension (invertible if square).

### Calculating Rank via Gaussian Elimination

The standard method for calculating rank is Gaussian elimination:

```cpp
#include <bits/stdc++.h>
using namespace std;

const double EPS = 1e-9;

int computeRank(vector<vector<double>>& matrix) {
    int n = matrix.size();        // rows
    int m = matrix[0].size();     // columns
    int rank = 0;
    
    for (int col = 0; col < m && rank < n; col++) {
        // Find pivot (largest absolute value for numerical stability)
        int pivot_row = rank;
        for (int i = rank + 1; i < n; i++) {
            if (abs(matrix[i][col]) > abs(matrix[pivot_row][col])) {
                pivot_row = i;
            }
        }
        
        // If the column is all zeros, skip it
        if (abs(matrix[pivot_row][col]) < EPS) {
            continue;
        }
        
        // Swap current row with pivot row
        swap(matrix[rank], matrix[pivot_row]);
        
        // Eliminate the column under the pivot
        for (int i = rank + 1; i < n; i++) {
            double factor = matrix[i][col] / matrix[rank][col];
            for (int j = col; j < m; j++) {
                matrix[i][j] -= factor * matrix[rank][j];
            }
        }
        
        rank++;
    }
    
    return rank;
}

int main() {
    // Example: 3x4 matrix
    vector<vector<double>> A = {
        {1, 2, 3, 4},
        {2, 4, 6, 8},   // This row is 2 * row 1 (dependent)
        {1, 3, 5, 7}
    };
    
    cout << "Rank: " << computeRank(A) << "\n";  // Output: 2
    
    return 0;
}
```

**Time complexity:** $O(\min(n,m) \cdot n \cdot m) = O(n^2 m)$ for an $n \times m$ matrix.

### Applications of Matrix Rank

#### 1. Determining the Existence of a Solution

For a system $Ax = b$:
- **Unique solution**: $\text{rank}(A) = \text{rank}([A|b]) = n$ (number of unknowns).
- **Infinite solutions**: $\text{rank}(A) = \text{rank}([A|b]) < n$.
- **No solution**: $\text{rank}(A) \neq \text{rank}([A|b])$.

```cpp
bool hasUniqueSolution(vector<vector<double>>& A, vector<double>& b) {
    int n = A.size();
    int m = A[0].size();
    
    // Create augmented matrix [A|b]
    vector<vector<double>> augmented(n, vector<double>(m + 1));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            augmented[i][j] = A[i][j];
        }
        augmented[i][m] = b[i];
    }
    
    int rank_A = computeRank(A);
    int rank_Aug = computeRank(augmented);
    
    if (rank_A != rank_Aug) {
        return false;  // No solution
    }
    return rank_A == m;  // Unique if rank is equal to number of variables
}
```

#### 2. Finding an Independent Subset

```cpp
// Find the maximal linearly independent subset of rows
vector<int> findIndependentRows(vector<vector<double>>& matrix) {
    int n = matrix.size();
    int m = matrix[0].size();
    vector<int> independent_rows;
    vector<vector<double>> selected;
    
    for (int i = 0; i < n; i++) {
        selected.push_back(matrix[i]);
        int new_rank = computeRank(selected);
        
        if (new_rank > (int)independent_rows.size()) {
            independent_rows.push_back(i);
        } else {
            selected.pop_back();  // This row is dependent
        }
    }
    
    return independent_rows;
}
```

## 🔧 Gaussian Elimination

Gaussian elimination is the fundamental algorithm for solving linear systems $Ax = b$.

### Standard Gaussian Elimination

```cpp
vector<double> gaussianElimination(vector<vector<double>> A, vector<double> b) {
    int n = A.size();
    
    // Forward elimination
    for (int col = 0; col < n; col++) {
        // Find pivot
        int pivot_row = col;
        for (int i = col + 1; i < n; i++) {
            if (abs(A[i][col]) > abs(A[pivot_row][col])) {
                pivot_row = i;
            }
        }
        
        // Check whether matrix is singular
        if (abs(A[pivot_row][col]) < EPS) {
            cout << "A unique solution does not exist\n";
            return {};
        }
        
        // Swap rows
        swap(A[col], A[pivot_row]);
        swap(b[col], b[pivot_row]);
        
        // Eliminate
        for (int i = col + 1; i < n; i++) {
            double factor = A[i][col] / A[col][col];
            b[i] -= factor * b[col];
            for (int j = col; j < n; j++) {
                A[i][j] -= factor * A[col][j];
            }
        }
    }
    
    // Backward substitution
    vector<double> x(n);
    for (int i = n - 1; i >= 0; i--) {
        x[i] = b[i];
        for (int j = i + 1; j < n; j++) {
            x[i] -= A[i][j] * x[j];
        }
        x[i] /= A[i][i];
    }
    
    return x;
}

int main() {
    // Example: Solve system
    // x + 2y + 3z = 14
    // 2x + 5y + 8z = 35
    // 3x + 8y + 14z = 58
    
    vector<vector<double>> A = {
        {1, 2, 3},
        {2, 5, 8},
        {3, 8, 14}
    };
    
    vector<double> b = {14, 35, 58};
    
    vector<double> solution = gaussianElimination(A, b);
    
    cout << "Solution: ";
    for (double x : solution) {
        cout << x << " ";
    }
    cout << "\n";  // Output: 1 2 3
    
    return 0;
}
```

**Time complexity:** $O(n^3)$ for an $n \times n$ system.

### Gauss-Jordan Elimination (Reduced Row Echelon Form)

Gauss-Jordan goes further to produce the reduced row echelon form (RREF):

```cpp
void gaussJordan(vector<vector<double>>& A, vector<double>& b) {
    int n = A.size();
    
    for (int col = 0; col < n; col++) {
        // Find pivot
        int pivot = col;
        for (int i = col + 1; i < n; i++) {
            if (abs(A[i][col]) > abs(A[pivot][col])) {
                pivot = i;
            }
        }
        
        if (abs(A[pivot][col]) < EPS) continue;
        
        swap(A[col], A[pivot]);
        swap(b[col], b[pivot]);
        
        // Normalize pivot row
        double divisor = A[col][col];
        for (int j = 0; j < n; j++) {
            A[col][j] /= divisor;
        }
        b[col] /= divisor;
        
        // Eliminate column in ALL other rows (not just below)
        for (int i = 0; i < n; i++) {
            if (i == col) continue;
            
            double factor = A[i][col];
            for (int j = 0; j < n; j++) {
                A[i][j] -= factor * A[col][j];
            }
            b[i] -= factor * b[col];
        }
    }
    
    // The solution is now directly in vector b
}
```

**Advantage:** The solution is directly available without backward substitution.

### Integer Gaussian Elimination

For tasks with integer coefficients, we can use modular arithmetic:

```cpp
const long long MOD_VAL = 1e9 + 7;

long long modInverse(long long a, long long mod) {
    // Extended Euclidean Algorithm
    long long m0 = mod, x0 = 0, x1 = 1;
    while (a > 1) {
        long long q = a / mod;
        long long t = mod;
        mod = a % mod;
        a = t;
        t = x0;
        x0 = x1 - q * x0;
        x1 = t;
    }
    return (x1 % m0 + m0) % m0;
}

vector<long long> gaussianEliminationMod(vector<vector<long long>> A, 
                                          vector<long long> b) {
    int n = A.size();
    
    for (int col = 0; col < n; col++) {
        // Find non-zero pivot
        int pivot = -1;
        for (int i = col; i < n; i++) {
            if (A[i][col] % MOD_VAL != 0) {
                pivot = i;
                break;
            }
        }
        
        if (pivot == -1) continue;
        
        swap(A[col], A[pivot]);
        swap(b[col], b[pivot]);
        
        long long inv = modInverse(A[col][col], MOD_VAL);
        
        // Eliminate
        for (int i = col + 1; i < n; i++) {
            long long factor = (A[i][col] * inv) % MOD_VAL;
            b[i] = ((b[i] - factor * b[col]) % MOD_VAL + MOD_VAL) % MOD_VAL;
            for (int j = col; j < n; j++) {
                A[i][j] = ((A[i][j] - factor * A[col][j]) % MOD_VAL + MOD_VAL) % MOD_VAL;
            }
        }
    }
    
    // Backward substitution (similar, but with modular arithmetic)
    vector<long long> x(n);
    for (int i = n - 1; i >= 0; i--) {
        x[i] = b[i];
        for (int j = i + 1; j < n; j++) {
            x[i] = ((x[i] - A[i][j] * x[j]) % MOD_VAL + MOD_VAL) % MOD_VAL;
        }
        x[i] = (x[i] * modInverse(A[i][i], MOD_VAL)) % MOD_VAL;
    }
    
    return x;
}
```

## ⊕ XOR Basis as Gaussian Elimination

XOR basis (from Topic 22 - Bitwise Operations) is actually a special case of Gaussian elimination over $GF(2)$ (the field with two elements: 0 and 1).

### Understanding XOR as Linear Algebra

In $GF(2)$:
- Addition is XOR: $1 + 1 = 0$, $1 + 0 = 1$.
- Multiplication is AND: $1 \cdot 1 = 1$, $1 \cdot 0 = 0$.

Each number can be viewed as a vector of bits:
```
13 = 1101₂ → [1, 1, 0, 1]
7  = 0111₂ → [0, 1, 1, 1]
```

XOR is vector addition in $GF(2)$:
```
13 XOR 7 = 1010₂ → [1, 0, 1, 0]
```

### Constructing an XOR Basis

```cpp
struct XORBasis {
    static const int MAXLOG = 60;  // For 64-bit integers
    long long basis[MAXLOG];
    int size;
    
    XORBasis() {
        memset(basis, 0, sizeof(basis));
        size = 0;
    }
    
    // Insert number into basis (Gaussian elimination)
    bool insert(long long x) {
        for (int i = MAXLOG - 1; i >= 0; i--) {
            if ((x & (1LL << i)) == 0) continue;
            
            if (basis[i] == 0) {
                basis[i] = x;
                size++;
                return true;  // x is linearly independent
            }
            
            x ^= basis[i];  // Eliminate bit i
        }
        return false;  // x is linearly dependent
    }
    
    // Check whether value can be represented
    bool canRepresent(long long x) {
        for (int i = MAXLOG - 1; i >= 0; i--) {
            if ((x & (1LL << i)) == 0) continue;
            
            if (basis[i] == 0) return false;
            
            x ^= basis[i];
        }
        return x == 0;
    }
    
    // Get maximum XOR value
    long long getMax() {
        long long result = 0;
        for (int i = MAXLOG - 1; i >= 0; i--) {
            result = max(result, result ^ basis[i]);
        }
        return result;
    }
    
    // Get minimum XOR value
    long long getMin() {
        for (int i = 0; i < MAXLOG; i++) {
            if (basis[i] != 0) return basis[i];
        }
        return 0;
    }
    
    // Number of distinct XOR values possible
    long long countDistinct() {
        return (1LL << size);
    }
};

int main() {
    XORBasis basis;
    
    vector<long long> numbers = {3, 5, 7, 10};
    // Binary: 0011, 0101, 0111, 1010
    
    for (long long x : numbers) {
        basis.insert(x);
    }
    
    cout << "Basis size: " << basis.size << "\n";
    cout << "Max XOR: " << basis.getMax() << "\n";
    cout << "Distinct values: " << basis.countDistinct() << "\n";
    
    return 0;
}
```

### Why it is Gaussian Elimination

Constructing an XOR basis is **exactly** Gaussian elimination:

1. **Pivot selection**: Choose the highest bit position.
2. **Row echelon form**: Each basis element has a unique highest bit.
3. **Linear dependence**: If we can reduce a number to 0, it is dependent.
4. **Basis**: Non-zero rows form a basis for the vector space.

**Matrix view:**
```
Numbers: 13, 7, 10, 5
Binary matrix:
1 1 0 1  (13)
0 1 1 1  (7)
1 0 1 0  (10)
0 1 0 1  (5)

After Gaussian elimination over GF(2):
1 1 0 1  (13)
0 1 1 1  (7)
0 0 1 1  (XOR result)
0 0 0 0  (dependent)

Basis: {13, 7, something}
```

### Applications of XOR Basis

#### 1. Maximum XOR Subarray
```cpp
long long maxXORSubarray(vector<long long>& arr) {
    XORBasis basis;
    long long prefix_xor = 0;
    basis.insert(0);  // Empty subarray
    
    for (long long x : arr) {
        prefix_xor ^= x;
        basis.insert(prefix_xor);
    }
    
    return basis.getMax();
}
```

#### 2. Number of Distinct XOR Values
```cpp
// Number of distinct XOR values from a subset of an array
long long countDistinctXOR(vector<long long>& arr) {
    XORBasis basis;
    for (long long x : arr) {
        basis.insert(x);
    }
    return basis.countDistinct();
}
```

#### 3. Checking for XOR Reachability
```cpp
// Can we obtain a target XOR value from some subset?
bool canGetXOR(vector<long long>& arr, long long target) {
    XORBasis basis;
    for (long long x : arr) {
        basis.insert(x);
    }
    return basis.canRepresent(target);
}
```

## 🔍 Determinants

The **determinant** is a scalar value that encodes important properties of a square matrix.

### Properties and Meaning

- $\det(I) = 1$ (identity matrix).
- $\det(AB) = \det(A) \cdot \det(B)$.
- $\det(A^T) = \det(A)$.
- $\det(A) \neq 0 \iff$ matrix is invertible.
- $|\det(A)|$ is the scaling factor of the volume of the linear transformation.

### Calculating Determinant via Gaussian Elimination

```cpp
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
            det *= -1;  // Row swap changes the sign
        }
        
        det *= A[i][i];
        
        // Eliminate
        for (int j = i + 1; j < n; j++) {
            double factor = A[j][i] / A[i][i];
            for (int k = i; k < n; k++) {
                A[j][k] -= factor * A[i][k];
            }
        }
    }
    
    return det;
}
```

**Time complexity:** $O(n^3)$.

### Determinant Modulo a Prime Number

```cpp
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
        long long inv = modInverse(A[i][i], mod);
        
        for (int j = i + 1; j < n; j++) {
            long long factor = (A[j][i] * inv) % mod;
            for (int k = i; k < n; k++) {
                A[j][k] = ((A[j][k] - factor * A[i][k]) % mod + mod) % mod;
            }
        }
    }
    
    return det;
}
```

## 💡 Practical Tips and Optimizations

### Numerical Stability

```cpp
// Use partial pivoting to avoid division by small numbers
int findPivot(vector<vector<double>>& A, int col, int start) {
    int pivot = start;
    double max_val = abs(A[start][col]);
    
    for (int i = start + 1; i < (int)A.size(); i++) {
        if (abs(A[i][col]) > max_val) {
            max_val = abs(A[i][col]);
            pivot = i;
        }
    }
    
    return pivot;
}
```

### Memory Optimization

```cpp
// For very large systems use sparse matrix representation
struct SparseMatrix {
    map<pair<int,int>, double> entries;
    int rows, cols;
    
    double get(int i, int j) {
        auto it = entries.find({i, j});
        return (it == entries.end()) ? 0.0 : it->second;
    }
    
    void set(int i, int j, double val) {
        if (abs(val) > EPS) {
            entries[{i, j}] = val;
        } else {
            entries.erase({i, j});
        }
    }
};
```

### Bitset Optimization for GF(2)

```cpp
// For binary matrices use bitset for 64x speedup
const int MAXN_BIT = 2000;
bitset<MAXN_BIT> bit_matrix[MAXN_BIT];

int gaussianEliminationBinary(int n, int m) {
    int rank = 0;
    
    for (int col = 0; col < m && rank < n; col++) {
        int pivot = -1;
        for (int i = rank; i < n; i++) {
            if (bit_matrix[i][col]) {
                pivot = i;
                break;
            }
        }
        
        if (pivot == -1) continue;
        
        swap(bit_matrix[rank], bit_matrix[pivot]);
        
        for (int i = 0; i < n; i++) {
            if (i != rank && bit_matrix[i][col]) {
                bit_matrix[i] ^= bit_matrix[rank];  // XOR entire rows at once!
            }
        }
        
        rank++;
    }
    
    return rank;
}
```

## 🎯 Common Tasks and Solutions

### Task 1: Linear Independence Check
```cpp
bool areIndependent(vector<vector<double>>& vectors) {
    // If rank is equal to number of vectors, they are independent
    return computeRank(vectors) == (int)vectors.size();
}
```

### Task 2: Finding All Solutions
```cpp
// Return parametric form of all solutions to Ax = b
struct Solution {
    vector<double> particular;  // One concrete solution
    vector<vector<double>> null_space;  // Basis for the null space
};
```

### Task 3: Matrix Inversion
```cpp
vector<vector<double>> invert(vector<vector<double>> A) {
    int n = A.size();
    vector<vector<double>> inv(n, vector<double>(n));
    
    // Create augmented matrix [A | I]
    for (int i = 0; i < n; i++) {
        inv[i][i] = 1;
    }
    
    // Apply Gauss-Jordan to both sides
    // ... (similar to gaussJordan but works on A and inv)
    
    return inv;
}
```

---

## 🏁 Conclusion

Linear algebra in competitive programming focuses on practical algorithmic techniques:

### Key Takeaways:

1. **Matrix Rank**: Fundamental measure of linear independence.
   - Calculated via Gaussian elimination in $O(n^3)$.
   - Determines existence and uniqueness of solutions.

2. **Gaussian Elimination**: Core algorithm for linear systems.
   - Forward elimination + backward substitution.
   - Works with floating point, integers modulo p, or binary.

3. **XOR Basis**: Special case over $GF(2)$.
   - Gaussian elimination on bit vectors.
   - Applications: max XOR, distinct XOR values, reachability.

4. **Determinants**: Encode invertibility and volume.
   - Calculated during Gaussian elimination.
   - Useful for matrix inversion and area calculation.

### Complexity Summary:

| Operation | Time Complexity | Memory |
|-----------|-----------------|--------|
| Gaussian elimination | $O(n^3)$ | $O(n^2)$ |
| Matrix rank | $O(n^2 m)$ | $O(nm)$ |
| Determinant | $O(n^3)$ | $O(n^2)$ |
| XOR basis insertion | $O(\log \max(x))$ | $O(\log \max(x))$ |
| XOR basis query | $O(\log \max(x))$ | $O(\log \max(x))$ |

### Common Competitive Patterns:

- **DP with matrix exponentiation** (covered in other topics).
- **XOR basis** for tasks with XOR subsets.
- **Gaussian elimination modulo p** for linear recurrences.
- **Determinant** for counting spanning trees (Matrix-Tree theorem).

In competitions, "Linear Algebra" **rarely** means eigenvalues, eigenvectors, or SVD. It almost always means:
- Matrix multiplication for DP.
- Gaussian elimination for linear systems.
- XOR basis for bitwise tasks.
- Simple determinant calculations.

Master these basic techniques and you will be well-prepared to tackle linear algebra tasks in competitive programming!