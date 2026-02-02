# 🔍 Binary Search

Binary search is one of the most fundamental and efficient algorithms. It allows finding an element in a **sorted** array in logarithmic time $O(\log N)$, instead of linear time $O(N)$.

## 1. Classical Algorithm

The idea is to maintain an interval `[L, R]` in which the searched element may be located. At each step, we check the middle `mid`.
*   If `arr[mid] == target`, we have found it.
*   If `arr[mid] < target`, we search in the right half `[mid + 1, R]`.
*   If `arr[mid] > target`, we search in the left half `[L, mid - 1]`.

```cpp
int binarySearch(const vector<int>& arr, int target) {
    int l = 0, r = arr.size() - 1;
    while (l <= r) {
        int mid = l + (r - l) / 2; // Protects against overflow with (l+r)
        if (arr[mid] == target) return mid;
        if (arr[mid] < target) l = mid + 1;
        else r = mid - 1;
    }
    return -1; // Not found
}
```

## 2. STL Functions (`lower_bound` / `upper_bound`)

In C++, we rarely write pure binary search unless the condition is specific. The `<algorithm>` library provides:

*   **`lower_bound(begin, end, val)`**: Returns an iterator to the **first** element that is $\ge val$.
*   **`upper_bound(begin, end, val)`**: Returns an iterator to the **first** element that is $> val$.

```cpp
vector<int> v = {10, 20, 20, 20, 30};

// The first 20
auto lb = lower_bound(v.begin(), v.end(), 20); 
cout << (lb - v.begin()); // Index 1

// The first > 20 (i.e., 30)
auto ub = upper_bound(v.begin(), v.end(), 20); 
cout << (ub - v.begin()); // Index 4

// Count of occurrences of 20
cout << (ub - lb); // 3
```

## 3. Binary Search on Answer

This is the most powerful application of the algorithm in competitions. It is used when searching for the "minimum value $X$ for which it is possible to satisfy condition $P(X)$" (or the maximum).
The function $P(X)$ must be monotonic:
*   If it is possible for $X$, then it is also possible for any $Y > X$ (or vice-versa).

**Template:**
```cpp
bool check(long long x) {
    // Is it possible to cope with value x?
    // Greedy check or simulation O(N)
    return true/false;
}

long long solve() {
    long long l = 0, r = 1e18; // Search space
    long long ans = -1;
    
    while (l <= r) {
        long long mid = l + (r - l) / 2;
        if (check(mid)) {
            ans = mid;
            r = mid - 1; // Searching for smaller (for minimum)
            // l = mid + 1; // For maximum
        } else {
            l = mid + 1; // Need larger
            // r = mid - 1; // For maximum
        }
    }
    return ans;
}
```

### Sample tasks:
*   "Aggressive Cows" (SPOJ): The minimum distance between cows should be maximized.
*   "K-th Number": Finding the k-th number in a complex structure.

## 4. Binary Search on Real Numbers

When the answer is a fractional number (`double`), instead of `l <= r`, we use a fixed number of iterations. This is more stable than `while (r - l > EPS)` due to precision issues.

```cpp
double l = 0, r = 1e9;
for (int i = 0; i < 100; i++) { // 100 iterations give huge precision
    double mid = (l + r) / 2;
    if (check(mid)) r = mid;
    else l = mid;
}
```

## 5. Ternary Search

Used for finding the maximum/minimum of a **unimodal** function (a function that increases and then decreases, or vice-versa - it has only one extremum).

We divide the interval into 3 parts with two points $m_1$ and $m_2$:
$m_1 = l + (r-l)/3$
$m_2 = r - (r-l)/3$

*   If $f(m_1) < f(m_2)$: The maximum is to the right, we move $L = m_1$.
*   If $f(m_1) > f(m_2)$: The maximum is to the left, we move $R = m_2$.

