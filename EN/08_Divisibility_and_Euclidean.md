# ➗ Number Divisibility and Euclidean Algorithm

Number theory is the foundation of modern cryptography and algorithmic optimization.

## 📖 Basic Concepts
- **Divisibility**: Number $a$ is divisible by $b$ ($b|a$) if the remainder $a 
mod b = 0$.
- **Prime Numbers**: Numbers with exactly two divisors (1 and itself).
- **Coprime**: Numbers whose greatest common divisor is 1.

## 🔢 Euclidean Algorithm (GCD)
An efficient way to find the greatest common divisor of two numbers.

### Iterative Implementation:
```cpp
int gcd(int a, int b) {
    while (b != 0) {
        a %= b;
        swap(a, b);
    }
    return a;
}
```
**Complexity**: $O(\log(\min(a, b)))$. Extremely fast even for 100-digit numbers.

## 📏 Least Common Multiple (LCM)
Calculated using the formula:
$$\text{lcm}(a, b) = \frac{|a \cdot b|}{\text{gcd}(a, b)}$$
*Warning: Divide first, then multiply to avoid overflow.*

## 🛡️ Remainder Properties
- $(a + b) \pmod m = (a \pmod m + b \pmod m) \pmod m$
- $(a \cdot b) \pmod m = (a \pmod m \cdot b \pmod m) \pmod m$
This allows working with huge results by constantly taking the modulus.

---

## 🏁 Conclusion
Understanding GCD and divisibility properties is critical for tasks involving cycles, periodicity, and fractional calculations.
