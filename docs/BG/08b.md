# 🔢 Прости Числа и Решето на Ератостен

Простите числа са фундаментални обекти в теория на числата и криптографията. Те са "атомите" на аритметиката - всяко естествено число се разлага уникално на прости множители (Основна теорема на аритметиката).

**Определение**: Просто число е естествено число $p > 1$, което има точно два делителя: 1 и самото себе си.

**Примери**: 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47...

---

## 1. Проверка за простота (Primality Test)

### 1.1. Наивен метод: $O(\sqrt{N})$

Проверяваме дали числото има делители от 2 до $\sqrt{N}$.

**Защо до $\sqrt{N}$?** Ако $N = a \cdot b$ и $a \leq b$, то $a \leq \sqrt{N}$. Следователно, ако има делител, поне единият от тях е $\leq \sqrt{N}$.

```cpp
bool isPrime(long long n) {
    if (n < 2) return false;
    if (n == 2) return true;
    if (n % 2 == 0) return false; // Оптимизация за четни числа
    
    for (long long i = 3; i * i <= n; i += 2) {
        if (n % i == 0) return false;
    }
    return true;
}
```

**Оптимизации**:
- Проверяваме само нечетни делители след 2
- Използваме `i * i <= n` вместо `i <= sqrt(n)` за избягване на операцията `sqrt`

### 1.2. Оптимизирана проверка само с прости числа

Ако вече имаме списък с прости числа, можем да проверяваме само с тях:

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

### 1.3. Милър-Рабин (Miller-Rabin): $O(k \log^3 N)$

Вероятностен тест за много големи числа (до $10^{18}$). Базира се на теоремата на Ферма.

**Принцип**: Ако $p$ е просто и $a$ не се дели на $p$, то $a^{p-1} \equiv 1 \pmod{p}$.

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
    
    // Тестваме с фиксирани бази, които работят до 2^64
    vector<long long> bases = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37};
    for (long long a : bases) {
        if (!millerRabin(n, a)) return false;
    }
    return true;
}
```

---

## 2. Решето на Ератостен (Sieve of Eratosthenes)

Намира всички прости числа до $N$ за време $O(N \log \log N)$.

**Идея**: Вземаме първото непресято число (то е просто) и зачеркваме всичките му кратни.

### 2.1. Класическа имплементация

```cpp
const int MAXN = 1000005;
bool is_prime[MAXN];

void sieve() {
    fill(is_prime, is_prime + MAXN, true);
    is_prime[0] = is_prime[1] = false;
    
    for (int i = 2; i * i < MAXN; i++) {
        if (is_prime[i]) {
            // Зачеркваме кратните на i, започвайки от i*i
            for (int j = i * i; j < MAXN; j += i) {
                is_prime[j] = false;
            }
        }
    }
}
```

**Защо започваме от $i \cdot i$?** Всички по-малки кратни ($i \cdot 2, i \cdot 3, \ldots, i \cdot (i-1)$) вече са зачеркнати от по-малките прости числа.

### 2.2. Оптимизирано решето (само нечетни)

```cpp
const int MAXN = 1000005;
bool is_prime[MAXN / 2]; // Пазим само нечетните