## 6. Tasks
1.  **Codeforces 706B**: Interesting drink (`upper_bound`).
2.  **SPOJ EKO**: Classical BS on answer.
3.  **LeetCode 33**: Search in Rotated Sorted Array.

## 🏁 Conclusion
If you see "monotonicity" or the phrase "maximize the minimum", immediately think of Binary Search.

---

## 7. More Examples of Binary Search on Answer

### 7.1. Task: Array Partitioning
**Condition**: You have an array with $N$ numbers. You must divide it into $K$ non-empty continuous parts such that the maximum sum in a single part is **minimal**. Find this minimal value.

**Solution**: Binary search on the answer.
*   $L = \max(arr)$ (at least one number must be in some part).
*   $R = \sum(arr)$ (everything in one part).
*   `check(x)`: Can we divide the array such that no part has a sum $> x$?

```cpp
bool check(vector<int>& arr, int k, long long maxSum) {
    int parts = 1;
    long long currentSum = 0;
    
    for (int num : arr) {
        if (num > maxSum) return false; // Impossible
        if (currentSum + num > maxSum) {
            parts++;
            currentSum = num;
        } else {
            currentSum += num;
        }
    }
    
    return parts <= k;
}

long long solve(vector<int>& arr, int k) {
    long long l = *max_element(arr.begin(), arr.end());
    long long r = accumulate(arr.begin(), arr.end(), 0LL);
    long long ans = r;
    
    while (l <= r) {
        long long mid = l + (r - l) / 2;
        if (check(arr, k, mid)) {
            ans = mid;
            r = mid - 1;
        } else {
            l = mid + 1;
        }
    }
    return ans;
}
```

### 7.2. Task: Copying Books
$N$ books with different thicknesses. $K$ workers must copy them. Each worker takes a continuous block of books. Minimize the maximum time spent by a worker.

The solution is identical to the previous task - binary search on time.

---

## 8. Binary Search on Fractional Numbers - More Examples

### 8.1. Task: Minimum Execution Time
$N$ machines, each performs a task in $t_i$ seconds. We must perform $M$ tasks. What is the minimum time?

**Solution**: Binary search on time.
*   $L = 0$, $R = \max(t_i) \times M$ (one machine does everything).
*   `check(T)`: In time $T$, how many tasks can be finished? $\sum_{i} \lfloor T / t_i \rfloor \ge M$?

```cpp
bool canFinish(vector<int>& times, long long M, long long T) {
    long long totalTasks = 0;
    for (int t : times) {
        totalTasks += T / t;
        if (totalTasks >= M) return true; // Optimization to avoid overflow
    }
    return totalTasks >= M;
}

long long minTime(vector<int>& times, long long M) {
    long long l = 0, r = 1e18;
    long long ans = r;
    
    while (l <= r) {
        long long mid = l + (r - l) / 2;
        if (canFinish(times, M, mid)) {
            ans = mid;
            r = mid - 1;
        } else {
            l = mid + 1;
        }
    }
    return ans;
}
```

---

## 9. Common Errors in Binary Search

### 9.1. Infinite Loop
If you update the boundaries incorrectly (e.g., `l = mid` instead of `l = mid + 1`), the loop may become infinite.

**Solution**: Always use `mid = l + (r - l) / 2` and `l = mid + 1` or `r = mid - 1`.

### 9.2. Overflow in `(l + r) / 2`
For large values, `l + r` may exceed the limit of `int` or even `long long`.

**Solution**: Use `mid = l + (r - l) / 2`.

### 9.3. Incorrect `check` Function
Ensure that the `check` function is indeed monotonic. If it is not, binary search will not work correctly.

---

## 🏁 Summary

Binary search is an extremely powerful technique with wide application. Its main forms are:
*   **Classical search** in a sorted array.
*   **Search on answer** for optimization tasks.
*   **Ternary search** for unimodal functions.

Whenever you see a monotonic function or a problem of the type "find the maximum minimal value", think of binary search!
