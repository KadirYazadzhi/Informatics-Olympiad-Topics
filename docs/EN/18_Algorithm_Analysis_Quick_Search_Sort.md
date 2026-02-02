# 📊 Algorithm Analysis. Quick Search and Quick Sort

## ⚖️ Deep Analysis of Algorithms

In competitive programming, it is not enough to just write working code. It must work within the time limit (usually 1 second). Modern judges execute about $10^8$ operations per second.

### 🕒 Time Complexity and Constraints

Knowing the constraints on input data ($N$) often hints at the required complexity of the solution:

| Constraint for $N$ | Permissible Complexity | Typical Algorithms |
| :--- | :--- | :--- |
| $N \le 10$ | $O(N!)$ | Exhaustive search (Backtracking), Permutations |
| $N \le 20$ | $O(2^N)$ | DP with bitmasks, Recursion |
| $N \le 500$ | $O(N^3)$ | Floyd-Warshall, Matrix Multiplication, DP |
| $N \le 2000$ | $O(N^2)$ | Bubble/Insertion Sort, DP, BFS/DFS from every vertex |
| $N \le 100,000$ | $O(N \log N)$ | Sorting, Heap, Segment Tree, Binary Search |
| $N \le 1,000,000$ | $O(N)$ or $O(N \log N)$ | KMP, Hash, Two Pointers, Union-Find |
| $N \ge 10^{18}$ | $O(\log N)$ or $O(1)$ | Matrix Exponentiation, Mathematical Formulas |

### 📜 Master Theorem (For Recursive Algorithms)
For a recurrence relation of the form $T(n) = a T(n/b) + f(n)$:
1. If $f(n) = O(n^c)$ and $c < \log_b a$, then $T(n) = \Theta(n^{\log_b a})$.
2. If $f(n) = \Theta(n^c \log^k n)$ and $c = \log_b a$, then $T(n) = \Theta(n^c \log^{k+1} n)$.
3. If $f(n) = \Omega(n^c)$ and $c > \log_b a$, then $T(n) = \Theta(f(n))$.

---

## 🔍 Quick Search (Binary Search) - Advanced

Binary search is not just for finding a number in an array. It is a powerful method for finding the answer to a task if the answer is monotonic.

### 1. Standard Binary Search (`std::lower_bound`)
Finds the first element that is $\ge$ to the searched one.
```cpp
int lowerBound(const std::vector<int>& arr, int target) {
    int left = 0, right = arr.size(); // Be careful with the boundaries [0, N)
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] >= target) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }
    return left; // Returns index or N if none exists
}
```

### 2. Binary Search on Answer
Used when searching for the minimum value $X$ for which condition $P(X)$ is satisfied (and for every $Y > X$, $P(Y)$ is also satisfied).

**Example**: "What is the minimum time in which $K$ machines can produce $M$ products?"
- The `check(time)` function checks whether in `time` seconds $\ge M$ products can be produced. It is monotonic (more time -> more products).

```cpp
bool check(long long time, int m, const std::vector<int>& machines) {
    long long products = 0;
    for (int speed : machines) {
        products += time / speed;
        if (products >= m) return true;
    }
    return products >= m;
}

long long solve(int m, std::vector<int>& machines) {
    long long left = 0, right = 1e18; // Large enough upper bound
    long long ans = right;

    while (left <= right) {
        long long mid = left + (right - left) / 2;
        if (check(mid, m, machines)) {
            ans = mid;
            right = mid - 1; // Try less time
        } else {
            left = mid + 1; // We need more time
        }
    }
    return ans;
}
```

---

## ⚡ Sorting Algorithms in Detail

### 1. Quick Sort - Optimizations
Classical Quick Sort can degenerate to $O(N^2)$ on a sorted array if we always choose the first/last element as the Pivot.

**Optimization - Random Pivot**:
Choosing a random index for the Pivot makes the probability of a bad case astronomically small.

```cpp
#include <cstdlib> // for rand()

void quickSort(std::vector<int>& arr, int low, int high) {
    if (low >= high) return;

    // Choice of a random pivot
    int pivotIndex = low + rand() % (high - low + 1);
    int pivot = arr[pivotIndex];
    std::swap(arr[pivotIndex], arr[high]); // Move pivot to the very end

    int i = low - 1;
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            i++;
            std::swap(arr[i], arr[j]);
        }
    }
    std::swap(arr[i + 1], arr[high]);
    
    quickSort(arr, low, i);
    quickSort(arr, i + 2, high);
}
```

### 2. Merge Sort
Unlike Quick Sort, Merge Sort **always** works in $O(N \log N)$ and is **stable** (preserves the order of equal elements). It is often used for counting inversions.

```cpp
void merge(std::vector<int>& arr, int left, int mid, int right) {
    std::vector<int> temp;
    int i = left, j = mid + 1;

    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) temp.push_back(arr[i++]);
        else temp.push_back(arr[j++]);
    }
    while (i <= mid) temp.push_back(arr[i++]);
    while (j <= right) temp.push_back(arr[j++]);

    for (int k = 0; k < temp.size(); k++) arr[left + k] = temp[k];
}

void mergeSort(std::vector<int>& arr, int left, int right) {
    if (left >= right) return;
    int mid = left + (right - left) / 2;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
}
```

### 3. C++ `std::sort`
The built-in `std::sort` function (in `<algorithm>`) uses a hybrid algorithm (**Introsort**):
1. Starts with Quick Sort.
2. If the recursion depth becomes too large, it switches to Heap Sort (to guarantee $O(N \log N)$).
3. For small subarrays, it uses Insertion Sort (because it is faster for small $N$).

---

## 🏁 Conclusion and Tips

1. Always use `std::sort` unless you have a specific reason (e.g., counting inversions with Merge Sort).
2. For tasks searching for "the minimum X for which ... is satisfied", always think of **Binary Search on Answer**.
3. Be careful with data types (`long long`) in binary search to avoid overflow at `left + right`.