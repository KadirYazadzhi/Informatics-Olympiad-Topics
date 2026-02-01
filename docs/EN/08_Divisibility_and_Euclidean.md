# ➗ Divisibility and Euclidean Algorithm

Number theory is core to CP.

## 1. Divisibility Rules
*   **2**: Last digit even.
*   **3**: Sum of digits divisible by 3.
*   **4**: Last 2 digits divisible by 4.
*   **5**: Ends in 0 or 5.
*   **6**: Divisible by 2 and 3.
*   **8**: Last 3 digits divisible by 8.
*   **9**: Sum of digits divisible by 9.
*   **11**: Alternating sum of digits is 0 or divisible by 11.

## 2. GCD and LCM

### 2.1. Euclid's Algorithm (GCD)
$\gcd(a, b) = \gcd(b, a \pmod b)$.

```cpp
long long gcd(long long a, long long b) {
    while (b) {
        a %= b;
        swap(a, b);
    }
    return a;
}
```

### 2.2. LCM
$\text{lcm}(a, b) = \frac{|a \cdot b|}{\gcd(a, b)}$.
Divide first to avoid overflow!

## 3. Extended Euclidean Algorithm
Finds $x, y$ such that $ax + by = \gcd(a, b)$.
Key for finding **Modular Inverse**.

```cpp
long long extended_gcd(long long a, long long b, long long &x, long long &y) {
    if (b == 0) {
        x = 1;
        y = 0;
        return a;
    }
    long long x1, y1;
    long long d = extended_gcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - y1 * (a / b);
    return d;
}
```

## 4. Practice
1.  **Codeforces 1152C**: Neko does Maths (LCM properties).
2.  **UVa 10104**: Euclid Problem.

```