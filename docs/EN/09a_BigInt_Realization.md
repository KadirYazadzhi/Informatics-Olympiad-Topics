# 🧮 BigInt Implementation (Long Numbers)

In C++, the largest integer type `unsigned long long` supports numbers up to $\approx 1.8 \times 10^{19}$. However, many tasks require working with numbers with hundreds or thousands of digits. Unlike Python and Java, C++ has no built-in support for BigInteger, so we have to write it ourselves.

## 1. Storage Strategy

The most efficient way is to store the number in `std::vector<int>`, but not digit by digit (base 10); instead, grouping them, for example, by 9 digits (base $10^9$). 
*   Base $10^9$ fits into `int`.
*   The multiplication of two "digits" (up to $10^{18}$) fits into `long long`.
*   It reduces the number of operations 9 times compared to base 10.

The vector stores the digits in **reverse order** (the least significant ones are at index 0) so that it can be easily expanded.

## 2. Full Implementation (Template) 

Here is a compact but functional structure that you can use in competitions:

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

    // Constructors
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
            if (i < 9) digits.push_back(stoi(s.substr(0, i)));
            else digits.push_back(stoi(s.substr(i - 9, 9)));
        }
        trim();
    }

    void trim() {
        while (digits.size() > 1 && digits.back() == 0) 
            digits.pop_back();
    }

    // Comparison operators
    bool operator<(const BigInt &other) const {
        if (digits.size() != other.digits.size()) 
            return digits.size() < other.digits.size();
        for (int i = digits.size() - 1; i >= 0; i--)
            if (digits[i] != other.digits[i]) 
                return digits[i] < other.digits[i];
        return false;
    }
    
    bool operator>(const BigInt &other) const { return other < *this; }
    bool operator==(const BigInt &other) const { return !(*this < other) && !(other < *this); }

    // Addition
    BigInt operator+(const BigInt &other) const {
        BigInt res = *this;
        int carry = 0;
        for (size_t i = 0; i < max(res.digits.size(), other.digits.size()) || carry; ++i) {
            if (i == res.digits.size()) res.digits.push_back(0);
            long long current = res.digits[i] + carry + (i < other.digits.size() ? other.digits[i] : 0);
            res.digits[i] = current % BASE;
            carry = current / BASE;
        }
        return res;
    }

    // Multiplication with a short number (int)
    BigInt operator*(int v) const {
        if (v == 0) return BigInt(0);
        BigInt res = *this;
        long long carry = 0;
        for (size_t i = 0; i < res.digits.size() || carry; ++i) {
            if (i == res.digits.size()) res.digits.push_back(0);
            long long cur = carry + (long long)res.digits[i] * v;
            res.digits[i] = cur % BASE;
            carry = cur / BASE;
        }
        res.trim();
        return res;
    }
    
    // Multiplication with BigInt (School method O(N*M))
    BigInt operator*(const BigInt &other) const {
        BigInt res;
        res.digits.resize(digits.size() + other.digits.size(), 0);
        for (size_t i = 0; i < digits.size(); ++i) {
            long long carry = 0;
            for (size_t j = 0; j < other.digits.size() || carry; ++j) {
                long long cur = res.digits[i+j] + (long long)digits[i] * (j < other.digits.size() ? other.digits[j] : 0) + carry;
                res.digits[i+j] = cur % BASE;
                carry = cur / BASE;
            }
        }
        res.trim();
        return res;
    }

    // Output
    friend ostream& operator<<(ostream &os, const BigInt &bi) {
        if (bi.digits.empty()) { os << 0; return os; }
        os << bi.digits.back();
        for (int i = bi.digits.size() - 2; i >= 0; i--) {
            os << setfill('0') << setw(9) << bi.digits[i];
        }
        return os;
    }
};
```

## 3. Python or Java?

If the task allows the use of other languages and the time limit is not too strict (Python is slower), it is often easier to simply switch the language.
In Python, `int` automatically supports arbitrary precision.

**Python example:**
```python
a = int(input())
b = int(input())
print(a * b)
```

## 4. Optimizations

1.  **Karatsuba**: For very large numbers (e.g., over 2000-3000 digits), the standard $O(N^2)$ multiplication is slow. The Karatsuba algorithm works in $O(N^{\log_2 3}) \approx O(N^{1.58})$.
2.  **FFT (Fast Fourier Transform)**: For extremely large numbers ($10^5$ digits), multiplication is done in $O(N \log N)$ (see Topic 44).

## 5. Tasks
1.  **UVa 10106 - Product**: Simple multiplication of two large numbers.
2.  **SPOJ JULKA**: Addition and subtraction / division by 2.
3.  **Codeforces 1042D**: BigInt may be needed for intermediate calculations.

---

## 6. Division of Long Numbers

Division is the most complex operation for BigInt. There are two main approaches:

### 6.1. Binary Search
If we divide $A$ by $B$ and want the quotient $Q$, we can use binary search on the answer:
*   Search for the largest $Q$ for which $Q \times B \le A$.
*   The remainder is $R = A - Q \times B$.

Complexity: $O((\log A) \cdot M)$, where $M$ is the complexity of multiplication.

### 6.2. "School Division" Method
Similar to the long division we learn at school, we can divide "digit" by "digit" (in our base $10^9$):
```cpp
BigInt divide(const BigInt& dividend, int divisor, int& remainder) {
    BigInt result;
    long long carry = 0;
    
    for (int i = dividend.digits.size() - 1; i >= 0; i--) {
        long long current = carry * BASE + dividend.digits[i];
        result.digits.push_back(current / divisor);
        carry = current % divisor;
    }
    
    remainder = carry;
    reverse(result.digits.begin(), result.digits.end());
    result.trim();
    return result;
}
```

---

## 7. Factorial of Large Numbers

Classical task: Calculate $N!$ for $N \le 1000$.

```cpp
BigInt factorial(int n) {
    BigInt result(1);
    for (int i = 2; i <= n; i++) {
        result = result * i;
    }
    return result;
}
```

For example, $100!$ has 158 digits and is beyond the range of all built-in types.

---

## 8. GCD of Long Numbers

The Euclidean algorithm also works for BigInt, but if one number is small (`int`), there is a faster method:
```cpp
int gcd_bigint_int(BigInt a, int b) {
    // Euclid with mod operation
    while (b != 0) {
        int remainder;
        a = divide(a, b, remainder);
        swap(a, BigInt(b));
        b = remainder;
    }
    return a.digits[0]; // The result will be a small number
}
```

---

## 9. Exponentiation of BigInt

For $A^B \pmod{M}$, if $A$ is BigInt, while $B$ and $M$ are ordinary numbers:
```cpp
BigInt powerMod(BigInt base, int exp, int mod) {
    BigInt result(1);
    while (exp > 0) {
        if (exp % 2 == 1) {
            result = (result * base); // Then take modulo
        }
        base = (base * base); // Then take modulo
        exp /= 2;
    }
    return result;
}
```
Note: For huge moduli, `mod` may need to be BigInt.

---

## 10. Alternatives and Optimizations

### 10.1. Using Python
If the competition allows it:
```python
a = int(input())
b = int(input())
print(a ** b)  # Python automatically works with arbitrarily large numbers
```

### 10.2. Libraries in C++
*   **GMP (GNU Multiple Precision Library)**: An extremely fast and optimized library for arbitrarily large numbers.
*   However, most competitions do NOT allow external libraries.

---

## 🏁 Conclusion

Implementation of BigInt is an important skill for competitors. It is good to have a prepared template to copy when needed. Key elements are:
*   Choosing an appropriate base ($10^9$ is optimal).
*   Correct management of `carry` during arithmetic operations.
*   `trim()` for removing leading zeros.
*   Testing with extreme cases (0, very large numbers, equal numbers).

Although it seems complex, once you have written it, you can use it repeatedly!

```