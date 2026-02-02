# 📐 Complex Numbers and FFT: Expert Level

This topic covers working with complex numbers in competitive programming and one of the most important algorithms in computer science – the Fast Fourier Transform (FFT).

## 1. Complex Numbers

Complex numbers are an extension of the real numbers. They have the form $z = a + bi$, where:
*   $a$ is the **real part** ($\text{Re}(z)$).
*   $b$ is the **imaginary part** ($\text{Im}(z)$).
*   $i$ is the imaginary unit, for which $i^2 = -1$.

### 1.1. Arithmetic Operations
If we have two numbers $z_1 = a_1 + b_1 i$ and $z_2 = a_2 + b_2 i$:
*   **Addition**: $(a_1 + a_2) + (b_1 + b_2)i$.
*   **Subtraction**: $(a_1 - a_2) + (b_1 - b_2)i$.
*   **Multiplication**: $(a_1 a_2 - b_1 b_2) + (a_1 b_2 + a_2 b_1)i$ (because $i^2 = -1$).

### 1.2. Geometric Interpretation
In the complex plane (Argand diagram), the number $z = x + iy$ is represented as a point $(x, y)$.
*   **Modulus** (length): $|z| = \sqrt{x^2 + y^2}$.
*   **Argument** (angle): $\arg(z) = \theta$ is the angle with the positive x-axis.
*   **Polar form**: $z = r(\cos \theta + i \sin \theta)$, where $r = |z|$.
*   **Euler's formula**: $e^{i\theta} = \cos \theta + i \sin \theta$. Therefore $z = r e^{i\theta}$.

Multiplication of complex numbers in polar form is very intuitive:
$z_1 \cdot z_2 = (r_1 e^{i\theta_1}) (r_2 e^{i\theta_2}) = (r_1 r_2) e^{i(\theta_1 + \theta_2)}$.
*   Moduli are multiplied.
*   Angles are added.

