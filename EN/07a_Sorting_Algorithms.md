# 🔍 Sorting Algorithms: From Basics to Optimization

Sorting is one of the most studied topics in computer science. Different algorithms have different applications depending on data size and memory requirements.

## 🐢 Simple Algorithms ($O(N^2)$)

### 1. Bubble Sort
The simplest but slowest. Used only for very small arrays ($N < 1000$).
- **Optimization**: If no swaps occurred during a pass, the array is sorted and we can stop.

### 2. Insertion Sort
Very efficient for "nearly sorted" arrays.
- **Principle**: Take an element and "insert" it into its correct position among the already sorted elements before it.

---

## ⚡ Fast Algorithms ($O(N \log N)$)

### 1. Quick Sort
Uses "Divide and Conquer" strategy. A **Pivot** is chosen.
- **Important**: Poor pivot choice can lead to $O(N^2)$. C++ `std::sort` uses a hybrid method to avoid this.

### 2. Merge Sort
Always guarantees $O(N \log N)$.
- **Stability**: Preserves the relative order of equal elements.
- **Memory**: Requires $O(N)$ additional memory for the auxiliary array.

---

## 🔢 Non-comparison Sorts ($O(N + K)$)

### 1. Counting Sort
If values are in a small range $[0, K]$.
- Simply count occurrences of each number and print them out.

### 2. Radix Sort
Sorts numbers digit by digit (from least significant to most significant).

---

## 🏁 Which algorithm to use?
- **Standard**: Always `std::sort()`.
- **Need Stability**: `std::stable_sort()`.
- **Small range of numbers**: Counting Sort.
- **Limited memory**: Heapsort or In-place Quick Sort.
