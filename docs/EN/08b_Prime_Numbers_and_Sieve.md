# 🔢 Prime Numbers and the Sieve of Eratosthenes

Prime numbers are fundamental objects in number theory and cryptography. They are the "atoms" of arithmetic - every natural number decomposes uniquely into prime factors (Fundamental Theorem of Arithmetic).

**Definition**: A prime number is a natural number $p > 1$ that has exactly two divisors: 1 and itself.

**Examples**: 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47...

---\n
## 1. Primality Test

### 1.1. Naive Method: $O(\sqrt{N})$

We check whether the number has divisors from 2 to $\sqrt{N}$.

**Why up to $\sqrt{N}$?** If $N = a \cdot b$ and $a \leq b$, then $a \leq \sqrt{N}$. Consequently, if there is a divisor, at least one of them is $\leq \sqrt{N}$.

```cpp
bool isPrime(long long n) {
    if (n < 2) return false;
    if (n == 2) return true;
    if (n % 2 == 0) return false; // Optimization for even numbers
    
    for (long long i = 3; i * i <= n; i += 2) {
        if (n % i == 0) return false;
    }
    return true;
}
```

**Optimizations**:
- Check only odd divisors after 2.
- Use `i * i <= n` instead of `i <= sqrt(n)` to avoid the `sqrt` operation.

### 1.2. Optimized Check with Prime Numbers Only

If we already have a list of prime numbers, we can check only with them:

```cpp
vector<long long> primes = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31};

bool isPrimeFast(long long n) {
    if (n < 2) return false;
    for (long long p : primes) {
        if (p * p > n) break;
        if (n % p == 0) return (n == p);
    }
    return true;
}
```

### 1.3. Miller-Rabin: $O(k \log^3 N)$

A probabilistic test for very large numbers (up to $10^{18}$). It is based on Fermat's Theorem.

**Principle**: If $p$ is prime and $a$ is not divisible by $p$, then $a^{p-1} \equiv 1 \pmod{p}$.

```cpp
long long mulmod(long long a, long long b, long long mod) {
    return (__int128)a * b % mod;
}

long long powmod(long long base, long long exp, long long mod) {
    long long result = 1;
    while (exp > 0) {
        if (exp % 2 == 1) result = mulmod(result, base, mod);
        base = mulmod(base, base, mod);
        exp /= 2;
    }
    return result;
}

bool millerRabin(long long n, long long a) {
    if (n % a == 0) return n == a;
    
    long long d = n - 1;
    int r = 0;
    while (d % 2 == 0) {
        d /= 2;
        r++;
    }
    
    long long x = powmod(a, d, n);
    if (x == 1 || x == n - 1) return true;
    
    for (int i = 0; i < r - 1; i++) {
        x = mulmod(x, x, n);
        if (x == n - 1) return true;
    }
    return false;
}

bool isPrime(long long n) {
    if (n < 2) return false;
    if (n == 2 || n == 3) return true;
    if (n % 2 == 0) return false;
    
    // Testing with fixed bases that work up to 2^64
    vector<long long> bases = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37};
    for (long long a : bases) {
        if (!millerRabin(n, a)) return false;
    }
    return true;
}
```

---\n
## 2. Sieve of Eratosthenes

Finds all prime numbers up to $N$ in $O(N \log \log N)$ time.

**Idea**: We take the first unsieved number (it is prime) and cross out all its multiples.

### 2.1. Classical Implementation

```cpp
const int MAXN = 1000005;
bool is_prime[MAXN];

void sieve() {
    fill(is_prime, is_prime + MAXN, true);
    is_prime[0] = is_prime[1] = false;
    
    for (int i = 2; i * i < MAXN; i++) {
        if (is_prime[i]) {
            // Crossing out multiples of i, starting from i*i
            for (int j = i * i; j < MAXN; j += i) {
                is_prime[j] = false;
            }
        }
    }
}
```

**Why start from $i \cdot i$?** All smaller multiples ($i \cdot 2, i \cdot 3, \ldots, i \cdot (i-1)$) have already been crossed out by smaller prime numbers.

### 2.2. Optimized Sieve (Odds Only)

```cpp
const int MAXN = 1000005;
bool is_prime[MAXN / 2]; // Keeping only odd numbers

void sieveOdd() {
    fill(is_prime, is_prime + MAXN / 2, true);
    is_prime[0] = false; // 1 is not prime
    
    for (int i = 3; i * i < MAXN; i += 2) {
        if (is_prime[i / 2]) {
            for (int j = i * i; j < MAXN; j += 2 * i) {
                is_prime[j / 2] = false;
            }
        }
    }
}

bool isPrimeOdd(int n) {
    if (n == 2) return true;
    if (n < 2 || n % 2 == 0) return false;
    return is_prime[n / 2];
}
```

### 2.3. Segmented Sieve (for very large $N$)

When $N$ is too large ($> 10^8$), we use a segmented sieve:

```cpp
vector<int> smallPrimes;

void segmentedSieve(long long L, long long R) {
    // First, find primes up to sqrt(R)
    int limit = sqrt(R) + 1;
    vector<bool> mark(limit, true);
    for (int i = 2; i < limit; i++) {
        if (mark[i]) {
            smallPrimes.push_back(i);
            for (int j = i * i; j < limit; j += i)
                mark[j] = false;
        }
    }
    
    // Now sieve [L, R]
    vector<bool> isPrime(R - L + 1, true);
    for (long long p : smallPrimes) {
        long long start = max(p * p, (L + p - 1) / p * p);
        for (long long j = start; j <= R; j += p) {
            isPrime[j - L] = false;
        }
    }
    
    if (L == 1) isPrime[0] = false;
    
    // Printing primes in [L, R]
    for (long long i = L; i <= R; i++) {
        if (isPrime[i - L]) {
            cout << i << " ";
        }
    }
}
```

