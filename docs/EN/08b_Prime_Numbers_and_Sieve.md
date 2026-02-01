# 🔢 Prime Numbers and Sieve of Eratosthenes: Advanced Theory

Primes are the basic building blocks of the integers. A solid understanding of how to find, count, and use them is vital for number theory problems in competitive programming.

---

## 1. Primality Testing

### 1.1. Trial Division ($O(\sqrt{N})$)
The simplest way to check if a number $N$ is prime is to try dividing it by every integer from 2 up to $\sqrt{N}$. If any integer divides $N$ evenly, $N$ is composite.

```cpp
bool isPrime(long long n) {
    if (n < 2) return false;
    if (n < 4) return true;
    if (n % 2 == 0 || n % 3 == 0) return false;
    for (long long i = 5; i * i <= n; i += 6) {
        if (n % i == 0 || n % (i + 2) == 0) return false;
    }
    return true;
}
```

### 1.2. Miller-Rabin Test (Probabilistic)
For $N$ up to $10^{18}$, trial division is too slow. The Miller-Rabin test allows checking primality in $O(k \log^3 N)$ time.

---

## 2. Sieve of Eratosthenes

The Sieve of Eratosthenes finds all prime numbers up to a specified limit $N$.

### 2.1. Standard Sieve ($O(N \log \log N)$)
```cpp
const int MAXN = 10000000; // 10^7
bool is_prime[MAXN + 1];

void sieve() {
    fill(is_prime, is_prime + MAXN + 1, true);
    is_prime[0] = is_prime[1] = false;
    for (int p = 2; p * p <= MAXN; p++) {
        if (is_prime[p]) {
            for (int i = p * p; i <= MAXN; i += p)
                is_prime[i] = false;
        }
    }
}
```

### 2.2. Linear Sieve ($O(N)$)
A linear sieve computes the **smallest prime factor (lp)** for every number up to $N$. This is extremely useful for fast prime factorization.

```cpp
int lp[MAXN + 1];
vector<int> primes;

void linear_sieve() {
    for (int i = 2; i <= MAXN; ++i) {
        if (lp[i] == 0) {
            lp[i] = i;
            primes.push_back(i);
        }
        for (int p : primes) {
            if (p > lp[i] || i * p > MAXN) break;
            lp[i * p] = p;
        }
    }
}
```

---

## 3. Fundamental Theorem of Arithmetic

Every integer $N > 1$ can be uniquely represented as a product of prime powers:
$$N = p_1^{a_1} p_2^{a_2} \dots p_k^{a_k}$$

### 3.1. Count of Divisors ($\tau(N)$)
$$\tau(N) = (a_1 + 1)(a_2 + 1) \dots (a_k + 1)$$

### 3.2. Sum of Divisors ($\sigma(N)$)
$$\sigma(N) = \frac{p_1^{a_1+1}-1}{p_1-1} \cdot \frac{p_2^{a_2+1}-1}{p_2-1} \dots$$

### 3.3. Euler's Totient Function ($\phi(N)$)
The number of integers $1 \le k \le N$ such that $\gcd(k, N) = 1$.
$$\phi(N) = N \prod_{i=1}^k \left(1 - \frac{1}{p_i}\right)$$

---

## 4. Segmented Sieve
If you need to find primes in a range $[L, R]$ where $R$ is large (e.g., $10^{12}$) but the range $R - L$ is small (e.g., $10^6$):
1.  Sieve all primes up to $\sqrt{R}$.
2.  Use these primes to mark multiples in the range $[L, R]$.

---

## 5. Practice Problems
1.  **Codeforces 230B**: T-primes (Numbers with exactly 3 divisors).
2.  **CSES Counting Divisors**: Requires linear sieve for fast factorization.
3.  **CSES Sum of Divisors**: Sum of $\sigma(i)$ for all $i$ from 1 to $N$.
4.  **UVa 10140**: Prime Distance (Segmented Sieve).

## 6. Conclusion
The linear sieve is the superior tool for $N \le 10^7$ because it precomputes the smallest prime factor, which solves many other multiplicative function problems. For larger $N$, use Miller-Rabin or Segmented Sieve.
