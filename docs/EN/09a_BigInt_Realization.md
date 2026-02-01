# 🧮 BigInt Implementation

C++ `unsigned long long` caps at $10^{19}$. For cryptography or specific problems, we need larger numbers.

## 1. Storage Strategy
Store digits in a `vector<int>`.
To optimize, store in **base $10^9$** (9 decimal digits per integer). This fits in `int` and multiplication fits in `long long`.
Store in **reverse order** (Little Endian).

## 2. Implementation

```cpp
#include <vector>
#include <string>
#include <algorithm>
#include <iostream>
#include <iomanip>

using namespace std;

const int BASE = 1e9;

struct BigInt {
    vector<int> digits;

    BigInt(long long v = 0) {
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
        while (digits.size() > 1 && digits.back() == 0) digits.pop_back();
    }

    // Addition
    BigInt operator+(const BigInt &o) const {
        BigInt res = *this;
        int carry = 0;
        for (size_t i = 0; i < max(res.digits.size(), o.digits.size()) || carry; ++i) {
            if (i == res.digits.size()) res.digits.push_back(0);
            long long cur = res.digits[i] + carry + (i < o.digits.size() ? o.digits[i] : 0);
            res.digits[i] = cur % BASE;
            carry = cur / BASE;
        }
        return res;
    }

    // Multiplication (Simple O(N*M))
    BigInt operator*(const BigInt &o) const {
        BigInt res;
        res.digits.resize(digits.size() + o.digits.size(), 0);
        for (size_t i = 0; i < digits.size(); ++i) {
            long long carry = 0;
            for (size_t j = 0; j < o.digits.size() || carry; ++j) {
                long long cur = res.digits[i+j] + 
                                digits[i] * 1LL * (j < o.digits.size() ? o.digits[j] : 0) + carry;
                res.digits[i+j] = cur % BASE;
                carry = cur / BASE;
            }
        }
        res.trim();
        return res;
    }
    
    // Comparison
    bool operator<(const BigInt& o) const {
        if (digits.size() != o.digits.size()) return digits.size() < o.digits.size();
        for (int i = digits.size() - 1; i >= 0; i--)
            if (digits[i] != o.digits[i]) return digits[i] < o.digits[i];
        return false;
    }

    friend ostream& operator<<(ostream &os, const BigInt &bi) {
        if (bi.digits.empty()) return os << 0;
        os << bi.digits.back();
        for (int i = bi.digits.size() - 2; i >= 0; i--)
            os << setfill('0') << setw(9) << bi.digits[i];
        return os;
    }
};
```

## 3. Fast Multiplication
For numbers with $10^5$ digits, use **FFT** (Topic 44).
For $10^3$ digits, standard multiplication is fine.

## 4. Python
If possible, use Python. It supports arbitrary precision integers natively.
```python
a = int(input())
b = int(input())
print(a * b)
```