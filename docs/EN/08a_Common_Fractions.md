# 🧮 Common Fractions

To avoid floating point errors, represent numbers as fractions $\frac{P}{Q}$.

## 1. Structure

Store `num` and `den`. Always simplify by dividing by `gcd(num, den)`. Keep `den > 0`.

```cpp
struct Fraction {
    long long n, d;
    Fraction(long long _n = 0, long long _d = 1) {
        if (_d == 0) _d = 1; // Handle error
        long long g = std::gcd(abs(_n), abs(_d));
        n = _n / g;
        d = _d / g;
        if (d < 0) { n = -n; d = -d; }
    }
    
    Fraction operator+(const Fraction& o) const {
        return Fraction(n * o.d + o.n * d, d * o.d);
    }
    // Implement -, *, / similarly
    
    bool operator<(const Fraction& o) const { return n * o.d < o.n * d; }
    bool operator==(const Fraction& o) const { return n == o.n && d == o.d; }
};
```

## 2. Best Practices
1.  **LCM**: For addition, use LCM of denominators to keep numbers smaller:
    $\frac{a}{b} + \frac{c}{d} = \frac{a \cdot (L/b) + c \cdot (L/d)}{L}$, where $L = \text{lcm}(b, d)$.
2.  **Comparison**: Use cross-multiplication.
3.  **Output**: Print as "N/D".

## 3. Applications
*   Geometry (Intersections).
*   Probability (Exact values).

```