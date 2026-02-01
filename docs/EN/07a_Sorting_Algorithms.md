# 🔍 Sorting Algorithms

Sorting is a fundamental operation. While `std::sort` is usually enough, understanding the mechanics is key for modification (e.g., counting inversions).

## 1. O(N^2) Algorithms
Good for $N \le 2000$.

### 1.1. Bubble Sort
Repeatedly swaps adjacent elements if they are in wrong order.
Optimization: Stop if no swaps occurred in a pass.

### 1.2. Insertion Sort
Builds the sorted array one item at a time. Very fast for small or nearly sorted data.

## 2. O(N log N) Algorithms
Required for $N \le 10^5$.

### 2.1. Merge Sort
Divide and Conquer.
1.  Divide array into halves.
2.  Sort halves recursively.
3.  **Merge** the sorted halves.
**Key Property**: Stable. Used for counting **Inversions** ($i < j$ but $A[i] > A[j]$).

### 2.2. Quick Sort
Partition based on a pivot.
*   Elements < pivot go left.
*   Elements > pivot go right.
Average $O(N \log N)$, worst case $O(N^2)$.
C++ `std::sort` uses a hybrid (Introsort) to guarantee $O(N \log N)$.

## 3. O(N) Algorithms (Non-comparison)

### 3.1. Counting Sort
If values are in range $[0, K]$.
Count frequencies of each value, then reconstruct.

### 3.2. Radix Sort
Sorts digit by digit (from least significant). Uses Counting Sort as a subroutine.

## 4. Custom Sorting
Use a comparator function or lambda.

```cpp
struct Point { int x, y; };

// Sort by x, then by y
sort(pts.begin(), pts.end(), [](const Point& a, const Point& b) {
    if (a.x != b.x) return a.x < b.x;
    return a.y < b.y;
});
```

## 5. Practice
1.  **Codeforces 61E**: Enemy is weak (Inversion counting).
2.  **SPOJ INVCNT**: Inversion Count.
3.  **CSES Towers**: Sorting + Multiset/Greedy.