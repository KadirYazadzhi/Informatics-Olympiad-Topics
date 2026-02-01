# 🔢 Numeral Systems: Theory and Fast Conversions

Numeral systems are the foundation of how computers and humans represent quantitative data. While humans primarily use the **Decimal (Base 10)** system, computers operate in **Binary (Base 2)**, and programmers frequently use **Hexadecimal (Base 16)** or **Octal (Base 8)** for better readability of binary data.

---

## 1. Positional Notation

Any number $N$ in base $B$ can be written as:
$$N = d_k B^k + d_{k-1} B^{k-1} + \dots + d_1 B^1 + d_0 B^0$$
where each digit $d_i \in [0, B-1]$.

---

## 2. Base Conversion Algorithms

### 2.1. From Decimal to Base $B$
The "Repeated Division" algorithm:
1.  Divide the number $N$ by $B$.
2.  The remainder is the next digit (from least to most significant).
3.  Replace $N$ with the quotient.
4.  Repeat until $N = 0$.

```cpp
string fromDecimal(long long n, int b) {
    if (n == 0) return "0";
    string res = "";
    string digits = "0123456789ABCDEF";
    while (n > 0) {
        res += digits[n % b];
        n /= b;
    }
    reverse(res.begin(), res.end());
    return res;
}
```

### 2.2. From Base $B$ to Decimal
The "Horner's Method" (Polynomial Evaluation):
Start with `sum = 0`. For each digit $d$ from most significant to least: `sum = sum * B + d`.

```cpp
long long toDecimal(string s, int b) {
    long long res = 0;
    for (char c : s) {
        int d = (isdigit(c) ? c - '0' : c - 'A' + 10);
        res = res * b + d;
    }
    return res;
}
```

---

## 3. Fast Conversions (Bases of Power of 2)

Conversion between bases $2^k$ and $2^m$ can be done without converting to decimal first, by grouping bits.
*   **Binary to Hex (Base 16)**: Group every 4 bits into one hex digit.
    *   `1101 0101 (2)` -> `D 5 (16)`
*   **Binary to Octal (Base 8)**: Group every 3 bits.
    *   `110 101 (2)` -> `6 5 (8)`

---

## 4. Bitwise Representation and Base 2

Understanding the binary system is crucial for **Bit Manipulation** (Topic 22).
*   **Left Shift (`<<`)**: $x \ll k$ is equivalent to $x \cdot 2^k$.
*   **Right Shift (`>>`)**: $x \gg k$ is equivalent to $x / 2^k$.

### Negative Numbers (Two's Complement)
Most computers represent negative numbers using two's complement:
`~x + 1 = -x`.

---

## 5. C++ Built-in Tools

C++ provides tools for common base operations:
*   **Stream Manipulators**: `cout << hex << 255;` prints `ff`.
*   **String Parsing**: `stoll(s, nullptr, 16);` converts hex string to long long.
*   **`std::bitset`**: For binary visualization.

```cpp
#include <bitset>
cout << bitset<8>(10); // Prints 00001010
```

---

## 6. Base 64 Encoding
Used for representing binary data as text (ASCII). It uses 64 characters: `A-Z`, `a-z`, `0-9`, `+`, `/`. Each character represents exactly 6 bits.

---

## 7. Practice Problems
1.  **Codeforces 492C**: Vanya and Exams.
2.  **UVa 10018**: Reverse and Add.
3.  **SPOJ BINPRNUM**: Binary Prime Numbers.

## 8. Conclusion
Base conversion is a frequent sub-task in larger problems. While simple for small numbers, be careful with overflow when the input string represents a number larger than $2^{63}-1$. In such cases, string-based BigInt logic is necessary.