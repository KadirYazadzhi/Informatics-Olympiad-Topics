# 🧮 Common Fractions: Precision and Implementation

In competitive programming, many problems (especially in geometry, probability, or games) require exact numeric results. Using floating-point types like `double` or `long double` can lead to precision errors (e.g., $0.1 + 0.2 \neq 0.3$) that accumulate over many operations. **Common Fractions** (representing numbers as $\frac{P}{Q}$) provide a way to maintain absolute precision using integer arithmetic.

---

## 1. The `Fraction` Structure

A fraction consists of two parts: a **numerator** (upper part) and a **denominator** (lower part).

### 1.1. Core Implementation
To keep the representation unique and prevent overflow, we must:
1.  Keep the fraction in **reduced form** (divide both by GCD).
2.  Ensure the **denominator is always positive** (move sign to numerator).

```cpp
#include <iostream>
#include <numeric> // for std::gcd since C++17

using namespace std;

struct Fraction {
    long long n, d; // Numerator, Denominator

    Fraction(long long _n = 0, long long _d = 1) {
        if (_d == 0) {
            // Handle division by zero based on problem requirements
            _d = 1; 
        }
        if (_d < 0) { _n = -_n; _d = -_d; }
        long long common = std::gcd(abs(_n), abs(_d));
        n = _n / common;
        d = _d / common;
    }

    // Arithmetic Operators
    Fraction operator+(const Fraction& o) const {
        // a/b + c/d = (ad + bc) / bd
        // Optimization: use LCM to keep intermediate numbers smaller
        long long common_den = (d / std::gcd(d, o.d)) * o.d;
        return Fraction(n * (common_den / d) + o.n * (common_den / o.d), common_den);
    }

    Fraction operator-(const Fraction& o) const {
        long long common_den = (d / std::gcd(d, o.d)) * o.d;
        return Fraction(n * (common_den / d) - o.n * (common_den / o.d), common_den);
    }

    Fraction operator*(const Fraction& o) const {
        return Fraction(n * o.n, d * o.d);
    }

    Fraction operator/(const Fraction& o) const {
        return Fraction(n * o.d, d * o.n);
    }

    // Comparison Operators (Avoid division!)
    bool operator<(const Fraction& o) const {
        // a/b < c/d <=> a*d < c*b
        return (__int128)n * o.d < (__int128)o.n * d; 
    }

    bool operator==(const Fraction& o) const {
        return n == o.n && d == o.d; // Works because they are always reduced
    }

    double to_double() const { return (double)n / d; }
};
```
*Note: Using `__int128` for comparisons prevents overflow if $n, d$ are near $10^{18}$.*

---

## 2. Advanced Usage and Tips

### 2.1. Handling Mixed Numbers
To convert $\frac{7}{3}$ to $2 \frac{1}{3}$:
```cpp
void printMixed(Fraction f) {
    long long whole = f.n / f.d;
    long long rem = abs(f.n % f.d);
    if (whole != 0) cout << whole << " ";
    if (rem != 0) cout << rem << "/" << f.d;
    if (whole == 0 && rem == 0) cout << 0;
}
```

### 2.2. Precision Comparison with Epsilon
If you eventually MUST convert to `double`, always use an epsilon (`EPS`) for equality checks:
```cpp
const double EPS = 1e-9;
if (abs(f1.to_double() - f2.to_double()) < EPS) { ... }
```

### 2.3. Fractions in Geometry
When finding the intersection of two lines $A_1x + B_1y + C_1 = 0$ and $A_2x + B_2y + C_2 = 0$:
$$x = \frac{B_1C_2 - B_2C_1}{A_1B_2 - A_2B_1}, \quad y = \frac{C_1A_2 - C_2A_1}{A_1B_2 - A_2B_1}$$ 
These coordinates are naturally fractions. Using the `Fraction` struct here prevents errors when lines are nearly parallel.

---

## 3. Practice Problems
1.  **UVa 10806**: Dijkstra with fractions (if weights are fractions).
2.  **Codeforces 702A**: Maximum Increase (can be used for slope comparisons).
3.  **SPOJ MPROPROB**: Probability tasks requiring exact "P/Q" output.

## 4. Conclusion
The `Fraction` struct is a "black box" utility. Implement it once, test it thoroughly, and keep it in your template library. It is often the difference between a `Wrong Answer` and `Accepted` in geometry problems.
