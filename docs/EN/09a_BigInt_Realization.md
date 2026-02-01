# 🧮 BigInt Realization

Standard types in C++ are limited. For problems with 1000-digit numbers, we must use our own implementation.

## 🏗️ Storing BigInt
Best to keep digits in `std::vector<int>` in reverse order. For faster operation, use base $10^9$ (9 digits per element).

```cpp
struct BigInt {
    vector<int> d;
    static const int BASE = 1e9;
    
    // Constructor from string
    BigInt(string s) {
        for (int i = s.size(); i > 0; i -= 9) {
            if (i < 9) d.push_back(stoi(s.substr(0, i)));
            else d.push_back(stoi(s.substr(i - 9, 9)));
        }
    }
};
```

## ➕ Operations

### 1. Addition
Move right to left and handle the `carry`.
```cpp
void add(BigInt &a, const BigInt &b) {
    int carry = 0;
    for (size_t i = 0; i < max(a.d.size(), b.d.size()) || carry; ++i) {
        if (i == a.d.size()) a.d.push_back(0);
        long long cur = a.d[i] + carry + (i < b.d.size() ? b.d[i] : 0);
        a.d[i] = cur % BASE;
        carry = cur / BASE;
    }
}
```

### 2. Multiplication
Every element of the first number is multiplied by every element of the second. Complexity $O(N \cdot M)$.

## 🏁 Conclusion
A custom BigInt implementation is a "black box" every competitor should have ready. For extremely large numbers, FFT is used (Topic 44).
