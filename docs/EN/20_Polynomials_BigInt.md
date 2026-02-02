# 📈 Polynomials and Full BigInt Implementation

## 🧮 BigInt - Detailed Implementation

Standard data types (`long long`) are limited to $\approx 9 \cdot 10^{18}$. In tasks with larger numbers (e.g., 100-digit ones), we must implement our own structure.

### Choosing a Base
Instead of keeping one digit at a time (base 10), it is much more efficient to keep 9 digits (base $10^9$) in a single `int`. This reduces memory and the number of operations 9 times.
- Base $10^9$ fits into `int`, and the product of two "digits" ($10^{18}$) fits into `long long`.

### Full BigInt Structure

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <iomanip>

using namespace std;

const int BASE = 1e9;

struct BigInt {
    vector<int> digits;

    BigInt() {}
    BigInt(long long v) {
        if (v == 0) digits.push_back(0);
        while (v > 0) {
            digits.push_back(v % BASE);
            v /= BASE;
        }
    }
    BigInt(string s) {
        for (int i = (int)s.length(); i > 0; i -= 9) {
            if (i < 9)
                digits.push_back(stoi(s.substr(0, i)));
            else
                digits.push_back(stoi(s.substr(i - 9, 9)));
        }
        trim();
    }

    void trim() {
        while (digits.size() > 1 && digits.back() == 0) {
            digits.pop_back();
        }
    }

    // Output operator
    friend ostream& operator<<(ostream& os, const BigInt& bi) {
        if (bi.digits.empty()) { os << 0; return os; }
        os << bi.digits.back();
        for (int i = (int)bi.digits.size() - 2; i >= 0; i--) {
            os << setfill('0') << setw(9) << bi.digits[i];
        }
        return os;
    }

    // Comparison
    bool operator<(const BigInt& other) const {
        if (digits.size() != other.digits.size())
            return digits.size() < other.digits.size();
        for (int i = (int)digits.size() - 1; i >= 0; i--)
            if (digits[i] != other.digits[i])
                return digits[i] < other.digits[i];
        return false;
    }

    // Addition
    BigInt operator+(const BigInt& other) const {
        BigInt res;
        int carry = 0;
        for (size_t i = 0; i < max(digits.size(), other.digits.size()) || carry; ++i) {
            int current = carry + (i < digits.size() ? digits[i] : 0) + 
                          (i < other.digits.size() ? other.digits[i] : 0);
            res.digits.push_back(current % BASE);
            carry = current / BASE;
        }
        return res;
    }

    // Subtraction (assuming *this >= other)
    BigInt operator-(const BigInt& other) const {
        BigInt res;
        int carry = 0;
        for (size_t i = 0; i < digits.size(); ++i) {
            int current = digits[i] - carry - (i < other.digits.size() ? other.digits[i] : 0);
            if (current < 0) {
                current += BASE;
                carry = 1;
            } else {
                carry = 0;
            }
            res.digits.push_back(current);
        }
        res.trim();
        return res;
    }

    // Multiplication with a short number (long long)
    BigInt operator*(long long v) const {
        BigInt res;
        long long carry = 0;
        for (int d : digits) {
            long long cur = d * v + carry;
            res.digits.push_back(cur % BASE);
            carry = cur / BASE;
        }
        while (carry) {
            res.digits.push_back(carry % BASE);
            carry /= BASE;
        }
        res.trim();
        return res;
    }

