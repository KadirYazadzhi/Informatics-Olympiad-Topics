# ➗ Divisibility and the Euclidean Algorithm

Number Theory is a branch of mathematics that is deeply embedded in computer science. Understanding the properties of integers, divisibility, and remainders is mandatory for competitive programming.

## 1. Divisibility Rules

A quick check of whether a number $N$ is divisible by $K$ without performing the division (useful for long numbers).

*   **2**: The last digit is even (0, 2, 4, 6, 8).
*   **3**: The sum of the digits is divisible by 3.
*   **4**: The number formed by the last two digits is divisible by 4.
*   **5**: The last digit is 0 or 5.
*   **6**: It is simultaneously divisible by 2 and by 3.
*   **8**: The number formed by the last three digits is divisible by 8.
*   **9**: The sum of the digits is divisible by 9.
*   **10**: The last digit is 0.
*   **11**: The difference between the sum of the digits at odd positions and the sum of those at even positions is divisible by 11 (or is 0).
    *   Example: 132 $\to (1+2) - 3 = 0 \implies$ it is divisible.

## 2. Greatest Common Divisor (GCD)

The largest number that divides both $A$ and $B$ without a remainder.

### 2.1. Euclidean Algorithm
This is one of the oldest and most important algorithms. It is based on the fact that $\gcd(a, b) = \gcd(b, a \pmod b)$.

```cpp
// Recursive variant
int gcd(int a, int b) {
    if (b == 0) return a;
    return gcd(b, a % b);
}

// Iterative variant
int gcd_iter(int a, int b) {
    while (b) {
        a %= b;
        swap(a, b);
    }
    return a;
}
```

*   **Built-in function**: In C++ (`<algorithm>` or `<numeric>`), there is a function `std::gcd(a, b)` (from C++17) or `__gcd(a, b)` (GCC built-in, works in older versions too).

### 2.2. Properties of GCD
*   $\gcd(a, b) = \gcd(a, b-a)$
*   $\gcd(k \cdot a, k \cdot b) = k \cdot \gcd(a, b)$
*   $\gcd(a, b, c) = \gcd(a, \gcd(b, c))$

## 3. Least Common Multiple (LCM)

The smallest number that is simultaneously divisible by $A$ and $B$.
Formula:
$$\text{lcm}(a, b) = \frac{|a \cdot b|}{\gcd(a, b)}$$

When programming, always divide first, then multiply, to avoid type overflow:
```cpp
long long lcm(long long a, long long b) {
    if (a == 0 || b == 0) return 0;
    return (a / gcd(a, b)) * b;
}
```
*   **Built-in function**: `std::lcm(a, b)` (C++17).

## 4. Extended Euclidean Algorithm

In addition to the GCD, the algorithm can also find the coefficients $x$ and $y$ in Bézout's identity:
$a \cdot x + b \cdot y = \gcd(a, b)$

This is key for finding the **Modular Multiplicative Inverse** when performing modular division.

```cpp
int extended_gcd(int a, int b, int &x, int &y) {
    if (b == 0) {
        x = 1; y = 0;
        return a;
    }
    int x1, y1;
    int d = extended_gcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - y1 * (a / b);
    return d;
}
```

## 6. Prime Numbers

A **prime number** is a natural number greater than 1 that is divisible only by 1 and itself.

### 6.1. Primality Test - Naive Method
```cpp
bool isPrime(int n) {
    if (n <= 1) return false;
    if (n <= 3) return true;
    if (n % 2 == 0 || n % 3 == 0) return false;
    
    for (int i = 5; i * i <= n; i += 6) {
        if (n % i == 0 || n % (i + 2) == 0)
            return false;
    }
    return true;
}
```

**Complexity**: $O(\sqrt{n})$ - we check only up to the square root of n.

### 6.2. Sieve of Eratosthenes

For finding all prime numbers up to N:
```cpp
vector<bool> sieve(int n) {
    vector<bool> prime(n + 1, true);
    prime[0] = prime[1] = false;
    
    for (int i = 2; i * i <= n; i++) {
        if (prime[i]) {
            for (int j = i * i; j <= n; j += i) {
                prime[j] = false;
            }
        }
    }
    return prime;
}
```

**Complexity**: $O(n \log \log n)$ - very efficient for multiple checks.

### 6.3. Integer Factorization

Decomposition of a number into prime factors:
```cpp
vector<pair<int, int>> primeFactors(int n) {
    vector<pair<int, int>> factors; // {prime divisor, power}
    
    for (int i = 2; i * i <= n; i++) {
        int count = 0;
        while (n % i == 0) {
            count++;
            n /= i;
        }
        if (count > 0) {
            factors.push_back({i, count});
        }
    }
    
    if (n > 1) {
        factors.push_back({n, 1}); // The remaining number is prime
    }
    
    return factors;
}
```

### 6.4. Number of Divisors

Using factorization: If $n = p_1^{a_1} \cdot p_2^{a_2} \cdot ... \cdot p_k^{a_k}$, then:
$$d(n) = (a_1 + 1) \cdot (a_2 + 1) \cdot ... \cdot (a_k + 1)$$

```cpp
int countDivisors(int n) {
    int count = 1;
    
    for (int i = 2; i * i <= n; i++) {
        int exp = 0;
        while (n % i == 0) {
            exp++;
            n /= i;
        }
        count *= (exp + 1);
    }
    
    if (n > 1) count *= 2; // The remaining prime number
    
    return count;
}
```

