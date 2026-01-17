# 🔢 Numeral Systems: Theory and Conversions

A numeral system is a way of representing numbers using a finite set of symbols (digits).

## 🏛️ Types of Numeral Systems
1. **Positional**: The value of a digit depends on its position (e.g., decimal, binary).
2. **Non-positional**: The value of a digit is fixed (e.g., Roman numerals).

## 🔄 Converting Between Systems

### 1. From Base $P$ to Decimal
Use the polynomial representation:
$$N = d_k P^k + d_{k-1} P^{k-1} + \dots + d_0 P^0$$
**Example (1011 in binary)**: $1\cdot 2^3 + 0\cdot 2^2 + 1\cdot 2^1 + 1\cdot 2^0 = 8 + 0 + 2 + 1 = 11$.

### 2. From Decimal to Base $P$
Use repeated division by $P$ and record remainders in reverse order.

### 3. Horner's Method
An efficient way to convert strings to numbers:
```cpp
long long toDecimal(string s, int base) {
    long long res = 0;
    for (char c : s) {
        int val = (isdigit(c) ? c - '0' : c - 'A' + 10);
        res = res * base + val;
    }
    return res;
}
```

## 🛠️ Binary System in Computers
- **Bits and Bytes**: 8 bits = 1 byte.
- **Two's Complement**: Method for representing negative numbers in binary.

---

## 🏁 Conclusion
Knowledge of numeral systems is critical for bitwise operations and solving problems requiring non-standard counting.