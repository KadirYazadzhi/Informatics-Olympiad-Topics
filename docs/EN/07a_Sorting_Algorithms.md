# 🔍 Sorting Algorithms: A Comprehensive Guide

Sorting is the process of arranging elements in a specific order (ascending or descending). It is one of the most studied problems in computer science and serves as a prerequisite for many other algorithms like Binary Search, Kruskal's, and Greedy approaches.

---

## 1. Simple Sorts ($O(N^2)$)

These are easy to implement but inefficient for large $N$.

### 1.1. Selection Sort
Iteratively find the minimum element from the unsorted part and put it at the beginning.
*   **Time**: Always $O(N^2)$.
*   **Space**: $O(1)$.

### 1.2. Bubble Sort
Repeatedly swaps adjacent elements if they are in the wrong order.
*   **Best case**: $O(N)$ (if already sorted and optimized).
*   **Average/Worst**: $O(N^2)$.

### 1.3. Insertion Sort
Builds the final sorted array one item at a time. It is much more efficient than selection/bubble sort for small datasets.
*   **Key usage**: Base case for hybrid algorithms like Timsort.

---

## 2. Efficient Sorts ($O(N \log N)$)

Standard for $N \ge 10^5$.

### 2.1. Merge Sort (Stable)
A divide-and-conquer algorithm.
1.  Divide the array into two halves.
2.  Recursively sort both halves.
3.  **Merge** the sorted halves.

```cpp
void merge(int a[], int l, int m, int r) {
    vector<int> L, R;
    // Copy data to temp vectors...
    // Merge back to a[]...
}
```
*   **Property**: Stable (preserves relative order of equal elements).
*   **Usage**: Counting **Inversions** in an array.

### 2.2. Quick Sort (Unstable)
Picks a "pivot" and partitions the array into elements smaller and larger than the pivot.
*   **Worst case**: $O(N^2)$ if pivot is poorly chosen.
*   **Average case**: $O(N \log N)$.
*   **Optimization**: Randomly shuffle the array or use "median of three" to pick the pivot.

### 2.3. Heap Sort (Unstable)
Uses a Binary Heap data structure.
*   **Time**: Always $O(N \log N)$.
*   **Space**: $O(1)$ (in-place).

---

## 3. Specialized Sorts ($O(N+K)$)

Only possible if data has specific properties.

### 3.1. Counting Sort
If values are integers in a small range $[0, K]$.
1.  Count occurrences of each value.
2.  Use prefix sums to determine positions.
3.  Place elements in sorted array.

### 3.2. Radix Sort
Sorts numbers digit by digit (from least to most significant).
*   Complexity: $O(D \cdot (N + B))$, where $D$ is digits and $B$ is base.

---

## 4. Using C++ `std::sort`

In most cases, use the highly optimized `std::sort` from `<algorithm>`. It uses **Introsort** (QuickSort, falling back to HeapSort if recursion depth exceeds a limit).

### 4.1. Basic usage
```cpp
sort(v.begin(), v.end()); // Ascending
sort(v.rbegin(), v.rend()); // Descending
```

### 4.2. Custom Comparators
Essential for sorting structs or custom logic.

```cpp
struct Edge {
    int u, v, w;
};

bool compareEdges(const Edge& a, const Edge& b) {
    return a.w < b.w; // Sort by weight
}

sort(edges.begin(), edges.end(), compareEdges);
```

### 4.3. Lambda Expressions (C++11)
```cpp
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;
});
```

---

## 5. Summary Table

| Algorithm | Avg Time | Worst Time | Space | Stable? |
| :--- | :--- | :--- | :--- | :--- |
| Bubble Sort | $O(N^2)$ | $O(N^2)$ | $O(1)$ | Yes |
| Merge Sort | $O(N \log N)$ | $O(N \log N)$ | $O(N)$ | Yes |
| Quick Sort | $O(N \log N)$ | $O(N^2)$ | $O(\log N)$| No |
| Heap Sort | $O(N \log N)$ | $O(N \log N)$ | $O(1)$ | No |
| Counting | $O(N+K)$ | $O(N+K)$ | $O(K)$ | Yes |

## 6. Practice Problems
1.  **SPOJ INVCNT**: Inversion Count (Merge Sort).
2.  **CSES Distinct Numbers**: Sort + Unique.
3.  **Codeforces 1144C**: Sorting and constraints.

## 7. Conclusion
Choosing the right sorting algorithm depends on stability requirements and memory constraints. For general use, `std::sort` is nearly unbeatable.