### 6.5. Sum of Divisors

Formula: $\sigma(n) = \frac{p_1^{a_1+1} - 1}{p_1 - 1} \cdot \frac{p_2^{a_2+1} - 1}{p_2 - 1} \cdot ...$

```cpp
long long sumOfDivisors(int n) {
    long long sum = 1;
    
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            int exp = 0;
            while (n % i == 0) {
                exp++;
                n /= i;
            }
            // Sum of geometric progression: (p^(e+1) - 1) / (p - 1)
            sum *= (pow(i, exp + 1) - 1) / (i - 1);
        }
    }
    
    if (n > 1) sum *= (n + 1);
    
    return sum;
}
```

## 7. Modular Arithmetic - Basics

### 7.1. Modular Addition and Multiplication
```cpp
long long modAdd(long long a, long long b, long long mod) {
    return ((a % mod) + (b % mod)) % mod;
}

long long modMul(long long a, long long b, long long mod) {
    return ((a % mod) * (b % mod)) % mod;
}
```

### 7.2. Modular Exponentiation (Fast Exponentiation)

Calculating $a^b \bmod m$ in $O(\log b)$:
```cpp
long long modPow(long long base, long long exp, long long mod) {
    long long result = 1;
    base %= mod;
    
    while (exp > 0) {
        if (exp % 2 == 1) {
            result = (result * base) % mod;
        }
        base = (base * base) % mod;
        exp /= 2;
    }
    
    return result;
}
```

### 7.3. Modular Inverse

Finding $x$ such that $(a \cdot x) \bmod m = 1$:

**Method 1**: Using Extended Euclidean Algorithm
```cpp
long long modInverse(long long a, long long m) {
    int x, y;
    int g = extended_gcd(a, m, x, y);
    
    if (g != 1) return -1; // Inverse does not exist
    
    return (x % m + m) % m; // Normalize to a positive range
}
```

**Method 2**: For prime modules (by Fermat's Little Theorem)
```cpp
long long modInverse(long long a, long long p) {
    // a^(-1) = a^(p-2) mod p, if p is prime
    return modPow(a, p - 2, p);
}
```

## 8. Combinatorial Applications

### 8.1. Factorial modulo
```cpp
const int MOD = 1e9 + 7;
const int MAXN = 1e6;
long long fact[MAXN];

void precomputeFactorials() {
    fact[0] = 1;
    for (int i = 1; i < MAXN; i++) {
        fact[i] = (fact[i-1] * i) % MOD;
    }
}
```

### 8.2. Binomial Coefficients C(n, k) = n! / (k! * (n-k)!)
```cpp
long long binomialCoeff(int n, int k, int mod) {
    if (k > n) return 0;
    
    long long num = fact[n];
    long long den = (fact[k] * fact[n - k]) % mod;
    
    return (num * modInverse(den, mod)) % mod;
}
```

## 9. Chinese Remainder Theorem (CRT)

Solving a system of congruences:
- $x \equiv a_1 \pmod{m_1}$
- $x \equiv a_2 \pmod{m_2}$
- ...
- $x \equiv a_k \pmod{m_k}$

where $m_1, m_2, ..., m_k$ are pairwise coprime.

```cpp
long long chineseRemainder(vector<int> num, vector<int> rem) {
    long long prod = 1;
    for (int i = 0; i < num.size(); i++) {
        prod *= num[i];
    }
    
    long long result = 0;
    for (int i = 0; i < num.size(); i++) {
        long long pp = prod / num[i];
        result += rem[i] * modInverse(pp, num[i]) * pp;
    }
    
    return result % prod;
}
```

## 10. Euler's Totient Function φ(n)

The number of integers from 1 to n that are coprime to n.

Formula: $\phi(n) = n \cdot \prod_{p|n} (1 - \frac{1}{p})$

```cpp
int phi(int n) {
    int result = n;
    
    for (int p = 2; p * p <= n; p++) {
        if (n % p == 0) {
            while (n % p == 0) {
                n /= p;
            }
            result -= result / p;
        }
    }
    
    if (n > 1) {
        result -= result / n;
    }
    
    return result;
}
```

**Application**: Fermat's Little Theorem generalized (Euler's Theorem):
$$a^{\phi(m)} \equiv 1 \pmod{m}$$

## 11. Practical Tasks

1.  **Finding GCD of 3 numbers**: $\gcd(a, b, c) = \gcd(a, \gcd(b, c))$
2.  **Perfect number**: The number is equal to the sum of its own proper divisors (e.g., 6 = 1 + 2 + 3)
3.  **Armstrong numbers**: $153 = 1^3 + 5^3 + 3^3$
4.  **Solving Linear Diophantine Equations**: $ax + by = c$ has a solution $\iff \gcd(a,b) | c$

## 💡 Important Notes

- When working with large numbers, always use `long long`.
- In modular arithmetic, apply the modulo after every operation.
- GCD and LCM are encountered in many Olympiad tasks - learn them by heart.
- The Extended Euclidean Algorithm is key for the modular inverse.

## 🏁 Advice

Number theory is fundamental in competitive programming. All these algorithms are frequently used in tasks from IOI, CEOI, Balkan Olympiads, and Codeforces. Practice regularly and always remember edge cases like n=0, n=1, and negative numbers!