    // Multiplication of two BigInt numbers
    BigInt operator*(const BigInt& other) const {
        BigInt res;
        res.digits.resize(digits.size() + other.digits.size(), 0);
        for (size_t i = 0; i < digits.size(); ++i) {
            long long carry = 0;
            for (size_t j = 0; j < other.digits.size() || carry; ++j) {
                long long cur = res.digits[i + j] + digits[i] * 1LL * (j < other.digits.size() ? other.digits[j] : 0) + carry;
                res.digits[i + j] = cur % BASE;
                carry = cur / BASE;
            }
        }
        res.trim();
        return res;
    }
};
```

---

## 📈 Polynomials

Polynomials are a generalization of numbers. If a number is a polynomial evaluated at point $x=10$ (for the decimal system), then the operations are analogous.

### Horner's Method - Optimization
Horner's method reduces the number of multiplications when calculating $P(x)$.
Instead of $a_n x^n + \dots + a_0$, we write:
$$\dots((a_n x + a_{n-1})x + a_{n-2})x + \dots)x + a_0$$
Thus only $N$ multiplications and $N$ additions are needed.

### Polynomial Multiplication
1. **Naive method**: $O(N^2)$ - identical to the BigInt multiplication above.
2. **Fast multiplication (FFT)**: $O(N \log N)$. Transforms polynomials into values (Point-Value form), multiplies them linearly, and converts them back. (See Topic 44).

### Lagrange Interpolation
If we have $N+1$ points $(x_i, y_i)$, there exists a unique polynomial of degree $\le N$ that passes through them.
$$P(x) = \sum_{j=0}^{N} y_j L_j(x), \quad \text{where } L_j(x) = \prod_{i \neq j} \frac{x - x_i}{x_j - x_i}$$

This is useful for reconstructing a function from its values, especially in tasks for finding the "next member of a sequence".

---

## 🏁 Conclusion

The implementation of `BigInt` is a common subtask. It is good to have a prepared and tested template ("black box") to copy during a competition. Be careful with the "trim" function for removing leading zeros, as they can break the logic of comparison or output.

---

## 11. Working with Polynomials - Practical Examples

### 11.1. Evaluating a Polynomial with Horner's Method
```cpp
// P(x) = a[0] + a[1]*x + a[2]*x^2 + ... + a[n]*x^n
double horner(vector<double>& a, double x) {
    double result = 0;
    for (int i = a.size() - 1; i >= 0; i--) {
        result = result * x + a[i];
    }
    return result;
}
```

### 11.2. Polynomial Addition
```cpp
vector<int> addPolynomials(vector<int>& p1, vector<int>& p2) {
    int maxDegree = max(p1.size(), p2.size());
    vector<int> result(maxDegree, 0);
    
    for (int i = 0; i < p1.size(); i++) result[i] += p1[i];
    for (int i = 0; i < p2.size(); i++) result[i] += p2[i];
    
    return result;
}
```

### 11.3. Polynomial Multiplication (Naive Method)
```cpp
vector<int> multiplyPolynomials(vector<int>& p1, vector<int>& p2) {
    vector<int> result(p1.size() + p2.size() - 1, 0);
    
    for (int i = 0; i < p1.size(); i++) {
        for (int j = 0; j < p2.size(); j++) {
            result[i + j] += p1[i] * p2[j];
        }
    }
    
    return result;
}
```
Complexity: $O(N \times M)$, where $N$ and $M$ are the degrees of the polynomials.

---

## 12. Interpolation - Practical Application

### Task: Finding the Next Member in a Sequence
The first $N+1$ members of a sequence are given. Find the $(N+2)$-th member.

**Solution**: If the sequence is described by a polynomial of degree $\le N$, we can use Lagrange interpolation.

```cpp
// Given points (0, y0), (1, y1), ..., (n, yn)
// Find the value of the polynomial at point x
double lagrangeInterpolation(vector<double>& y, double x) {
    int n = y.size();
    double result = 0;
    
    for (int j = 0; j < n; j++) {
        double term = y[j];
        for (int i = 0; i < n; i++) {
            if (i != j) {
                term *= (x - i) / (j - i);
            }
        }
        result += term;
    }
    
    return result;
}
```

Example: If we have $y = [1, 4, 9, 16, 25]$ (the squares of numbers), this is a polynomial of degree 2. We can find the next member for $x=5$: $P(5) = 36$.

---

## 13. Connection with Dynamic Programming

Polynomials often appear in DP tasks, especially in:
*   **Generating functions**.
*   **Combinatorics** (binomial coefficients, Catalan numbers).

For example, the number of ways to change coins can be represented as coefficients in a product of polynomials.

---

## 14. Practice Tasks

1. **Project Euler 101**: Optimum Polynomial (interpolation).
2. **Codeforces 528D**: Fuzzy Search (can be solved with FFT polynomial multiplication).
3. **SPOJ POLYMUL**: Direct polynomial multiplication.
4. **UVa 10105**: Polynomial Coefficients (combinatorics with polynomials).

---

## 🏁 Final Tips

*   For BigInt tasks, always test with extreme cases (0, very large numbers, equal numbers).
*   With polynomials, be careful with overflow - use `long long` or modular arithmetic.
*   Horner's method is elegant and efficient - use it whenever you evaluate a polynomial.
*   For competitions with allowed external libraries (GMP, NTL), learn how to use them - they save time.

Mastering work with arbitrarily large numbers and polynomials opens the door to many advanced algorithms such as FFT, NTT, and various cryptographic applications!

```