---\n
## 3. Linear Sieve

Finds prime numbers and the least prime divisor (`lp`) for every number in **exactly** $O(N)$.

**Key Idea**: Every composite number is crossed out exactly once - by its smallest prime divisor.

```cpp
const int MAXN = 10000005;
int lp[MAXN]; // Least Prime factor
vector<int> primes;

void linearSieve() {
    for (int i = 2; i < MAXN; ++i) {
        if (lp[i] == 0) { // i is prime
            lp[i] = i;
            primes.push_back(i);
        }
        
        // Crossing out multiples of i with prime divisors
        for (int p : primes) {
            if (p > lp[i] || i * p >= MAXN) break;
            lp[i * p] = p;
        }
    }
}
```

**Why does it work?** The key is in the condition `p > lp[i]`. It ensures that we cross out $i \cdot p$ only when $p$ is the smallest prime divisor of $i \cdot p$.

---\n
## 4. Factorization

### 4.1. With Linear Sieve ($O(\log N)$ per query)

```cpp
vector<int> getFactors(int x) {
    vector<int> factors;
    while (x > 1) {
        factors.push_back(lp[x]);
        x /= lp[x];
    }
    return factors;
}

// With occurrence count
map<int, int> getPrimeFactorization(int x) {
    map<int, int> factors;
    while (x > 1) {
        factors[lp[x]]++;
        x /= lp[x];
    }
    return factors;
}
```

### 4.2. Direct Factorization ($O(\sqrt{N})$)

```cpp
vector<pair<int, int>> factorize(long long n) {
    vector<pair<int, int>> factors;
    
    for (long long i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            int count = 0;
            while (n % i == 0) {
                n /= i;
                count++;
            }
            factors.push_back({i, count});
        }
    }
    
    if (n > 1) {
        factors.push_back({n, 1});
    }
    
    return factors;
}
```

---\n
## 5. Number and Sum of Divisors

If $N = p_1^{a_1} \cdot p_2^{a_2} \cdot \ldots \cdot p_k^{a_k}$:

### Number of divisors
$$\tau(N) = (a_1+1)(a_2+1)\cdots(a_k+1)$$

```cpp
int countDivisors(int n) {
    map<int, int> factors = getPrimeFactorization(n);
    int count = 1;
    for (auto [p, a] : factors) {
        count *= (a + 1);
    }
    return count;
}
```

### Sum of divisors
$$\sigma(N) = \frac{p_1^{a_1+1}-1}{p_1-1} \cdot \frac{p_2^{a_2+1}-1}{p_2-1} \cdots \frac{p_k^{a_k+1}-1}{p_k-1}$$

```cpp
long long sumDivisors(int n) {
    map<int, int> factors = getPrimeFactorization(n);
    long long sum = 1;
    
    for (auto [p, a] : factors) {
        long long temp = (pow(p, a + 1) - 1) / (p - 1);
        sum *= temp;
    }
    return sum;
}
```

### Finding all divisors

```cpp
vector<int> getDivisors(int n) {
    vector<int> divisors;
    for (int i = 1; i * i <= n; i++) {
        if (n % i == 0) {
            divisors.push_back(i);
            if (i != n / i) {
                divisors.push_back(n / i);
            }
        }
    }
    sort(divisors.begin(), divisors.end());
    return divisors;
}
```

---\n
## 6. Euler's Totient Function

$\\phi(n)$ is the number of integers from 1 to $n$ that are coprime to $n$.

**Formula**: If $n = p_1^{a_1} \cdot p_2^{a_2} \cdots p_k^{a_k}$, then:
$$\\phi(n) = n \cdot \left(1 - \frac{1}{p_1}\right) \cdot \left(1 - \frac{1}{p_2}\right) \cdots \left(1 - \frac{1}{p_k}\right)$$

```cpp
int phi(int n) {
    int result = n;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            while (n % i == 0) n /= i;
            result -= result / i;
        }
    }
    if (n > 1) result -= result / n;
    return result;
}
```

---\n
## 7. Practical Tips

1. **Choosing a method**:
   - For a single check of large numbers: Miller-Rabin.
   - For multiple checks in a small range: Sieve of Eratosthenes.
   - For a very large range: Segmented Sieve.
   - When factorization is needed: Linear Sieve.

2. **Optimizations**:
   - For even numbers, return `false` immediately (except for 2).
   - Use bitwise operations where possible.
   - Cache results for frequently used numbers.

---\n
## 8. Practice Tasks

1. **SPOJ TDPRIMES**: Printing some primes (Segmented Sieve).
2. **Codeforces 230B**: T-primes (Prime numbers and perfect squares).
3. **Project Euler Problem 10**: Summation of primes.
4. **CSES Counting Divisors**: Number of divisors for many numbers.
5. **Codeforces 26A**: Almost Prime (Factorization).
6. **SPOJ PRIME1**: Prime Generator (Segmented Sieve).

---\n
## 🏁 Conclusion

Prime numbers are fundamental in mathematics and cryptography. The Sieve of Eratosthenes and the Linear Sieve are must-know algorithms for every competitor. Understanding factorization and arithmetic functions opens doors to complex number theory problems.