# 🔍 Introduction to Sorting Algorithms

Sorting is the process of arranging data in a specific order (ascending or descending). It is one of the most fundamental operations in computer science.

## 1. Simple O(N²) Sorting Algorithms

### 🟢 Bubble Sort
Repeatedly steps through the list, compares adjacent elements and swaps them if they are in the wrong order.
- **Complexity**: $O(N^2)$
- **Stability**: Yes
```cpp
void bubbleSort(vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) swap(arr[j], arr[j + 1]);
        }
    }
}
```

### 🔵 Selection Sort
Divides the input list into two parts: a sorted and an unsorted part. It repeatedly selects the smallest element from the unsorted part and moves it to the end of the sorted part.
- **Complexity**: $O(N^2)$
- **Stability**: Usually No
```cpp
void selectionSort(vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i < n - 1; i++) {
        int min_idx = i;
        for (int j = i + 1; j < n; j++)
            if (arr[j] < arr[min_idx]) min_idx = j;
        swap(arr[i], arr[min_idx]);
    }
}
```

## 2. Advanced O(N log N) Sorting

### ⚡ Quick Sort
Uses a "pivot" element to partition the array into two sub-arrays. (Detailed in Topic 18).

### 🥞 Merge Sort
Recursively divides the array into halves, sorts them, and merges them back together. (Detailed in Topic 18).

---

## 📝 String Manipulation and Searching

Strings in C++ can be handled via `char[]` (C-style) or `std::string` (Modern).

### 🔍 Searching in Strings
- `str.find(sub)`: Returns the first index of the substring or `std::string::npos`.
- `str.rfind(sub)`: Searches from the end.

### 🛠️ Common Operations
- **Substring**: `str.substr(pos, len)`
- **Transformation**: `std::stoi(s)` (string to int), `std::to_string(n)`
- **Comparison**: `s1 == s2`, `s1 < s2` (lexicographical)

## 🏁 Conclusion
For small arrays, simple sorts are easy to code. For larger data, always use `std::sort` from STL which is optimized for speed and reliability.
