# 🧮 Calculation with Common Fractions

Working with fractions requires precision and avoiding floating-point numbers (`double`) to maintain accuracy.

## 🏗️ Fraction Representation
Always store fractions as a pair of integers: numerator ($num$) and denominator ($den$).

```cpp
struct Fraction {
    long long num, den;
    
    void simplify() {
        long long common = gcd(abs(num), abs(den));
        num /= common;
        den /= common;
        if (den < 0) { num = -num; den = -den; }
    }
};
```

## ➕ Fraction Operations

### 1. Addition and Subtraction
Bring to a common denominator. Best to use the LCM of denominators or just their product.
$$\frac{a}{b} + \frac{c}{d} = \frac{a \cdot d + b \cdot c}{b \cdot d}$$

### 2. Multiplication and Division
Multiply numerators and denominators respectively.
$$\frac{a}{b} \cdot \frac{c}{d} = \frac{a \cdot c}{b \cdot d}, \quad \frac{a}{b} \div \frac{c}{d} = \frac{a \cdot d}{b \cdot c}$$

## ⚠️ Important Rules
1. **Always simplify**: Call `simplify()` after every operation to prevent variable overflow.
2. **Zero denominator**: Always check if the denominator becomes 0.
3. **Comparison**: Instead of `a/b == c/d`, use cross-multiplication: `a * d == b * c`.

---

## 🏁 Conclusion
Using structures for fractions guarantees absolute mathematical precision, vital in geometric and logic problems.
