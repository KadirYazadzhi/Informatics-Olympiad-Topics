# 🔁 Recursion: Principles and Depth

Recursion is a fundamental concept where a function calls itself to solve a smaller instance of the same problem. It is the basis for DFS, Divide and Conquer, and Dynamic Programming.

## 1. Anatomy of Recursion

A correct recursive function must have:
1.  **Base Case**: The condition that stops the recursion. Without it, you get infinite recursion and `Stack Overflow`.
2.  **Recursive Step**: Calling the function with parameters that move towards the base case.

### Example: Factorial
$n! = n \times (n-1)!$, base case $0! = 1$.

```cpp
long long factorial(int n) {
    if (n == 0) return 1; // Base case
    return n * factorial(n - 1); // Recursive step
}
```

## 2. The Call Stack

When a function is called, a "Stack Frame" is pushed onto the system stack. It holds local variables and the return address.
*   **Recursion Depth**: The maximum number of nested calls. Usually limited (e.g., 256MB stack allows $\approx 10^5-10^6$ calls).
*   **Stack Overflow**: Occurs when the recursion is too deep.

### Visualization `factorial(3)`
1. `factorial(3)` calls `factorial(2)`
2. `factorial(2)` calls `factorial(1)`
3. `factorial(1)` calls `factorial(0)`
4. `factorial(0)` returns `1` -> stack pops
5. `factorial(1)` returns `1*1` -> stack pops
6. `factorial(2)` returns `2*1` -> stack pops
7. `factorial(3)` returns `3*2` -> stack pops

## 3. Examples

### 3.1. Fibonacci (Naive vs DP)
$F_n = F_{n-1} + F_{n-2}$.
Naive recursion is $O(2^n)$ (Exponential!).
**Fix**: Use Memoization (store results in an array) to make it $O(N)$.

### 3.2. GCD (Euclid)
```cpp
int gcd(int a, int b) {
    if (b == 0) return a;
    return gcd(b, a % b);
}
```
This is **Tail Recursion** (recursive call is the last action). Compilers can optimize this into a loop.

### 3.3. Printing Digits
To print digits of `n` in order:
```cpp
void printDigits(int n) {
    if (n < 10) { cout << n << " "; return; }
    printDigits(n / 10);
    cout << n % 10 << " ";
}
```

### 3.4. Fast Modular Exponentiation
Compute $a^b \pmod m$ in $O(\log b)$.
```cpp
long long power(long long a, long long b, long long m) {
    if (b == 0) return 1;
    long long res = power(a, b / 2, m);
    res = (res * res) % m;
    if (b % 2 == 1) res = (res * a) % m;
    return res;
}
```

## 4. Common Pitfalls
1.  **Missing Base Case**: Immediate crash.
2.  **Global vs Local**: Large arrays inside recursive functions cause stack overflow. Use global variables or pass by reference.
3.  **Redundant Computations**: Like in naive Fibonacci. Always check if you are solving the same subproblem multiple times.

## 5. Practice
1.  Check if a string is a palindrome using recursion.
2.  Binary Search (Recursive implementation).
3.  Towers of Hanoi.
4.  Flood Fill (DFS in a grid).