void sieveOdd() {
    fill(is_prime, is_prime + MAXN / 2, true);
    is_prime[0] = false; // 1 не е просто
    
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

### 2.3. Сегментирано решето (за много големи $N$)

Когато $N$ е твърде голямо ($> 10^8$), използваме сегментирано решето:

```cpp
vector<int> smallPrimes;

void segmentedSieve(long long L, long long R) {
    // Първо намираме простите до sqrt(R)
    int limit = sqrt(R) + 1;
    vector<bool> mark(limit, true);
    for (int i = 2; i < limit; i++) {
        if (mark[i]) {
            smallPrimes.push_back(i);
            for (int j = i * i; j < limit; j += i)
                mark[j] = false;
        }
    }
    
    // Сега пресяваме [L, R]
    vector<bool> isPrime(R - L + 1, true);
    for (long long p : smallPrimes) {
        long long start = max(p * p, (L + p - 1) / p * p);
        for (long long j = start; j <= R; j += p) {
            isPrime[j - L] = false;
        }
    }
    
    if (L == 1) isPrime[0] = false;
    
    // Принтиране на простите в [L, R]
    for (long long i = L; i <= R; i++) {
        if (isPrime[i - L]) {
            cout << i << " ";
        }
    }
}
```

---

## 3. Линейно Решето (Linear Sieve)

Намира простите числа и най-малкия прост делител (`lp`) за всяко число за **точно** $O(N)$.

**Ключова идея**: Всяко съставно число се зачертава точно веднъж - от своя най-малък прост делител.

```cpp
const int MAXN = 10000005;
int lp[MAXN]; // Least Prime factor
vector<int> primes;

void linearSieve() {
    for (int i = 2; i < MAXN; ++i) {
        if (lp[i] == 0) { // i е просто
            lp[i] = i;
            primes.push_back(i);
        }
        
        // Зачертаваме кратните на i с прости делители
        for (int p : primes) {
            if (p > lp[i] || i * p >= MAXN) break;
            lp[i * p] = p;
        }
    }
}
```

**Защо работи?** Ключът е в условието `p > lp[i]`. Гарантира, че зачертаваме $i \cdot p$ само когато $p$ е най-малкият прост делител на $i \cdot p$.

---

## 4. Разлагане на множители (Factorization)

### 4.1. С линейно решето ($O(\log N)$ на заявка)

```cpp
vector<int> getFactors(int x) {
    vector<int> factors;
    while (x > 1) {
        factors.push_back(lp[x]);
        x /= lp[x];
    }
    return factors;
}

// С брой на срещанията
map<int, int> getPrimeFactorization(int x) {
    map<int, int> factors;
    while (x > 1) {
        factors[lp[x]]++;
        x /= lp[x];
    }
    return factors;
}
```

### 4.2. Пряка факторизация ($O(\sqrt{N})$)

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

---

## 5. Брой и Сума на Делителите

Ако $N = p_1^{a_1} \cdot p_2^{a_2} \cdot \ldots \cdot p_k^{a_k}$:

### Брой делители
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

### Сума на делители
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

### Намиране на всички делители

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

---

## 6. Функцията на Ойлер (Euler's Totient Function)

$\phi(n)$ е броят на числата от 1 до $n$, които са взаимно прости с $n$.

**Формула**: Ако $n = p_1^{a_1} \cdot p_2^{a_2} \cdots p_k^{a_k}$, то:
$$\phi(n) = n \cdot \left(1 - \frac{1}{p_1}\right) \cdot \left(1 - \frac{1}{p_2}\right) \cdots \left(1 - \frac{1}{p_k}\right)$$

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

---

## 7. Практически съвети

1. **Избор на метод**:
   - За единична проверка на големи числа: Miller-Rabin
   - За множество проверки в малък диапазон: Решето на Ератостен
   - За много голям диапазон: Сегментирано решето
   - Когато трябва факторизация: Линейно решето

2. **Оптимизации**:
   - За четни числа веднага връщайте `false` (освен 2)
   - Използвайте битови операции където е възможно
   - Кеширайте резултати за често използвани числа

---

## 8. Задачи за упражнение

1. **SPOJ TDPRIMES**: Printing some primes (Сегментирано решето)
2. **Codeforces 230B**: T-primes (Прости числа и перфектни квадрати)
3. **Project Euler Problem 10**: Summation of primes
4. **CSES Counting Divisors**: Брой делители за много числа
5. **Codeforces 26A**: Almost Prime (Факторизация)
6. **SPOJ PRIME1**: Prime Generator (Сегментирано решето)

---

## 🏁 Заключение

Простите числа са фундаментални в математиката и криптографията. Решетото на Ератостен и линейното решето са must-know алгоритми за всеки състезател. Разбирането на факторизацията и аритметичните функции отваря вратите към сложни задачи по теория на числата.