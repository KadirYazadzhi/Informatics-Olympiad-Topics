# 📈 Polynomials and Full Implementation of Big Integers (BigInt)

## 🧮 Big Integers (BigInt) - Detailed Implementation

Standard data types (`long long`) are limited to $\approx 9 \cdot 10^{18}$. In problems with larger numbers (e.g., 100-digit), we need to implement our own structure.

### Choice of Base
Instead of storing one digit (base 10), it is much more efficient to store 9 digits (base $10^9$) in one `int`. This reduces memory and the number of operations by 9 times.
- Base $10^9$ fits in `int`, and the product of two "digits" ($10^{18}$) fits in `long long`.

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

    // Subtraction (assume *this >= other)
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

    // Multiplication by short number (long long)
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

Polynomials are a generalization of numbers. If a number is a polynomial evaluated at point $x=10$ (for decimal system), then operations are analogous.

### Horner's Method - Optimization
Horner's method reduces the number of multiplications when evaluating $P(x)$.
Instead of $a_n x^n + \dots + a_0$, we write:
$$(\dots((a_n x + a_{n-1})x + a_{n-2})x + \dots)x + a_0$$
Thus, only $N$ multiplications and $N$ additions are needed.

### Polynomial Multiplication
1. **Naive Method**: $O(N^2)$ - identical to BigInt multiplication above.
2. **Fast Multiplication (FFT)**: $O(N \log N)$. Converts polynomials to values (Point-Value form), multiplies them linearly, and converts them back. (See Topic 44).

### Lagrange Interpolation
If we have $N+1$ points $(x_i, y_i)$, there exists a unique polynomial of degree $\le N$ passing through them.
$$P(x) = \sum_{j=0}^{N} y_j L_j(x), \quad \text{where } L_j(x) = \prod_{i \neq j} \frac{x - x_i}{x_j - x_i}$$

This is useful for reconstructing a function from its values, especially in problems finding the "next term in a sequence".

---

## 🏁 Conclusion

Implementing `BigInt` is a common subtask. It is good to have a prepared and tested template ("black box") to copy during a contest. Be careful with the "trim" function for removing leading zeros, as they can break comparison or output logic.

```
