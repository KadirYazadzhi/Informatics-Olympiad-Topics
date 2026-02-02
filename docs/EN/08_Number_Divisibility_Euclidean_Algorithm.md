# ➗ Number Divisibility, Euclidean Algorithm and Applications


## 📚 Number Divisibility

Divisibility is a fundamental concept in number theory. Number `a` is divisible by number `b` (where `b ≠ 0`) if there exists an integer `k` such that `a = b * k`. In this case, we say that `b` is a divisor of `a`, and `a` is a multiple of `b`.

**Example:**
- The number `15` is divisible by `3` because `15 = 3 * 5`.

### Divisibility Checks
Common divisibility rules include:
- **Divisibility by 2:** The number ends in an even digit (0, 2, 4, 6, 8).
- **Divisibility by 3:** The sum of the number's digits is divisible by 3.
- **Divisibility by 5:** The number ends in 0 or 5.

**Example Code:**
```cpp
#include <iostream>
using namespace std;

int main() {
    int num;
    cout << "Enter a number: ";
    cin >> num;

    if (num % 2 == 0) {
        cout << num << " is even." << endl;
    }
    if (num % 3 == 0) {
        cout << num << " is divisible by 3." << endl;
    }
    if (num % 5 == 0) {
        cout << num << " is divisible by 5." << endl;
    }

    return 0;
}
```

## 🔢 Euclidean Algorithm and Applications

### What is the Euclidean Algorithm?
The Euclidean algorithm is an efficient method for finding the greatest common divisor (GCD) of two numbers. It is based on the principle that the GCD of two numbers does not change if the larger number is replaced by the difference between the two numbers.

Formulation:
  - If `b = 0`, then `GCD(a, b) = a`.
  - Otherwise, `GCD(a, b) = GCD(b, a % b)`.

Example
To find `GCD(48, 18)`:
  1. `48 % 18 = 12`
  2. `18 % 12 = 6`
  3. `12 % 6 = 0`, therefore `GCD(48, 18) = 6`.


Example Code
```cpp
#include <iostream>
using namespace std;

int gcd(int a, int b) {
    while (b != 0) {
        int temp = b;
        b = a % b;
        a = temp;
    }
    return a;
}

int main() {
    int a, b;
    cout << "Enter two numbers: ";
    cin >> a >> b;

    cout << "GCD(" << a << ", " << b << ") = " << gcd(a, b) << endl;
    return 0;
}
```

## 🧮 Calculation with Common Fractions

Common fractions represent a part of a whole number, expressed by a numerator (top part) and a denominator (bottom part).

### Basic Operations

- **Addition of fractions**
```(a / b) + (c / d) = (a * d + b * c) / (b * d)```

- **Subtraction of fractions**
```(a / b) - (c / d) = (a * d - b * c) / (b * d)```

- **Multiplication of fractions**
```(a / b) * (c / d) = (a * c) / (b * d)```

- **Division of fractions**
```(a / b) ÷ (c / d) = (a * d) / (b * c)```


Example Code for Addition of Fractions
```cpp
#include <iostream>
using namespace std;

int main() {
    int a, b, c, d;
    cout << "Enter numerator and denominator for the first fraction: ";
    cin >> a >> b;
    cout << "Enter numerator and denominator for the second fraction: ";
    cin >> c >> d;

    int numerator = a * d + b * c;
    int denominator = b * d;

    cout << "Result: " << numerator << "/" << denominator << endl;
    return 0;
}
```

## 🧵 Prime Numbers and Sieve of Eratosthenes

### What is a Prime Number?

A prime number is a natural number greater than 1 that has exactly two divisors: 1 and itself.

### Sieve of Eratosthenes

The Sieve of Eratosthenes is an efficient algorithm for finding all prime numbers up to a given number `n`.

#### Algorithm:
1. Create a boolean array from 2 to `n`, where all elements are marked as "true" (initially all numbers are prime).
2. Start from `p = 2` and remove all its multiples.
3. Move to the next number that hasn't been removed.
4. Repeat the process up to `n`.


