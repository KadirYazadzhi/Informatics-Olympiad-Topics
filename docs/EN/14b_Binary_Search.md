# 🛠️ Binary Search

Binary search is one of the most efficient searching methods, reducing complexity from $O(N)$ to $O(\log N)$.

## 📐 Application Condition
Data must be **sorted** (monotonic).

## ⚡ Iterative Implementation
```cpp
int binarySearch(vector<int>& a, int target) {
    int left = 0, right = a.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (a[mid] == target) return mid;
        if (a[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

## 🚀 Binary Search on Answer
Often in tasks we don't look for a number in an array, but look for **the smallest value $X$ for which condition $P(X)$ is met**.
- If $P(X)$ is true for $X$, it is true for all values $> X$.
- This allows "searching" the answer in a range.

## 🏁 Conclusion
Binary search is fundamental. Watch out for "off-by-one" errors with boundaries (`left`, `right`, `mid`) and always test with 1 or 2 element arrays.