### 1.3. C++ `std::complex`
C++ provides a built-in `<complex>` class.

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
    
    // Creation via polar coordinates
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
1.  **Coefficient form**: A list of coefficients $[a_0, a_1, \dots, a_{n-1}]$.
    *   Addition: $O(n)$.
    *   Multiplication: $O(n^2)$ (each with each).
    *   Value calculation (Horner's method): $O(n)$.
2.  **Point-Value form**: A set of $n$ pairs ${\{(x_0, y_0), \dots, (x_{n-1}, y_{n-1})\}}$, where $y_k = A(x_k)$. Every polynomial of degree $n-1$ is uniquely determined by $n$ points.
    *   Addition: $O(n)$ (values for the same $x$ are added).
    *   Multiplication: $O(n)$ (values are multiplied: $C(x_k) = A(x_k) \cdot B(x_k)$)!

**The idea of FFT**: We want to multiply two polynomials quickly.
1.  Transform them from coefficient to point-value form (**DFT**).
2.  Multiply them in $O(n)$ in point-value form.
3.  Transform the result back to coefficient form (**Inverse DFT**).

If we choose arbitrary $x$, the transformation is slow ($O(n^2)$). But if we choose special points – **the complex roots of unity**, we can do it in $O(n \log n)$.

---

## 3. Fast Fourier Transform (FFT)

### 3.1. Roots of Unity
The complex $n$-th roots of unity are solutions to the equation $x^n = 1$. There are exactly $n$ such numbers, evenly spaced on the unit circle.
$\\omega_n^k = e^{i \frac{2\pi k}{n}} = \cos\left(\frac{2\pi k}{n}\right) + i \sin\left(\frac{2\pi k}{n}\right)$

The principal (primitive) root is $\\omega_n = e^{i \frac{2\pi}{n}}$. The others are its powers: $\\omega_n^0, \\omega_n^1, \dots, \\omega_n^{n-1}$.

Important properties:
1.  $\\omega_n^n = 1$.
2.  $\\omega_n^{n/2} = -1$.
3.  $\\omega_{2n}^{2k} = \\omega_n^k$.

### 3.2. Cooley-Tukey Algorithm
Let $n$ be an even number. We split the polynomial $A(x)$ into two parts – even and odd indices:
$A(x) = (a_0 + a_2 x^2 + \dots) + x(a_1 + a_3 x^2 + \dots)$
$A(x) = A_{even}(x^2) + x A_{odd}(x^2)$

To calculate $A(x)$ at points $x = \\omega_n^k$, we notice symmetry:
For $0 \le k < n/2$:
$x = \\omega_n^k \implies x^2 = \\omega_n^{2k} = \\omega_{n/2}^k$
$A(\omega_n^k) = A_{even}(\\omega_{n/2}^k) + \\omega_n^k A_{odd}(\\omega_{n/2}^k)$

For the "second half" $k' = k + n/2$:
$\\omega_n^{k + n/2} = -\\omega_n^k$
$A(\omega_n^{k+n/2}) = A_{even}(\\omega_{n/2}^k) - \\omega_n^k A_{odd}(\\omega_{n/2}^k)$

Thus the problem for size $n$ reduces to two subproblems for size $n/2$.
Complexity: $T(n) = 2T(n/2) + O(n) \implies O(n \log n)$.

### 3.3. Recursive Implementation

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
    if (n == 1) return;

    vector<cd> a0(n / 2), a1(n / 2);
    for (int i = 0; 2 * i < n; i++) {
        a0[i] = a[2 * i];
        a1[i] = a[2 * i + 1];
    }

    fft(a0, invert);
    fft(a1, invert);

    double ang = 2 * PI / n * (invert ? -1 : 1);
    cd w(1), wn(cos(ang), sin(ang));

    for (int i = 0; 2 * i < n; i++) {
        a[i] = a0[i] + w * a1[i];
        a[i + n / 2] = a0[i] - w * a1[i];
        if (invert) {
            a[i] /= 2;
            a[i + n / 2] /= 2;
        }
        w *= wn;
    }
}
```

### 3.4. Iterative Implementation (Optimized)
Recursion can be slow. The iterative approach uses "Bit-reversal permutation" – the indices are rearranged according to the mirror representation of their bits.

```cpp
void fft_iterative(vector<cd> & a, bool invert) {
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

### 3.5. Polynomial Multiplication
To multiply two polynomials $A$ and $B$:
1.  Extend the vectors with zeros to the nearest power of 2 that is at least $\deg(A) + \deg(B) + 1$.
2.  Apply FFT to $A$ and $B$.
3.  Multiply element-wise $A[i] = A[i] \cdot B[i]$.
4.  Apply Inverse FFT to the result.
5.  (Optional) Round the real parts to the nearest integer.

```cpp
vector<int> multiply(vector<int> const& a, vector<int> const& b) {
    vector<cd> fa(a.begin(), a.end()), fb(b.begin(), b.end());
    int n = 1;
    while (n < (int)(a.size() + b.size())) 
        n <<= 1;
    fa.resize(n);
    fb.resize(n);

    fft_iterative(fa, false);
    fft_iterative(fb, false);

    for (int i = 0; i < n; i++)
        fa[i] *= fb[i];

    fft_iterative(fa, true);

    vector<int> result(n);
    for (int i = 0; i < n; i++)
        result[i] = round(fa[i].real());
    
    // Remove leading zeros if needed
    return result;
}
```

---

## 4. Number Theoretic Transform (NTT)

FFT uses complex numbers (`double`), which leads to rounding errors. In tasks requiring a result modulo some number, we use **NTT**.
This is the equivalent of FFT in finite fields (Modular Arithmetic).
*   Instead of complex roots of unity, we use **primitive roots** modulo $P$.
*   Requires $P$ to be of a special form: $P = c \cdot 2^k + 1$.
*   Example: $998244353 = 119 \cdot 2^{23} + 1$. Primitive root $g=3$.

**Advantage**: Works with integers (`long long`), no loss of precision.

### Application of NTT
Everything is the same as FFT, but:
1.  All operations are modulo $P$.
2.  $\\omega_n$ is replaced by $g^{(P-1)/n} \pmod P$.
3.  $\\omega_n^{-1}$ is replaced by the modular inverse.

```cpp
const int mod = 998244353;
const int root = 3; // Primitive root
// Implementation requires modular exponentiation and modular inverse
```

---

## 5. Fast Walsh-Hadamard Transform (FWHT)

FWHT is a variant of FFT used for bitmask operations. If we want to find a polynomial $C$ such that:
$C[k] = \sum_{i \oplus j = k} A[i] B[j]$ (XOR convolution)
Or for AND/OR operations.

For XOR FWHT, the transformation matrix is very simple:
$\\begin{pmatrix} 1 & 1 \\ 1 & -1 \\end{pmatrix}$
This allows multiplication in $O(N \log N)$ instead of $O(N^2)$.

---

## 6. Practice Tasks
1.  **SPOJ POLARIVAL**: Evaluating a polynomial value.
2.  **SPOJ MUL**: Multiplication of large numbers (consider the digits as coefficients).
3.  **Codeforces 993E**: Nikita and Order Statistics (counting subarrays).
4.  **CSES Problem Set**: Polynomial Queries.

FFT is a powerful tool not only for polynomials but for any tasks involving combinatorics and sums with a fixed index (convolutions).

```
