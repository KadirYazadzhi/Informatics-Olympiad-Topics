# 🧮 One-dimensional Arrays: Memory and Management

A one-dimensional array is a continuous region in memory storing elements of the same type. In C++, arrays are extremely fast due to direct memory access.

## 🧠 Internal Representation and Memory
When you define `int arr[10]`, the compiler allocates `10 * sizeof(int)` bytes (usually 40 bytes).
- **Address Arithmetic**: The address of element `arr[i]` is calculated as `BaseAddress + i * sizeof(type)`. This happens in $O(1)$ time.
- **Cache Locality**: Since elements are adjacent, the CPU loads them into the cache simultaneously, making arrays faster than linked lists.

## 🚀 Optimization of Array Work
1. **Passing to Functions**: Arrays are always passed by pointer. Changes inside the function affect the original array.
2. **Static vs Dynamic**:
   - Static: `int a[100];` (on stack, fixed size).
   - Dynamic: `std::vector<int> v;` (on heap, variable size).

## 🛠️ Common Techniques (Two Pointers)
The "two pointers" technique allows solving problems in $O(N)$ instead of $O(N^2)$.
- **Example**: Finding two elements with sum $S$ in a sorted array.
```cpp
int i = 0, j = n - 1;
while (i < j) {
    if (a[i] + a[j] == S) return {i, j};
    if (a[i] + a[j] < S) i++;
    else j--;
}
```

## 🏁 Conclusion
Arrays are the foundation of almost all complex data structures. Understanding their memory management is the first step toward writing high-performance code.
