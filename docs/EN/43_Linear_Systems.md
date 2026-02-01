# 📉 Linear Systems and Gaussian Elimination

Solving a system of linear equations is a classic problem.
Given $N$ equations with $M$ variables:
$a_{11}x_1 + a_{12}x_2 + \dots + a_{1m}x_m = b_1$
$\dots$
$a_{n1}x_1 + a_{n2}x_2 + \dots + a_{nm}x_m = b_n$

We want to find values for $x_1, \dots, x_m$.

## 1. Gaussian Elimination

The standard algorithm to solve this is Gaussian Elimination, which converts the augmented matrix $[A|b]$ into **Row Echelon Form**.

### Algorithm
1.  Iterate through columns $j$ from $0$ to $M-1$.
2.  Select a **pivot** row $i$ (from current row to $N-1$) which has a non-zero value in column $j$. Best to pick the one with largest absolute value for numerical stability.
3.  Swap current row with pivot row.
4.  Divide the current row by the pivot value so the pivot becomes 1.
5.  Subtract multiples of the current row from all other rows to make their column $j$ zero.

Complexity: $O(N^3)$ (or $O(N^2 M)$).

### Gauss-Jordan Elimination
Goes a step further to **Reduced Row Echelon Form**, where the pivot is the only non-zero entry in its column. This directly gives the solution.

```cpp
#include <vector>
#include <cmath>
#include <algorithm>
#include <iostream>

using namespace std;

const double EPS = 1e-9;

int gauss(vector<vector<double>> a, vector<double> &ans) {
    int n = a.size();
    int m = a[0].size() - 1;

    vector<int> where(m, -1);
    for (int col = 0, row = 0; col < m && row < n; ++col) {
        int sel = row;
        for (int i = row; i < n; ++i)
            if (abs(a[i][col]) > abs(a[sel][col]))
                sel = i;
        if (abs(a[sel][col]) < EPS)
            continue;
        
        for (int i = col; i <= m; ++i)
            swap(a[sel][i], a[row][i]);
        where[col] = row;

        for (int i = 0; i < n; ++i)
            if (i != row) {
                double c = a[i][col] / a[row][col];
                for (int j = col; j <= m; ++j)
                    a[i][j] -= a[row][j] * c;
            }
        ++row;
    }

    ans.assign(m, 0);
    for (int j = 0; j < m; ++j) {
        if (where[j] != -1)
            ans[j] = a[where[j]][m] / a[where[j]][j];
    }

    for (int i = 0; i < n; ++i) {
        double sum = 0;
        for (int j = 0; j < m; ++j)
            sum += ans[j] * a[i][j];
        if (abs(sum - a[i][m]) > EPS)
            return 0; // No solution
    }

    for (int j = 0; j < m; ++j)
        if (where[j] == -1)
            return 2; // Infinite solutions

    return 1; // Unique solution
}
```

## 2. XOR Basis (Linear Basis)
For systems over GF(2) (boolean equations / XOR), Gaussian Elimination works similarly but using XOR instead of subtraction.
This is used to find the "Linear Basis" of a set of numbers to answer queries like "Maximum XOR subset sum".

## 3. Modulo Arithmetic
If we work modulo $P$ (where $P$ is prime), division becomes multiplication by modular inverse.
The logic remains the same.

## 4. Practice Problems
1.  **Rosalind GAUS**: Gaussian Elimination.
2.  **SPOJ XORDEC**: XOR Basis.
3.  **Codeforces 1100F**: Linear Basis with queries.