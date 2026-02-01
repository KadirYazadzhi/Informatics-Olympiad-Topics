# 🔍 Binary Search

Binary Search is a fundamental algorithm for finding an element in a **sorted** array in $O(\log N)$ time.

## 1. Classical Algorithm

Maintains a range `[L, R]`.
*   If `arr[mid] == target`, found.
*   If `arr[mid] < target`, go right `L = mid + 1`.
*   If `arr[mid] > target`, go left `R = mid - 1`.

```cpp
int binarySearch(const vector<int>& arr, int target) {
    int l = 0, r = arr.size() - 1;
    while (l <= r) {
        int mid = l + (r - l) / 2;
        if (arr[mid] == target) return mid;
        if (arr[mid] < target) l = mid + 1;
        else r = mid - 1;
    }
    return -1;
}
```

## 2. STL Functions

*   **`lower_bound`**: First element $\ge val$.
*   **`upper_bound`**: First element $> val$.

```cpp
vector<int> v = {10, 20, 20, 20, 30};
auto lb = lower_bound(v.begin(), v.end(), 20); // Index 1
auto ub = upper_bound(v.begin(), v.end(), 20); // Index 4
// Count occurrences: ub - lb = 3
```

## 3. Binary Search on Answer

Used to find the optimal value $X$ for which a predicate $P(X)$ is true. $P(X)$ must be monotonic.

**Template:**
```cpp
bool check(long long x) {
    // Can we achieve the goal with value x?
    return true; 
}

long long solve() {
    long long l = 0, r = 1e18;
    long long ans = -1;
    
    while (l <= r) {
        long long mid = l + (r - l) / 2;
        if (check(mid)) {
            ans = mid;
            r = mid - 1; // Try smaller (for min)
        } else {
            l = mid + 1;
        }
    }
    return ans;
}
```

## 4. Real Numbers

For `double`, use a fixed number of iterations to avoid precision issues.

```cpp
for (int i = 0; i < 100; i++) {
    double mid = (l + r) / 2;
    if (check(mid)) r = mid;
    else l = mid;
}
```

## 5. Ternary Search

Finds the maximum/minimum of a **unimodal** function.
Divides range into 3 parts using $m_1$ and $m_2$.

*   If $f(m_1) < f(m_2)$: Max is to the right, $L = m_1$.
*   If $f(m_1) > f(m_2)$: Max is to the left, $R = m_2$.

## 6. Practice Problems
1.  **SPOJ EKO**: Lumberjack Mirko (BS on Answer).
2.  **Codeforces 492B**: Vanya and Lanterns (BS on Real Numbers).
3.  **LeetCode 33**: Search in Rotated Sorted Array.

## 7. Conclusion
Binary search turns $O(N)$ into $O(\log N)$. It is essential for "minimize the maximum" or "maximize the minimum" type problems.