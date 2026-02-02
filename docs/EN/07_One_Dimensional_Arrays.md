# 🧮 One-dimensional Arrays and Basic Tasks

One-dimensional arrays are one of the most important data structures used for storing and processing multiple values of the same type. In this topic, we will examine in detail what they represent, how they are used, and how to solve basic tasks with them.

## 📚 One-dimensional Arrays

### What is a One-dimensional Array?

A one-dimensional array is a continuous sequence of elements that can be accessed via an index. Indices start from 0 and increment by 1 up to the array size minus one.

### Declaration and Initialization of Arrays

**Declaration:**
```cpp
type array_name[size];
```

Examples:
```cpp
int numbers[5]; // Array of 5 integers
float values[10]; // Array of 10 floating point numbers
```

Initialization:
```cpp
int numbers[5] = {1, 2, 3, 4, 5};
float values[] = {3.14, 2.71, 1.41};
```

## 🔄 Basic Tasks with One-dimensional Arrays

1. Summing Elements
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int numbers[5] = {1, 2, 3, 4, 5};
        int sum = 0;
    
        for (int i = 0; i < 5; i++) {
            sum += numbers[i];
        }
    
        cout << "Sum: " << sum << endl;
        return 0;
    }
    ```

2. Finding Maximum and Minimum Element
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int numbers[5] = {3, 7, 1, 9, 4};
        int max = numbers[0];
        int min = numbers[0];
    
        for (int i = 1; i < 5; i++) {
            if (numbers[i] > max) max = numbers[i];
            if (numbers[i] < min) min = numbers[i];
        }
    
        cout << "Maximum: " << max << ", Minimum: " << min << endl;
        return 0;
    }
    ```

3. Sorting an Array

Sorting is the process of arranging array elements in ascending or descending order.

## 🔍 Introduction to Sorting Algorithms

Sorting algorithms play a key role in data processing. The most commonly used methods are:

1. Bubble Sort
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int numbers[5] = {5, 3, 8, 6, 2};
    
        for (int i = 0; i < 5 - 1; i++) {
            for (int j = 0; j < 5 - i - 1; j++) {
                if (numbers[j] > numbers[j + 1]) {
                    swap(numbers[j], numbers[j + 1]);
                }
            }
        }
    
        for (int i = 0; i < 5; i++) {
            cout << numbers[i] << " ";
        }
        return 0;
    }
    ```

2. Selection Sort
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int numbers[5] = {5, 3, 8, 6, 2};
    
        for (int i = 0; i < 5 - 1; i++) {
            int minIndex = i;
            for (int j = i + 1; j < 5; j++) {
                if (numbers[j] < numbers[minIndex]) {
                    minIndex = j;
                }
            }
            swap(numbers[i], numbers[minIndex]);
        }
    
        for (int i = 0; i < 5; i++) {
            cout << numbers[i] << " ";
        }
        return 0;
    }
    ```

## 📝 String Manipulation Tools and Searching within Strings

What is a string?
A string is a sequence of characters processed as a single text value. In C++, strings can be processed via character arrays or via the `string` class.

### Basic String Operations

Example of basic operations:
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string text = "Hello, world!";
    cout << "String length: " << text.length() << endl;
    cout << "First character: " << text[0] << endl;
    cout << "Last character: " << text[text.length() - 1] << endl;
    return 0;
}
```

### Searching in Strings

Searching for a substring:
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string text = "Hello, world!";
    string search = "world";

    size_t found = text.find(search);
    if (found != string::npos) {
        cout << "Found at position: " << found << endl;
    } else {
        cout << "Substring not found." << endl;
    }

    return 0;
}
```

### String Comparison
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str1 = "apple";
    string str2 = "banana";

    if (str1 < str2) {
        cout << str1 << " is before " << str2 << " in lexicographical order." << endl;
    } else {
        cout << str2 << " is before " << str1 << " in lexicographical order." << endl;
    }

    return 0;
}
```

## 🎯 Conclusion

One-dimensional arrays, sorting algorithms, and string operations are foundational concepts in programming. They provide powerful tools for working with data, searching, and sorting, which are at the core of many algorithms and applications.
# 🔢 One-Dimensional Arrays: Foundations and Techniques

The one-dimensional array is the most basic yet versatile data structure in computer science. It stores elements of the same type in contiguous memory locations, allowing for $O(1)$ random access.

---

## 1. Memory and Performance

### 1.1. Contiguity
Because elements are side-by-side, arrays have excellent **CPU cache locality**. Iterating through an array is one of the fastest operations a processor can perform.

### 1.2. Static vs. Dynamic Allocation
*   **Static (`int a[100]`)**: Size is fixed at compile-time. Usually stored on the **Stack**.
*   **Dynamic (`vector<int> a(n)`)**: Size determined at runtime. Stored on the **Heap**.

**Important**: Local static arrays in C++ are limited by the stack size (typically 1MB - 8MB). For arrays larger than $10^5$ elements, always declare them **globally** or use `std::vector`.

---

## 2. Essential Techniques

### 2.1. Prefix Sums
To answer "sum of range $[L, R]$" queries in $O(1)$.
1.  Compute $P[i] = A[0] + \dots + A[i]$.
2.  $	ext{Sum}(L, R) = P[R] - P[L-1]$.

```cpp
vector<long long> pref(n + 1, 0);
for (int i = 0; i < n; i++) pref[i+1] = pref[i] + A[i];
```

### 2.2. Difference Arrays
To perform "add $X$ to range $[L, R]$" updates in $O(1)$.
1.  Let $D[i] = A[i] - A[i-1]$.
2.  Range update $[L, R] + X \implies D[L] += X, D[R+1] -= X$.
3.  Reconstruct $A$ using prefix sums of $D$.

### 2.3. Sliding Window
Used to find optimal subarrays (e.g., "max sum subarray of fixed length $K$").
Instead of recomputing the sum, subtract the element leaving the window and add the one entering.

---

## 3. Common Array Tasks

### 3.1. Frequency Array
If values are small integers, use their values as indices to count occurrences.
```cpp
int freq[1001] = {0};
for (int x : A) freq[x]++;
```

### 3.2. Two Pointers
Used on **sorted** arrays to find pairs satisfying a condition ($O(N)$ instead of $O(N^2)$).
```cpp
int i = 0, j = n - 1;
while (i < j) {
    if (A[i] + A[j] == target) return true;
    if (A[i] + A[j] < target) i++;
    else j--;
}
```

---

## 4. Safety and Best Practices

1.  **Bounds Checking**: Accessing `A[n]` is **Undefined Behavior**. It might crash (Segfault) or silently corrupt other data.
2.  **Initialization**: Global arrays are initialized to 0. Local static arrays contain "garbage" (random values). Always initialize: `int a[100] = {0};`.
3.  **Use `long long`**: When summing large array elements, `int` will overflow ($2^{31}-1 \approx 2 \cdot 10^9$).

---

## 5. Practice Problems
1.  **CSES Static Range Sum Queries**: Prefix Sums.
2.  **CSES Maximum Subarray Sum**: Kadane's Algorithm.
3.  **Codeforces 279B**: Books (Sliding Window).
4.  **UVa 10038**: Jolly Jumpers.

## 6. Conclusion
The array is the starting point for almost all competitive programming algorithms. Mastering prefix sums, two pointers, and sliding windows will solve a surprisingly large percentage of introductory problems.
