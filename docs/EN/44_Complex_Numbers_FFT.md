# 📐 Complex Numbers and FFT: Expert Level

This topic covers working with complex numbers in competitive programming and one of the most important algorithms in computer science – the Fast Fourier Transform (FFT).

## 1. Complex Numbers

Complex numbers are an extension of real numbers. They are of the form $z = a + bi$, where:
*   $a$ is the **real part** ($\text{Re}(z)$).
*   $b$ is the **imaginary part** ($\text{Im}(z)$).
*   $i$ is the imaginary unit, such that $i^2 = -1$.

### 1.1. Arithmetic Operations
Given two numbers $z_1 = a_1 + b_1 i$ and $z_2 = a_2 + b_2 i$:
*   **Addition**: $(a_1 + a_2) + (b_1 + b_2)i$
*   **Subtraction**: $(a_1 - a_2) + (b_1 - b_2)i$
*   **Multiplication**: $(a_1 a_2 - b_1 b_2) + (a_1 b_2 + a_2 b_1)i$ (since $i^2 = -1$)

### 1.2. Geometric Interpretation
In the complex plane (Argand diagram), the number $z = x + iy$ is represented as a point $(x, y)$.
*   **Modulus** (magnitude): $|z| = \sqrt{x^2 + y^2}$.
*   **Argument** (angle): $\arg(z) = \theta$ is the angle with the positive x-axis.
*   **Polar form**: $z = r(\cos \theta + i \sin \theta)$, where $r = |z|$.
*   **Euler's Formula**: $e^{i\theta} = \cos \theta + i \sin \theta$. Thus $z = r e^{i\theta}$.

Multiplication in polar form is intuitive:
$z_1 \cdot z_2 = (r_1 r_2) e^{i(\theta_1 + \theta_2)}$.
*   Moduli are multiplied.
*   Angles are added.

### 1.3. C++ `std::complex`
C++ provides a built-in class `<complex>`.

```cpp
#include <iostream>
#include <complex>
#include <cmath>

using namespace std;

typedef complex<double> cd;
const double PI = acos(-1);

int main() {
    cd a(3, 4); // 3 + 4i
    cd b(1, -2); // 1 - 2i
    
    cd sum = a + b;
    cd prod = a * b;
    
    cout << "Sum: " << sum.real() << " + " << sum.imag() << "i\n";
    cout << "Abs of a: " << abs(a) << "\n"; // 5
    cout << "Arg of a: " << arg(a) << "\n"; // in radians
    
    // Create using polar coordinates
    cd c = polar(1.0, PI / 2); // 1 * e^(i*pi/2) = i
    cout << "Polar (0, 1): " << c << "\n";
    
    return 0;
}
```

---

## 2. Polynomials

A polynomial of degree $n-1$ is defined as:
$A(x) = a_0 + a_1 x + a_2 x^2 + \dots + a_{n-1} x^{n-1}$

There are two main ways to represent a polynomial:
1.  **Coefficient Representation**: A list of coefficients $[a_0, a_1, \dots, a_{n-1}]$.
    *   Addition: $O(n)$.
    *   Multiplication: $O(n^2)$.
    *   Evaluation (Horner): $O(n)$.
2.  **Point-Value Representation**: A set of $n$ pairs ${\{(x_0, y_0), \dots, (x_{n-1}, y_{n-1})\}}$, where $y_k = A(x_k)$. A polynomial of degree $n-1$ is uniquely determined by $n$ points.
    *   Addition: $O(n)$.
    *   Multiplication: $O(n)$ (multiply values $C(x_k) = A(x_k) \cdot B(x_k)$)!

**FFT Idea**: We want to multiply two polynomials quickly.
1.  Convert them from Coefficient to Point-Value form (**DFT**).
2.  Multiply them in $O(n)$ in Point-Value form.
3.  Convert back to Coefficient form (**Inverse DFT**).

