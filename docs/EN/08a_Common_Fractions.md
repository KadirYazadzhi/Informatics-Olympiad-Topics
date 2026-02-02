# 🧮 Common Fractions

In many tasks (especially geometric or probabilistic ones), absolute precision is required. Floating-point numbers (`double`, `float`) have limited precision and accumulate errors.
The solution is to use **Common Fractions** (numerator and denominator).

## 1. `Fraction` Structure

A fraction is represented as a pair of integers $\frac{a}{b}$.
Mandatory condition: $b 
eq 0$.
It is a good habit to always maintain the fraction in **irreducible form** and to keep the denominator positive.

```cpp
#include <iostream>
#include <numeric> // for std::gcd (C++17) or write your own

using namespace std;

// Helper function for GCD
long long gcd(long long a, long long b) {
    return b == 0 ? a : gcd(b, a % b);
}

struct Fraction {
    long long num, den; // Numerator, Denominator

    Fraction(long long n = 0, long long d = 1) {
        if (d == 0) {
            cerr << "Error: Division by zero!" << endl;
            d = 1; 
        }
        long long common = gcd(abs(n), abs(d));
        num = n / common;
        den = d / common;
        if (den < 0) { // The sign is always in the numerator
            num = -num;
            den = -den;
        }
    }

    // Addition: a/b + c/d = (ad + bc) / bd
    Fraction operator+(const Fraction& other) const {
        return Fraction(num * other.den + other.num * den, den * other.den);
    }

    // Subtraction
    Fraction operator-(const Fraction& other) const {
        return Fraction(num * other.den - other.num * den, den * other.den);
    }

    // Multiplication
    Fraction operator*(const Fraction& other) const {
        return Fraction(num * other.num, den * other.den);
    }

    // Division
    Fraction operator/(const Fraction& other) const {
        return Fraction(num * other.den, den * other.num);
    }

    // Comparison (Cross-multiplication to avoid division)
    bool operator<(const Fraction& other) const {
        return num * other.den < other.num * den;
    }
    bool operator==(const Fraction& other) const {
        return num == other.num && den == other.den; // Because they are irreducible
    }
    bool operator<=(const Fraction& other) const {
        return *this < other || *this == other;
    }
    bool operator>(const Fraction& other) const {
        return !(*this <= other);
    }
    bool operator>=(const Fraction& other) const {
        return !(*this < other);
    }
    bool operator!=(const Fraction& other) const {
        return !(*this == other);
    }
};

// Output
ostream& operator<<(ostream& os, const Fraction& f) {
    if (f.den == 1) os << f.num;
    else os << f.num << "/" << f.den;
    return os;
}

// Input
istream& operator>>(istream& is, Fraction& f) {
    long long n, d = 1;
    char slash;
    is >> n;
    if (is.peek() == '/') {
        is >> slash >> d;
    }
    f = Fraction(n, d);
    return is;
}
```

## 2. Optimizations

The above implementation uses `num * other.den`, which can overflow `long long` if the numbers are large ($> 10^9$). 
*   Instead of `Fraction(num * other.den + ...)`, it is better to use the LCM of the denominators:
    $	ext{LCM} = (den 
cdot other.den) / 
cdot gcd(den, other.den)$.
    This keeps the numbers smaller before reduction.

### Improved version with overflow protection:

```cpp
// LCM function
long long lcm(long long a, long long b) {
    return a / gcd(a, b) * b;
}

// Improved addition
Fraction addSafe(const Fraction& a, const Fraction& b) {
    long long l = lcm(a.den, b.den);
    long long num1 = a.num * (l / a.den);
    long long num2 = b.num * (l / b.den);
    return Fraction(num1 + num2, l);
}
```

### Working with very large numbers:

If the numbers are truly huge, you can use `__int128` or implement a big integer class:

```cpp
typedef __int128 lll;

gcd128(lll a, lll b) {
    return b == 0 ? a : gcd128(b, a % b);
}

struct BigFraction {
    lll num, den;
    
    BigFraction(lll n = 0, lll d = 1) {
        if (d == 0) d = 1;
        lll g = gcd128(abs(n), abs(d));
        num = n / g;
        den = d / g;
        if (den < 0) {
            num = -num;
            den = -den;
        }
    }
    
    BigFraction operator+(const BigFraction& other) const {
        return BigFraction(num * other.den + other.num * den, den * other.den);
    }
    
    // Printing for __int128
    void print() {
        if (num < 0) {
            cout << "-";
            print128(-num);
        } else {
            print128(num);
        }
        if (den != 1) {
            cout << "/";
            print128(den);
        }
    }
    
private:
    void print128(lll x) {
        if (x > 9) print128(x / 10);
        cout << (char)('0' + x % 10);
    }
};
```

## 3. Mixed Numbers

