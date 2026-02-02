# 🔢 Special Number Sequences: Theory and Applications

## 📚 Introduction

In combinatorics and number theory, there are sequences that appear in hundreds of different contexts. Knowing them allows you to recognize the structure of a task as soon as you read it. These sequences are not just mathematical curiosities - they are key tools for solving complex Olympiad tasks.

When you see that the first few answers to a task are $1, 2, 5, 14, 42$, it is almost certainly about the Catalan numbers. Recognizing such patterns can save you hours of thought and guide you directly to the correct approach.

---

## 1. Catalan Numbers

The sequence begins as follows: $1, 1, 2, 5, 14, 42, 132, 429, 1430, \dots$ for $n = 0, 1, 2, \dots$.

### 1.1. Formulas
The most popular formula is:
$$C_n = \frac{1}{n+1} \binom{2n}{n} = \frac{(2n)!}{(n+1)!n!}$$ 

The recurrence relation, which is frequently used in DP:
$$C_{n+1} = \sum_{i=0}^{n} C_i C_{n-i}$$ 

### 1.2. Classical Applications
Catalan numbers are the answer to the following questions:
1.  **Valid Parentheses Expressions**: How many ways are there to arrange $n$ pairs of opening and closing parentheses?
2.  **Binary Trees**: How many different binary trees with $n$ nodes are there?
3.  **Triangulation**: In how many ways can we divide a convex $(n+2)$-gon into triangles?
4.  **Lattice Paths (Dyck Paths)**: How many paths are there from $(0,0)$ to $(n,n)$ in a square grid that do not cross the diagonal $y=x$?

### 1.3. Implementation and Calculations

**Direct calculation with the formula:**

```cpp
// Calculating C_n directly
long long catalan(int n) {
    long long res = 1;
    for (int i = 0; i < n; i++) {
        res *= (2 * n - i);
        res /= (i + 1);
    }
    return res / (n + 1);
}
```

**Dynamic calculation with recursion:**

```cpp
const int MAXN = 100;
long long C[MAXN];

void computeCatalan(int n) {
    C[0] = C[1] = 1;
    for (int i = 2; i <= n; i++) {
        C[i] = 0;
        for (int j = 0; j < i; j++) {
            C[i] += C[j] * C[i - 1 - j];
        }
    }
}
```

**Modular arithmetic (for large numbers):**

```cpp
const int MOD = 1e9 + 7;

long long modpow(long long base, long long exp, long long mod) {
    long long res = 1;
    while (exp > 0) {
        if (exp % 2) res = (res * base) % mod;
        base = (base * base) % mod;
        exp /= 2;
    }
    return res;
}

long long modinv(long long a, long long mod) {
    return modpow(a, mod - 2, mod);  // Works for prime moduli
}

long long catalanMod(int n) {
    vector<long long> fact(2 * n + 1);
    fact[0] = 1;
    for (int i = 1; i <= 2 * n; i++) {
        fact[i] = (fact[i - 1] * i) % MOD;
    }
    
    long long num = fact[2 * n];
    long long den = (fact[n + 1] * fact[n]) % MOD;
    return (num * modinv(den, MOD)) % MOD;
}
```

### 1.4. Practical Example: Valid Parentheses

```cpp
// Generating all valid parentheses sequences
void generateParens(int n, int open, int close, string curr, vector<string>& result) {
    if (curr.length() == 2 * n) {
        result.push_back(curr);
        return;
    }
    
    if (open < n) {
        generateParens(n, open + 1, close, curr + "(", result);
    }
    if (close < open) {
        generateParens(n, open, close + 1, curr + ")", result);
    }
}
```

---

## 2. Fibonacci Numbers

Defined by: $F_0 = 0, F_1 = 1, F_n = F_{n-1} + F_{n-2}$.

### 2.1. Fast Calculation ($O(\log N)$)
For very large $N$ ($10^{18}$), we use matrix exponentiation:
$$ \begin{pmatrix} F_{n+1} \\ F_n \end{pmatrix} = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^n \begin{pmatrix} 1 \\ 0 \end{pmatrix} $$ 

