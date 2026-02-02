# 🔢 One-Dimensional Arrays: Foundations and Tasks

An array is the simplest data structure that stores a sequence of elements of the same type at contiguous memory addresses.

## 1. Declaration and Access

### 1.1. Static Arrays
The size must be known at compile-time (except for VLA in C99, but it is not recommended in C++).
```cpp
int arr[100]; // Array of 100 integers
arr[0] = 5;   // Indexing starts from 0!
```

### 1.2. Global vs. Local Arrays
*   **Local (within a function)**: Allocated on the **Stack**. Memory is limited (usually a few MB). If you define `int a[1000000]` inside `main`, you will get a "Stack Overflow".
*   **Global (outside functions)**: Allocated in the **Static/Data segment**. They can be very large (up to hundreds of MB). Furthermore, they are automatically initialized with zeros.

```cpp
int bigArr[1000005]; // OK, global

int main() {
    // int huge[1000005]; // ERROR! Stack overflow.
}
```

## 2. Common Errors

### 2.1. Out of Bounds
C++ does not perform a check on whether the index is valid.
```cpp
int a[5];
a[5] = 10; // ERROR! Valid indices are 0, 1, 2, 3, 4.
```
This can lead to changing other variables, infinite loops, or a "Segmentation Fault".

### 2.2. Off-by-one errors
Confusion about whether the loop should be `< N` or `<= N`. For 0-indexed arrays, it is always `< N`.

## 3. Basic Algorithms

### 3.1. Finding Minimum and Maximum
```cpp
int minVal = arr[0];
int maxVal = arr[0];
for (int i = 1; i < n; i++) {
    if (arr[i] < minVal) minVal = arr[i];
    if (arr[i] > maxVal) maxVal = arr[i];
}
// Or std::min_element / std::max_element
```

### 3.2. Sum and Average
```cpp
long long sum = 0; // Use long long to avoid overflow
for (int i = 0; i < n; i++) sum += arr[i];
double avg = (double)sum / n;
```

### 3.3. Reverse
We swap the first with the last, the second with the second-to-last, and so on.
```cpp
for (int i = 0; i < n / 2; i++) {
    swap(arr[i], arr[n - 1 - i]);
}
```

### 3.4. Sortedness Check
```cpp
bool isSorted = true;
for (int i = 0; i < n - 1; i++) {
    if (arr[i] > arr[i+1]) {
        isSorted = false;
        break;
    }
}
```

## 4. `std::vector` - The Modern Array
In C++, it is almost always better to use `std::vector`.
*   Dynamic size.
*   Keeps its size (`v.size()`).
*   Stored in the **Heap**, so it can be large even if it is local.

```cpp
vector<int> v(n); // Vector with n elements (zeros)
v.push_back(5);   // Adding to the end
```

## 5. Two Pointers Technique
A common technique for solving array tasks in linear time.

### 5.1. Removing Duplicates from a Sorted Array
```cpp
int removeDuplicates(vector<int>& arr) {
    if (arr.empty()) return 0;
    int writePos = 1;
    for (int i = 1; i < arr.size(); i++) {
        if (arr[i] != arr[i-1]) {
            arr[writePos++] = arr[i];
        }
    }
    return writePos;
}
```

### 5.2. Finding a Pair with a Given Sum (in a sorted array)
Complexity: $O(N)$ instead of $O(N^2)$.
```cpp
bool findPairWithSum(vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    while (left < right) {
        int sum = arr[left] + arr[right];
        if (sum == target) return true;
        else if (sum < target) left++;
        else right--;
    }
    return false;
}
```

## 6. Prefix Sums
Allow for fast calculation of the sum of elements in an interval $[L, R]$.

### 6.1. Basic Idea
We create an array `prefix`, where `prefix[i]` contains the sum of elements from 0 to i-1.
```cpp
vector<long long> buildPrefix(const vector<int>& arr) {
    int n = arr.size();
    vector<long long> prefix(n + 1, 0);
    for (int i = 0; i < n; i++) {
        prefix[i + 1] = prefix[i] + arr[i];
    }
    return prefix;
}
```

