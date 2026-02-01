# 🔢 One-Dimensional Arrays: Foundations and Techniques

The one-dimensional array is the most basic yet versatile data structure in computer science. It stores elements of the same type in contiguous memory locations, allowing for $O(1)$ random access.

---

## 1. Memory and Performance

### 1.1. Contiguity
Because elements are side-by-side, arrays have excellent **CPU cache locality**. Iterating through an array is one of the fastest operations a processor can perform.

### 1.2. Static vs. Dynamic Allocation
*   **Static (`int a[100]`)**: Size is fixed at compile-time. Usually stored on the **Stack**.
*   **Dynamic (`vector<int> a(n)`)**: Size determined at runtime. Stored on the **Heap**.

**Important**: Local static arrays in C++ are limited by the stack size (typically 1MB - 8MB). For arrays larger than $10^5$ elements, always declare them **globally** or use `std::vector`.

---

## 2. Essential Techniques

### 2.1. Prefix Sums
To answer "sum of range $[L, R]$" queries in $O(1)$.
1.  Compute $P[i] = A[0] + \dots + A[i]$.
2.  $	ext{Sum}(L, R) = P[R] - P[L-1]$.

```cpp
vector<long long> pref(n + 1, 0);
for (int i = 0; i < n; i++) pref[i+1] = pref[i] + A[i];
```

### 2.2. Difference Arrays
To perform "add $X$ to range $[L, R]$" updates in $O(1)$.
1.  Let $D[i] = A[i] - A[i-1]$.
2.  Range update $[L, R] + X \implies D[L] += X, D[R+1] -= X$.
3.  Reconstruct $A$ using prefix sums of $D$.

### 2.3. Sliding Window
Used to find optimal subarrays (e.g., "max sum subarray of fixed length $K$").
Instead of recomputing the sum, subtract the element leaving the window and add the one entering.

---

## 3. Common Array Tasks

### 3.1. Frequency Array
If values are small integers, use their values as indices to count occurrences.
```cpp
int freq[1001] = {0};
for (int x : A) freq[x]++;
```

### 3.2. Two Pointers
Used on **sorted** arrays to find pairs satisfying a condition ($O(N)$ instead of $O(N^2)$).
```cpp
int i = 0, j = n - 1;
while (i < j) {
    if (A[i] + A[j] == target) return true;
    if (A[i] + A[j] < target) i++;
    else j--;
}
```

---

## 4. Safety and Best Practices

1.  **Bounds Checking**: Accessing `A[n]` is **Undefined Behavior**. It might crash (Segfault) or silently corrupt other data.
2.  **Initialization**: Global arrays are initialized to 0. Local static arrays contain "garbage" (random values). Always initialize: `int a[100] = {0};`.
3.  **Use `long long`**: When summing large array elements, `int` will overflow ($2^{31}-1 \approx 2 \cdot 10^9$).

---

## 5. Practice Problems
1.  **CSES Static Range Sum Queries**: Prefix Sums.
2.  **CSES Maximum Subarray Sum**: Kadane's Algorithm.
3.  **Codeforces 279B**: Books (Sliding Window).
4.  **UVa 10038**: Jolly Jumpers.

## 6. Conclusion
The array is the starting point for almost all competitive programming algorithms. Mastering prefix sums, two pointers, and sliding windows will solve a surprisingly large percentage of introductory problems.
