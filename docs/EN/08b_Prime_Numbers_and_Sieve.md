# 🧵 Prime Numbers and Sieve of Eratosthenes

Prime numbers are the "atoms" of numbers. Every natural number $>1$ can be uniquely factored into a product of primes.

## 🔎 Primality Test
The fastest way to check if $N$ is prime is to try dividing by all numbers up to $\sqrt{N}$.
```cpp
bool isPrime(long long n) {
    if (n < 2) return false;
    for (long long i = 2; i * i <= n; i++) {
        if (n % i == 0) return false;
    }
    return true;
}
```
Complexity: $O(\sqrt{N})$.

---

## 🧶 Sieve of Eratosthenes
Used to find **all** prime numbers in the range $[1, N]$.

### Algorithm:
1. Create an `isPrime` array of size $N+1$, filled with `true`.
2. Start from 2. If marked prime, cross out all its multiples ($4, 6, 8 \dots$).
3. Continue up to $\sqrt{N}$.

```cpp
vector<int> primes;
bool is_prime[MAXN];
void sieve(int n) {
    fill(is_prime, is_prime + n + 1, true);
    is_prime[0] = is_prime[1] = false;
    for (int p = 2; p * p <= n; p++) {
        if (is_prime[p]) {
            for (int i = p * p; i <= n; i += p)
                is_prime[i] = false;
        }
    }
}
```
**Complexity**: $O(N \log \log N)$ – practically linear time.

---

## 🔢 Prime Factorization
Any number $N$ can be factored by repeatedly dividing by prime numbers up to $\sqrt{N}$.
```cpp
void factorize(long long n) {
    for (long long i = 2; i * i <= n; i++) {
        while (n % i == 0) {
            cout << i << " ";
            n /= i;
        }
    }
    if (n > 1) cout << n;
}
```

## 🏁 Conclusion
The Sieve is an extremely powerful tool. If a problem requires handling many numbers up to $10^6$ or $10^7$, the sieve is the only way to fit within the time limit.
