# 🔢 One-Dimensional Arrays

Arrays are the simplest data structure, storing elements of the same type contiguously in memory.

## 1. Declaration

### 1.1. Static Arrays
Size must be known at compile time.
```cpp
int arr[100]; // Indices 0 to 99
```

### 1.2. Global vs Local
*   **Local**: Stored on **Stack**. Limit ~2-8MB.
*   **Global**: Stored in **Data Segment**. Limit ~256MB+. Always zero-initialized.

**Tip**: Always declare large arrays globally or use `vector`.

## 2. Common Errors
*   **Out of Bounds**: Accessing `arr[N]` is undefined behavior (often segfault).
*   **Off-by-one**: Loop condition `i <= n` instead of `i < n`.

## 3. Basic Algorithms

### 3.1. Min/Max
```cpp
int minVal = arr[0];
for (int i = 1; i < n; i++) minVal = min(minVal, arr[i]);
```

### 3.2. Sum and Average
Use `long long` for sum to avoid overflow.

### 3.3. Reverse
`std::reverse(arr, arr + n);` or manual swap.

### 3.4. Check Sorted
Loop `i` from `0` to `n-2`. If `arr[i] > arr[i+1]`, then not sorted.

## 4. `std::vector` - The Modern Array
Preferred in C++.
*   Dynamic size.
*   Stored on **Heap**.
*   `v.push_back(x)`, `v.size()`.

```cpp
vector<int> v(n); // Vector of size n (zeros)
```

## 5. Practice
1.  Reverse an array.
2.  Find second largest element.
3.  Remove duplicates from sorted array.
4.  Rotate array.

## 6. Conclusion
Master array indexing. It's the foundation of everything else.