Example Code:
```cpp
#include <iostream>
#include <vector>
using namespace std;

void sieveOfEratosthenes(int n) {
    vector<bool> isPrime(n + 1, true);
    isPrime[0] = isPrime[1] = false;

    for (int p = 2; p * p <= n; p++) {
        if (isPrime[p]) {
            for (int i = p * p; i <= n; i += p) {
                isPrime[i] = false;
            }
        }
    }

    cout << "Prime numbers up to " << n << " are: ";
    for (int i = 2; i <= n; i++) {
        if (isPrime[i]) {
            cout << i << " ";
        }
    }
    cout << endl;
}

int main() {
    int n;
    cout << "Enter a number: ";
    cin >> n;

    sieveOfEratosthenes(n);
    return 0;
}
```

## 🎯 Conclusion

The topics of divisibility, Euclidean algorithm, common fractions, and prime numbers are foundational for understanding number theory. They find application in numerous fields of mathematics and programming, including cryptography, data analysis, and algorithmic optimization.
# ➗ Divisibility, GCD, and the Euclidean Algorithm

Number Theory is a cornerstone of algorithm design. Understanding how integers divide and interact is crucial for everything from basic loops to advanced cryptography (RSA).

---

## 1. Fundamental Properties

### 1.1. Divisibility Rules
Quickly checking if $N$ is divisible by $K$ without performing the division:
*   **2**: Last digit is even ($0, 2, 4, 6, 8$).
*   **3**: Sum of digits is divisible by 3.
*   **4**: The number formed by the last two digits is divisible by 4.
*   **5**: Ends in 0 or 5.
*   **6**: Divisible by both 2 and 3.
*   **8**: Last three digits form a number divisible by 8.
*   **9**: Sum of digits is divisible by 9.
*   **10**: Ends in 0.
*   **11**: Alternating sum of digits is divisible by 11. (e.g., $132 \to 1-3+2 = 0$).

---

## 2. Greatest Common Divisor (GCD)

The GCD of $a$ and $b$ is the largest positive integer that divides both $a$ and $b$ without a remainder.

### 2.1. The Euclidean Algorithm
Based on the property: $\gcd(a, b) = \gcd(b, a \pmod b)$.
It has a logarithmic time complexity $O(\log(\min(a, b)))$, making it extremely efficient.

```cpp
// Recursive
long long gcd(long long a, long long b) {
    return b == 0 ? a : gcd(b, a % b);
}

// Iterative
long long gcd_iter(long long a, long long b) {
    while (b) {
        a %= b;
        swap(a, b);
    }
    return a;
}
```

### 2.2. Properties of GCD
*   $\\gcd(a, b) = \gcd(a, b-a)$.
*   $\\gcd(ka, kb) = k \cdot \gcd(a, b)$.
*   $\\gcd(a, b, c) = \gcd(a, \gcd(b, c))$.

---

## 3. Least Common Multiple (LCM)

The smallest positive integer that is divisible by both $a$ and $b$.
**Formula**:
$$\text{lcm}(a, b) = \frac{|a \cdot b|}{\gcd(a, b)}$$

*Important*: In code, always divide first to prevent `long long` overflow:
`lcm = (a / gcd(a, b)) * b;`

---

## 4. Extended Euclidean Algorithm

Given $a$ and $b$, it finds integers $x$ and $y$ such that:
$$ax + by = \gcd(a, b)$$
These $x$ and $y$ are known as Bézout coefficients.

**Application**: Finding the **Modular Multiplicative Inverse**.
If $\\gcd(a, m) = 1$, then $ax \equiv 1 \pmod m$. Solving $ax + my = 1$ gives $x$ as the inverse of $a$ modulo $m$.

```cpp
long long extended_gcd(long long a, long long b, long long &x, long long &y) {
    if (b == 0) {
        x = 1; y = 0;
        return a;
    }
    long long x1, y1;
    long long d = extended_gcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - y1 * (a / b);
    return d;
}
```

---

## 5. Practice Problems
1.  **CSES Common Divisors**: Finding max GCD of any pair in an array.
2.  **Codeforces 1152C**: Minimizing LCM.
3.  **UVa 10104**: Bézout's Identity implementation.
4.  **SPOJ GCDEX**: Sum of GCDs (requires more advanced techniques).

## 6. Conclusion
The Euclidean algorithm is often a component of more complex problems. Always use `long long` for number theory tasks, as intermediate products frequently exceed $2 \cdot 10^9$.

```