### 2.2. Properties
1.  **Sum**: $\sum_{i=1}^n F_i = F_{n+2} - 1$.
2.  **Pisano Period**: Fibonacci remainders modulo $M$ form a cycle.
3.  **Golden Ratio**: The limit of $F_{n+1}/F_n$ is $\phi \approx 1.618$.
4.  **Binet's Formula**: $F_n = \frac{\phi^n - \psi^n}{\sqrt{5}}$ where $\phi = \frac{1+\sqrt{5}}{2}$ and $\psi = \frac{1-\sqrt{5}}{2}$.
5.  **GCD Property**: $\gcd(F_m, F_n) = F_{\gcd(m,n)}$.

### 2.3. Matrix Exponentiation Implementation

```cpp
struct Matrix {
    long long a[2][2];
    
    Matrix() {
        memset(a, 0, sizeof(a));
    }
    
    Matrix operator*(const Matrix& other) const {
        Matrix res;
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                for (int k = 0; k < 2; k++) {
                    res.a[i][j] += a[i][k] * other.a[k][j];
                    res.a[i][j] %= MOD;
                }
            }
        }
        return res;
    }
};

Matrix matpow(Matrix base, long long exp) {
    Matrix res;
    res.a[0][0] = res.a[1][1] = 1;  // Identity matrix
    
    while (exp > 0) {
        if (exp % 2) res = res * base;
        base = base * base;
        exp /= 2;
    }
    return res;
}

long long fibonacci(long long n) {
    if (n <= 1) return n;
    
    Matrix base;
    base.a[0][0] = base.a[0][1] = base.a[1][0] = 1;
    base.a[1][1] = 0;
    
    Matrix res = matpow(base, n);
    return res.a[0][1];
}
```

### 2.4. Applications of Fibonacci in Tasks

**Example 1: Number of ways to climb N steps (you can step on 1 or 2 steps at once)**

The answer is $F_{n+1}$ (the n-th Fibonacci number).

**Example 2: Tiling a 2×N board with 1×2 tiles**

The answer is $F_{n+1}$.

```cpp
// DP solution for board tiling
int dp[MAXN];

int tileDp(int n) {
    dp[0] = 1;
    dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        dp[i] = (dp[i-1] + dp[i-2]) % MOD;
    }
    return dp[n];
}
```

**Example 3: Zeckendorf's Representation**

Every positive integer can be uniquely represented as a sum of non-consecutive Fibonacci numbers (i.e., we do not use two consecutive Fibonacci numbers).

```cpp
vector<int> zeckendorf(int n) {
    vector<int> fibs;
    fibs.push_back(1);
    fibs.push_back(2);
    
    while (fibs.back() < n) {
        int sz = fibs.size();
        fibs.push_back(fibs[sz-1] + fibs[sz-2]);
    }
    
    vector<int> result;
    for (int i = fibs.size() - 1; i >= 0; i--) {
        if (n >= fibs[i]) {
            result.push_back(fibs[i]);
            n -= fibs[i];
        }
    }
    return result;
}
```

---

## 3. Stirling Numbers

### 3.1. Second Kind $S(n, k)$
The number of ways to partition a set of $n$ elements into $k$ **non-empty** subsets.
*   **Recursion**: $S(n, k) = k \cdot S(n-1, k) + S(n-1, k-1)$.
    *   *Explanation*: The new element either enters one of the existing $k$ subsets or creates a new one alone.

### 3.2. First Kind $c(n, k)$
The number of permutations of $n$ elements that consist of exactly $k$ cycles.
*   **Recursion**: $c(n, k) = (n-1)c(n-1, k) + c(n-1, k-1)$.

### 3.3. Implementation

```cpp
// Stirling numbers of the second kind
long long stirling2[MAXN][MAXN];

void computeStirling2(int n) {
    stirling2[0][0] = 1;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= i; j++) {
            stirling2[i][j] = j * stirling2[i-1][j] + stirling2[i-1][j-1];
            stirling2[i][j] %= MOD;
        }
    }
}

// Stirling numbers of the first kind
long long stirling1[MAXN][MAXN];

void computeStirling1(int n) {
    stirling1[0][0] = 1;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= i; j++) {
            stirling1[i][j] = (i-1) * stirling1[i-1][j] + stirling1[i-1][j-1];
            stirling1[i][j] %= MOD;
        }
    }
}
```