### 6.2. Interval Sum Queries
After we have constructed the prefix array, each query is answered in $O(1)$.
```cpp
// Sum of elements from index L to R (inclusive)
long long rangeSum(const vector<long long>& prefix, int L, int R) {
    return prefix[R + 1] - prefix[L];
}
```

### 6.3. Application: At most K zeros in a subarray
Task: Find the longest subarray of 0s and 1s that contains at most K zeros.
```cpp
int longestSubarrayWithKZeros(vector<int>& arr, int K) {
    int left = 0, zeros = 0, maxLen = 0;
    for (int right = 0; right < arr.size(); right++) {
        if (arr[right] == 0) zeros++;
        while (zeros > K) {
            if (arr[left] == 0) zeros--;
            left++;
        }
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

## 7. Difference Array
Allows for fast interval updates.

### 7.1. Idea
If we want to add `x` to all elements in the interval `[L, R]`, we use a difference array.
```cpp
vector<int> diff(n + 1, 0);
// Adding x to the interval [L, R]
diff[L] += x;
diff[R + 1] -= x;
// Recovering the original array
for (int i = 1; i < n; i++) {
    diff[i] += diff[i - 1];
}
```
This allows for $O(1)$ interval updates and $O(N)$ recovery.

## 8. Cyclic Rotation of an Array
Shifting elements left or right by K positions.

### 8.1. Rotating Right
```cpp
void rotateRight(vector<int>& arr, int K) {
    int n = arr.size();
    K %= n; // In case K > n
    reverse(arr.begin(), arr.end());
    reverse(arr.begin(), arr.begin() + K);
    reverse(arr.begin() + K, arr.end());
}
```

### 8.2. Rotating Left
```cpp
void rotateLeft(vector<int>& arr, int K) {
    rotateRight(arr, arr.size() - K);
}
```

## 9. Kadane's Algorithm - Maximum Subarray Sum
Classical task: Find the subarray with the maximum sum.
```cpp
long long maxSubarraySum(const vector<int>& arr) {
    long long maxSum = arr[0], currentSum = arr[0];
    for (int i = 1; i < arr.size(); i++) {
        currentSum = max((long long)arr[i], currentSum + arr[i]);
        maxSum = max(maxSum, currentSum);
    }
    return maxSum;
}
```

## 10. Frequency Arrays
Used for fast counting of element occurrences.

### 10.1. Example
```cpp
const int MAXVAL = 1000005;
int freq[MAXVAL];
memset(freq, 0, sizeof(freq));

// Counting
for (int i = 0; i < n; i++) {
    freq[arr[i]]++;
}

// Finding the most frequent element
int maxFreq = 0, mostFrequent = arr[0];
for (int i = 0; i < MAXVAL; i++) {
    if (freq[i] > maxFreq) {
        maxFreq = freq[i];
        mostFrequent = i;
    }
}
```

## 11. Practice Tasks
1.  **Reverse an Array**: Read an array and output it in reverse order.
2.  **Second Largest**: Find the second largest element in $O(N)$.
3.  **Removing Duplicates**: From a sorted array using the two pointers technique.
4.  **Cyclic Rotation**: Shifting an array left/right by K positions.
5.  **Prefix Sums**: Task with many interval sum queries.
6.  **Pair with the Closest Sum**: In a sorted array, find a pair of numbers with a sum closest to a given number.
7.  **Kadane**: Find the maximum sum of a subarray.
8.  **Longest Subarray**: With at most K different numbers.

## 🏁 Conclusion
Arrays are the foundation of programming and algorithms. Ensure you fully understand indexing (starts at 0!), array boundaries, and basic techniques like prefix sums, two pointers, and Kadane's algorithm. Off-by-one errors are the most common cause of incorrect solutions in competitions. Practice with numerous tasks to develop intuition and speed when working with arrays.