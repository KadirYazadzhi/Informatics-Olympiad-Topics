# 🔢 Numeral Systems

How we represent numbers matters. Computers use binary, but we often use hex for compactness.

## 1. Common Systems
*   **Decimal (Base 10)**: 0-9.
*   **Binary (Base 2)**: 0-1.
*   **Hexadecimal (Base 16)**: 0-9, A-F.

## 2. Conversion Algorithms

### 2.1. Decimal to Base $K$
Repeatedly take modulo $K$ and divide by $K$.
The remainders (in reverse order) form the number.

```cpp
string toBase(long long n, int base) {
    if (n == 0) return "0";
    string digits = "0123456789ABCDEF";
    string res = "";
    while (n > 0) {
        res += digits[n % base];
        n /= base;
    }
    reverse(res.begin(), res.end());
    return res;
}
```

### 2.2. Base $K$ to Decimal
Sum the weighted digits: $\sum d_i \cdot K^i$.

```cpp
long long toDec(string s, int base) {
    return stoll(s, nullptr, base); // Built-in C++ function
}
```

## 3. Tricks
*   **Binary to Hex**: Group bits by 4. `1010` -> `A`.
*   **Bitwise operations**: Work directly on the binary representation.

## 4. Built-in Manipulators
```cpp
cout << hex << 255; // ff
cout << uppercase << hex << 255; // FF
cout << dec; // Switch back
```

## 5. Practice
1.  **UVa 382**: Perfection.
2.  **Codeforces 1144C**: Two Shuffled Sequences.

## 6. Conclusion
For huge numbers (e.g. 100 digits in base 10), standard types overflow. Use BigInt or string manipulation.