### 3.4. Applications

**Example: Partitioning a set of people into groups**

We have $n$ people and want to divide them into $k$ non-empty groups (order does not matter). The answer is $S(n, k)$.

---

## 4. Bell Numbers $B_n$

The total number of all possible partitions of a set of $n$ elements (into an indefinite number of subsets).
$$B_n = \sum_{k=0}^n S(n, k)$$
Recursion: $B_{n+1} = \sum_{k=0}^n \binom{n}{k} B_k$.

### 4.1. Implementation

```cpp
long long bell[MAXN];

void computeBell(int n) {
    // We use the already calculated Stirling numbers
    for (int i = 0; i <= n; i++) {
        bell[i] = 0;
        for (int j = 0; j <= i; j++) {
            bell[i] += stirling2[i][j];
            bell[i] %= MOD;
        }
    }
}

// Alternative method with Bell triangle
void computeBellTriangle(int n) {
    long long B[MAXN][MAXN];
    B[0][0] = 1;
    
    for (int i = 1; i <= n; i++) {
        B[i][0] = B[i-1][i-1];
        for (int j = 1; j <= i; j++) {
            B[i][j] = (B[i-1][j-1] + B[i][j-1]) % MOD;
        }
    }
    
    for (int i = 0; i <= n; i++) {
        bell[i] = B[i][0];
    }
}
```

### 4.2. Application

How many different ways are there to group $n$ students (regardless of the size of the groups)?

---

## 5. Integer Partitions

The number of ways to represent a number $n$ as a sum of positive integers (order does not matter).

### 5.1. Formulas and Recursions

Let $p(n)$ be the number of partitions of $n$, and $p(n, k)$ be the number of partitions of $n$ using numbers at most $k$.

**Recursion:**
$$p(n, k) = p(n, k-1) + p(n-k, k)$$

Explanation: We either do not use $k$ in the partition, or we use it at least once.

### 5.2. Implementation

```cpp
int partition_table[MAXN][MAXN];

void computePartitions(int n) {
    // p(n, k) = number of partitions of n using numbers <= k
    for (int i = 0; i <= n; i++) {
        partition_table[0][i] = 1;  // Empty partition
    }
    
    for (int num = 1; num <= n; num++) {
        for (int k = 1; k <= n; k++) {
            partition_table[num][k] = partition_table[num][k-1];
            if (num >= k) {
                partition_table[num][k] += partition_table[num - k][k];
                partition_table[num][k] %= MOD;
            }
        }
    }
}

// Number of all partitions of n
int totalPartitions(int n) {
    return partition_table[n][n];
}
```

### 5.3. Generating Functions

The generating function for partitions is:
$$\prod_{k=1}^{\infty} \frac{1}{1-x^k}$$

This leads to Euler's pentagonal number formula:
$$p(n) = p(n-1) + p(n-2) - p(n-5) - p(n-7) + p(n-12) + \dots$$

---

## 5. Bernoulli Numbers

Mainly used for summing powers: $1^k + 2^k + \dots + n^k$. Faulhaber's formula gives a polynomial for this sum, whose coefficients depend on Bernoulli numbers.

### 5.1. Definition and Basic Properties

Bernoulli numbers $B_n$ are defined via the generating function:
$$\frac{x}{e^x - 1} = \sum_{n=0}^{\infty} B_n \frac{x^n}{n!}$$

The first few Bernoulli numbers:
- $B_0 = 1$
- $B_1 = -\frac{1}{2}$
- $B_2 = \frac{1}{6}$
- $B_4 = -\frac{1}{30}$
- $B_6 = \frac{1}{42}$

Note: $B_n = 0$ for all odd $n > 1$.

### 5.2. Faulhaber's Formula

The sum of $k$-th powers can be expressed as:
$$\sum_{i=1}^{n} i^k = \frac{1}{k+1} \sum_{j=0}^{k} \binom{k+1}{j} B_j n^{k+1-j}$$

