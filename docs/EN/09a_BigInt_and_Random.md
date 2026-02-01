# 🧮 BigInt and Random Numbers

## 🧮 BigInt Implementations (Simple)

When numbers exceed $10^{18}$, we use strings or vectors to store them.

### Basic Addition of Long Numbers
```cpp
string add(string a, string b) {
    string res = "";
    int i = a.size() - 1, j = b.size() - 1, carry = 0;
    while (i >= 0 || j >= 0 || carry) {
        int sum = carry + (i >= 0 ? a[i--] - '0' : 0) + (j >= 0 ? b[j--] - '0' : 0);
        res += to_string(sum % 10);
        carry = sum / 10;
    }
    reverse(res.begin(), res.end());
    return res;
}
```

---

## 🎲 Random Numbers

In C++, `rand()` is common but `std::mt19937` is preferred for quality and speed.

### Using `mt19937` (Mersenne Twister)
```cpp
#include <random>
#include <chrono>

mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());

int getRandom(int l, int r) {
    return uniform_int_distribution<int>(l, r)(rng);
}
```

## 🏁 Conclusion
BigInt is a must-know for math tasks. Always use a stable random generator like `mt19937` to avoid predictable patterns.