If you need to output the result as a mixed number (e.g., $7/3 = 2 \frac{1}{3}$):
```cpp
void printMixed(Fraction f) {
    long long whole = f.num / f.den;
    long long rem = abs(f.num % f.den); // The remainder is always positive for the fractional part
    if (rem == 0) {
        cout << whole << endl;
    } else {
        if (whole != 0) cout << whole << " ";
        else if (f.num < 0) cout << "-"; // Only a minus sign if there is no whole part
        cout << rem << "/" << f.den << endl;
    }
}
```

## 4. Extended Euclidean Algorithm

GCD is the foundation of fractions, but the Extended Euclidean Algorithm offers more:

### 4.1. Basic Algorithm

```cpp
// Finds GCD and coefficients x, y such that ax + by = gcd(a, b)
long long extgcd(long long a, long long b, long long& x, long long& y) {
    if (b == 0) {
        x = 1;
        y = 0;
        return a;
    }
    long long x1, y1;
    long long d = extgcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - (a / b) * y1;
    return d;
}
```

### 4.2. Modular Inverse

Finding $a^{-1} 
cdot 
mod m$ (necessary for some operations with fractions in modular arithmetic):

```cpp
long long modinv(long long a, long long m) {
    long long x, y;
    long long g = extgcd(a, m, x, y);
    if (g != 1) return -1; // Inverse does not exist
    return (x % m + m) % m;
}
```

### 4.3. Solving a Linear Diophantine Equation

An equation of the form $ax + by = c$:

```cpp
bool solveDiophantine(long long a, long long b, long long c,
                      long long& x, long long& y) {
    long long g = extgcd(a, b, x, y);
    if (c % g != 0) return false; // No solution
    
    x *= c / g;
    y *= c / g;
    return true;
}
```

### 4.4. Chinese Remainder Theorem (CRT)

Solving a system of congruences:
```
x 
cdot 
a1 (mod m1)
x 
cdot 
a2 (mod m2)
```

```cpp
pair<long long, long long> crt(long long a1, long long m1,
                               long long a2, long long m2) {
    long long x, y;
    long long g = extgcd(m1, m2, x, y);
    
    if ((a2 - a1) % g != 0) return {-1, -1}; // No solution
    
    long long lcm_val = m1 / g * m2;
    long long solution = (a1 + x * m1 * ((a2 - a1) / g)) % lcm_val;
    if (solution < 0) solution += lcm_val;
    
    return {solution, lcm_val};
}
```

## 5. Mediant of Fractions

The mediant of $\frac{a}{b}$ and $\frac{c}{d}$ is $\frac{a+c}{b+d}$. It is used in the Stern-Brocot algorithm for finding fractions with a small denominator:

```cpp
// Stern-Brocot tree for finding the fraction closest to target
Fraction sternBrocot(double target, int maxDen) {
    Fraction left(0, 1), right(1, 0); // Boundaries
    
    while (true) {
        // Mediant
        Fraction mid(left.num + right.num, left.den + right.den);
        
        if (mid.den > maxDen) break;
        
        double midVal = (double)mid.num / mid.den;
        if (abs(midVal - target) < 1e-9) return mid;
        
        if (midVal < target) {
            left = mid;
        } else {
            right = mid;
        }
    }
    
    // Returning the closer of left and right
    double dLeft = abs((double)left.num / left.den - target);
    double dRight = abs((double)right.num / right.den - target);
    return dLeft < dRight ? left : right;
}
```

## 6. Practical Tasks

1.  **Codeforces 834D**: Egyptian Fractions - representation as a sum $1/a + 1/b + ...$
2.  **Codeforces 702A**: Maximum increasing subsequence (sometimes fractions help in comparing slopes).
3.  **Geometry**: Finding the intersection point of two lines ($Ax + By + C = 0$). The result is often a fraction.
4.  **Probabilities**: Tasks requiring the answer as an irreducible fraction "P/Q".
5.  **CSES Exponentiation II**: Modular exponentiation with Extended Euclidean.
6.  **Project Euler 71**: Ordered Fractions - Farey sequences.

## 7. Farey Sequences

The Farey sequence $F_n$ contains all irreducible fractions between 0 and 1 with denominator $\leq n$, arranged in ascending order.

Property: If $\frac{a}{b}$ and $\frac{c}{d}$ are adjacent fractions in $F_n$, then $bc - ad = 1$.

```cpp
void generateFarey(int n) {
    Fraction a(0, 1), b(1, n);
    cout << a << " ";
    
    while (b.den != 1 || b.num != 1) {
        cout << b << " ";
        
        int k = (n + a.den) / b.den;
        Fraction next(k * b.num - a.num, k * b.den - a.den);
        a = b;
        b = next;
    }
    cout << b << endl;
}
```

## 🏁 Conclusion

The `Fraction` class is part of the "battle arsenal" of every competitor. Write it once, test it, and save it in your code library (Template). The Extended Euclidean Algorithm reveals numerous applications - from modular inverse to solving Diophantine equations and the Chinese Remainder Theorem.

```