For example:
- $\sum_{i=1}^{n} i = \frac{n(n+1)}{2}$
- $\sum_{i=1}^{n} i^2 = \frac{n(n+1)(2n+1)}{6}$
- $\sum_{i=1}^{n} i^3 = \left(\frac{n(n+1)}{2}\right)^2$

---

## 6. Derangements

A derangement is a permutation in which no element is in its original position. The number of derangements of $n$ elements is denoted by $!n$ or $D_n$.

### 6.1. Formulas

**Recursive formula:**
$$D_n = (n-1)(D_{n-1} + D_{n-2})$$

**Inclusion-exclusion formula:**
$$D_n = n! \sum_{i=0}^{n} \frac{(-1)^i}{i!}$$

This is approximately $\frac{n!}{e}$ for large $n$.

### 6.2. Implementation

```cpp
long long derangement[MAXN];

void computeDerangements(int n) {
    derangement[0] = 1;
    derangement[1] = 0;
    
    for (int i = 2; i <= n; i++) {
        derangement[i] = (i - 1) * (derangement[i-1] + derangement[i-2]);
        derangement[i] %= MOD;
    }
}
```

### 6.3. Application

**The secretary problem**: $n$ secretaries write letters and envelopes for them. In how many ways can they put the letters in the envelopes such that no letter is in the correct envelope?

The answer is $D_n$.

---

## 6. Practice Tasks
1.  **SPOJ SKYLINE**: Buildings (Catalan).
2.  **CSES Fibonacci Numbers**: Matrix exponentiation.
3.  **Codeforces 990C**: Bracket Sequences (Variation).
4.  **UVa 10601**: Cubes (Combining ideas).
5.  **Project Euler 15**: Lattice Paths (Catalan application).
6.  **Codeforces 414B**: Mashmokh and ACM (Partitions).
7.  **SPOJ PARTSUM**: Partition Sums (Dynamic programming with partitions).

---

## 7. How to Recognize the Sequence

### 7.1. OEIS (Online Encyclopedia of Integer Sequences)

This is the most powerful tool for recognizing integer sequences. When solving a task:

1. Calculate the first 5-10 values for small inputs.
2. Enter the sequence in [oeis.org](https://oeis.org).
3. Check whether your sequence matches a known one.

**Example**: You enter `1, 2, 5, 14, 42` and find A000108 (Catalan numbers).

### 7.2. Indicators for Different Sequences

**Catalan ($C_n$):**
- Balanced parentheses, paths, trees.
- Fast growth: $C_n \approx \frac{4^n}{n^{3/2}\sqrt{\pi}}$.
- Each member is about 4 times the previous one.

**Fibonacci ($F_n$):**
- Summation of the previous two members.
- Growth: $F_n \approx \frac{\phi^n}{\sqrt{5}}$.
- Each member is about 1.618 times the previous one.

**Stirling ($S(n,k)$):**
- Partitioning sets or cycles in permutations.
- Triangular structure (similar to Pascal's triangle).

**Bell ($B_n$):**
- Total partitions of sets.
- Very fast growth (faster than factorial).

**Integer Partitions ($p(n)$):**
- Ways to represent a number as a sum.
- Slower than exponential growth.

### 7.3. Tips for Olympiad Tasks

1. **Always test with small inputs** and record the results.
2. **Look for a pattern** in the sequence.
3. **Check OEIS** before wasting time on inventing.
4. **Pay attention to constraints** - if $n \leq 20$, it is likely combinatorics or DP with masks.
5. **Look at the growth** - if the numbers grow very quickly, it could be factorial or Catalan.

---

## 🏁 Conclusion

If you notice that the first few answers to a task are $1, 2, 5, 14, 42$, it is almost certain that the answer is a Catalan number. Always check your small cases in **OEIS** (Online Encyclopedia of Integer Sequences).

Special number sequences are not just theoretical concepts - they are practical tools that can save hours of thought in a competition. Invest time in learning them because they appear again and again in various forms.

**Key points:**
- Remember the first few members of the basic sequences.
- Learn how to calculate them efficiently (especially modulo).
- Always check OEIS for unrecognized sequences.
- Understand the applications deeply - not just the formulas.
- Practice with real tasks from competitions.