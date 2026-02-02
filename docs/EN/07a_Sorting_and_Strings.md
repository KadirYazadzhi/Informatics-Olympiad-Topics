# 🔍 Sorting Algorithms: A Complete Guide

Sorting is a fundamental operation in computer science that arranges elements of a list in a specific order (most commonly ascending or descending). Choosing the right algorithm depends on the data size ($N$), memory constraints, and the specific characteristics of the elements.

## 1. Simple Algorithms: $O(N^2)$
These algorithms are easy to implement but inefficient for large arrays ($N > 2000$).

### 1.1. Bubble Sort
Repeatedly traverses the array, swapping adjacent elements if they are in the wrong order. The heaviest element "floats" like a bubble to the end.
```cpp
void bubbleSort(vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i < n - 1; i++) {
        bool swapped = false;
        for (int j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                swap(arr[j], arr[j + 1]);
                swapped = true;
            }
        }
        if (!swapped) break; // Optimization
    }
}
```

### 1.2. Insertion Sort
Builds the sorted array one element at a time. It takes the next element and "inserts" it in the correct place among those already sorted.
*   **Advantage**: Extremely fast ($O(N)$) for **nearly sorted** data.
*   **Usage**: Often used as a base case in more complex algorithms (e.g., Timsort, which is the basis for sorting in Python and Java, uses Insertion Sort for small chunks).

---

## 2. Efficient Algorithms: $O(N \log N)$
Mandatory for competitions where $N$ reaches $10^5$ or $10^6$.

### 2.1. Merge Sort
A classical example of the **"Divide and Conquer"** strategy.
1.  **Divide**: The array is divided into two halves.
2.  **Conquer**: Each half is sorted recursively.
3.  **Merge**: The two sorted halves are united (merged) into a single sorted array.

*   **Stability**: Yes (preserves the order of equal elements).
*   **Memory**: Requires $O(N)$ additional memory.

```cpp
void merge(vector<int>& arr, int l, int m, int r) {
    vector<int> temp;
    int i = l, j = m + 1;
    while (i <= m && j <= r) {
        if (arr[i] <= arr[j]) temp.push_back(arr[i++]);
        else temp.push_back(arr[j++]);
    }
    while (i <= m) temp.push_back(arr[i++]);
    while (j <= r) temp.push_back(arr[j++]);
    for (int k = 0; k < temp.size(); k++) arr[l + k] = temp[k];
}

void mergeSort(vector<int>& arr, int l, int r) {
    if (l < r) {
        int m = l + (r - l) / 2;
        mergeSort(arr, l, m);
        mergeSort(arr, m + 1, r);
        merge(arr, l, m, r);
    }
}
```

### 2.2. Quick Sort
Also uses "Divide and Conquer", but the division occurs via a **Pivot** (reference element). All elements smaller than the pivot go to the left, and those larger go to the right.
*   **Speed**: In practice, it is faster than Merge Sort because it works "in-place" (without additional memory) and has good cache locality.
*   **Worst case**: $O(N^2)$ if the pivot is poorly chosen (e.g., a sorted array).
*   `std::sort` in C++ uses a hybrid variant (Introsort: QuickSort + HeapSort) to guarantee $O(N \log N)$.

---

## 3. Linear Sorts: $O(N)$
Possible only if we know something more about the data (e.g., that they are integers in a small range).

### 3.1. Counting Sort
If the numbers are in the interval $[0, K]$:
1.  Create an array `cnt[K+1]` with zeros.
2.  For each number `x` from the input, increment `cnt[x]`.
3.  Output index `i` exactly `cnt[i]` times.
Complexity: $O(N + K)$.

### 3.2. Radix Sort
Sorts numbers digit by digit (usually from the last to the first), using a stable Counting Sort for each position.
Complexity: $O(D \cdot (N+K))$, where $D$ is the number of digits.

---

## 4. Using `std::sort` in C++

In 99% of cases in competitions, you should use the built-in `sort`.

```cpp
#include <algorithm>
#include <vector>
#include <iostream>

using namespace std;

struct Student {
    string name;
    int score;
};

// Comparator function
bool compareStudents(const Student& a, const Student& b) {
    if (a.score != b.score)
        return a.score > b.score; // Descending by points
    return a.name < b.name;       // Ascending by name for equal points
}

int main() {
    vector<int> v = {5, 1, 4, 2, 8};
    
    // Standard sorting (ascending)
    sort(v.begin(), v.end()); 
    
    // Descending with a lambda function
    sort(v.begin(), v.end(), [](int a, int b) {
        return a > b; 
    });

    vector<Student> students = {{"Ivan", 90}, {"Ani", 95}, {"Bobi", 90}};
    sort(students.begin(), students.end(), compareStudents);
    
    return 0;
}
```

### `std::stable_sort`
If you want to preserve the order of elements that are equal according to the comparison criterion, use `stable_sort`. It is usually based on Merge Sort and is slightly slower, but sometimes it is necessary.

---

## 5. Practical Tips and Common Errors

### 5.1. Problems with the Comparator Function
One of the most frequent errors when using `sort` is writing an invalid comparison function. It must:
*   Be **transitive**: If $A < B$ and $B < C$, then $A < C$.
*   Be **antisymmetric**: If $A < B$, then NOT $B < A$.
*   If $A$ is NOT less than $B$ and $B$ is NOT less than $A$, then they are equal.

A faulty function can lead to a `Segmentation Fault` or unpredictable results.

### 5.2. Sorting Structures with `operator<`
Instead of passing a function, you can define `operator<` for your structure:
```cpp
struct Point {
    int x, y;
    bool operator<(const Point& other) const {
        if (x != other.x) return x < other.x;
        return y < other.y;
    }
};

vector<Point> points = {{3,5}, {1,2}, {3,1}};
sort(points.begin(), points.end()); // Already works directly
```

### 5.3. Sorting Part of an Array
You can sort only a specific section:
```cpp
vector<int> arr = {5, 2, 9, 1, 5, 6};
sort(arr.begin() + 1, arr.begin() + 4); // Sorts indices [1, 4)
// Result: {5, 1, 2, 9, 5, 6}
```

### 5.4. Partial Sorting (`partial_sort`)
If you need only the first $K$ smallest elements, `partial_sort` is faster than full sorting:
```cpp
vector<int> v = {5, 1, 9, 3, 7, 2};
partial_sort(v.begin(), v.begin() + 3, v.end());
// The first 3 are sorted: {1, 2, 3, ...}
```
Complexity: $O(N \log K)$ instead of $O(N \log N)$.

---

## 6. Practice Tasks

1. **CSES Distinct Numbers**: Use sorting to find unique elements.
2. **Codeforces 339D**: Requires sorting with a custom function.
3. **AtCoder ABC128C**: Combination of sorting and structures.

---

## 🏁 Conclusion

Choosing the right algorithm depends on the context:
*   **$N \le 2000$**: Bubble Sort or Insertion Sort are sufficient (if time permits).
*   **$N \le 10^6$**: Use `std::sort` (Introsort).
*   **Stability is critical**: `std::stable_sort`.
*   **Specific constraints**: Counting Sort or Radix Sort.

Remember: in 99% of cases in competitions, just write `sort(arr.begin(), arr.end())` – do not reinvent the wheel!
