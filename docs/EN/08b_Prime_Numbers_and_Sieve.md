# 🔢 Prime Numbers and Sieve

Primes are the building blocks of integers.

## 1. Primality Test

### 1.1. Trial Division: $O(\sqrt{N})$
```cpp
bool isPrime(long long n) {
    if (n < 2) return false;
    for (long long i = 2; i * i <= n; i++)
        if (n % i == 0) return false;
    return true;
}
```

## 2. Sieve of Eratosthenes

Finds all primes up to $N$ in $O(N \log \log N)$.
```cpp
const int MAXN = 1000005;
bool is_prime[MAXN];
void sieve() {
    fill(is_prime, is_prime+MAXN, true);
    is_prime[0] = is_prime[1] = false;
    for (int i = 2; i*i < MAXN; i++)
        if (is_prime[i])
            for (int j = i*i; j < MAXN; j += i)
                is_prime[j] = false;
}
```

## 3. Linear Sieve
Computes smallest prime factor (`lp`) in $O(N)$.
Allows factorization in $O(\log N)$.

```cpp
int lp[MAXN];
vector<int> primes;
void linearSieve() {
    for (int i = 2; i < MAXN; ++i) {
        if (lp[i] == 0) {
            lp[i] = i;
            primes.push_back(i);
        }
        for (int p : primes) {
            if (p > lp[i] || i * p >= MAXN) break;
            lp[i * p] = p;
        }
    }
}
```

## 4. Number of Divisors
If $N = \prod p_i^{a_i}$, then count of divisors is $\prod (a_i + 1)$.
Usually max divisors for $N \le 10^9$ is small (1344 for 735134400).

## 5. Euler's Totient Function ($\phi$)
$\phi(n)$ = count of numbers $\le n$ coprime to $n$.
$\phi(n) = n \prod (1 - 1/p)$.
Can be computed via Sieve.

## 6. Practice
1.  **SPOJ PRIME1**: Segmented Sieve.
2.  **Codeforces 776B**: Sherlock and his girlfriend.