If we choose arbitrary $x$, transformation is slow ($O(n^2)$). But if we choose special points – **Complex Roots of Unity**, we can do it in $O(n \log n)$.

---

## 3. Fast Fourier Transform (FFT)

### 3.1. Roots of Unity
The complex $n$-th roots of unity are solutions to $x^n = 1$. There are exactly $n$ such numbers, evenly spaced on the unit circle.
$\omega_n^k = e^{i \frac{2\pi k}{n}} = \cos\left(\frac{2\pi k}{n}\right) + i \sin\left(\frac{2\pi k}{n}\right)$

### 3.2. Cooley-Tukey Algorithm
Let $n$ be a power of 2. We divide $A(x)$ into even and odd indices:
$A(x) = (a_0 + a_2 x^2 + \dots) + x(a_1 + a_3 x^2 + \dots)$
$A(x) = A_{even}(x^2) + x A_{odd}(x^2)$

Using properties of roots of unity, the problem for size $n$ reduces to two subproblems of size $n/2$.
Complexity: $T(n) = 2T(n/2) + O(n) \implies O(n \log n)$.

### 3.3. Iterative Implementation

```cpp
#include <vector>
#include <complex>
#include <cmath>
#include <algorithm>

using namespace std;
typedef complex<double> cd;
const double PI = acos(-1);

void fft(vector<cd> & a, bool invert) {
    int n = a.size();

    // Bit-reversal permutation
    for (int i = 1, j = 0; i < n; i++) {
        int bit = n >> 1;
        for (; j & bit; bit >>= 1)
            j ^= bit;
        j ^= bit;
        if (i < j) swap(a[i], a[j]);
    }

    // Butterfly operations
    for (int len = 2; len <= n; len <<= 1) {
        double ang = 2 * PI / len * (invert ? -1 : 1);
        cd wlen(cos(ang), sin(ang));
        for (int i = 0; i < n; i += len) {
            cd w(1);
            for (int j = 0; j < len / 2; j++) {
                cd u = a[i + j], v = a[i + j + len / 2] * w;
                a[i + j] = u + v;
                a[i + j + len / 2] = u - v;
                w *= wlen;
            }
        }
    }

    if (invert) {
        for (cd & x : a) x /= n;
    }
}
```

### 3.4. Polynomial Multiplication
1.  Resize vectors to the nearest power of 2 (at least $\deg(A) + \deg(B) + 1$).
2.  Apply FFT.
3.  Point-wise multiply.
4.  Apply Inverse FFT.
5.  Round real parts.

```cpp
vector<int> multiply(vector<int> const& a, vector<int> const& b) {
    vector<cd> fa(a.begin(), a.end()), fb(b.begin(), b.end());
    int n = 1;
    while (n < a.size() + b.size()) 
        n <<= 1;
    fa.resize(n);
    fb.resize(n);

    fft(fa, false);
    fft(fb, false);

    for (int i = 0; i < n; i++)
        fa[i] *= fb[i];

    fft(fa, true);

    vector<int> result(n);
    for (int i = 0; i < n; i++)
        result[i] = round(fa[i].real());
    
    return result;
}
```

---

## 4. Number Theoretic Transform (NTT)

FFT uses `double`, leading to precision errors. For modular arithmetic problems, we use **NTT**.
*   Instead of complex roots, we use **primitive roots** modulo $P$.
*   Requires $P = c \cdot 2^k + 1$ (e.g., 998244353).
*   Guarantees exact integer results.

---

## 5. Fast Walsh-Hadamard Transform (FWHT)

Used for bitwise convolutions (XOR, AND, OR).
$C[k] = \sum_{i \oplus j = k} A[i] B[j]$.
Complexity: $O(N \log N)$.

---

## 6. Practice Problems
1.  **SPOJ POLARIVAL**: Polynomial evaluation.
2.  **SPOJ MUL**: Big Integer Multiplication.
3.  **Codeforces 993E**: Nikita and Order Statistics.
4.  **CSES Polynomial Queries**.

```