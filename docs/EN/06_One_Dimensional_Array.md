# 🧮 One-Dimensional Array in C++

A one-dimensional array is a fundamental data structure used to store multiple values of the same type. It is a continuous sequence of elements that can be accessed via indices.


## 📝 Definition and Declaration of a One-Dimensional Array

A one-dimensional array is declared using the following syntax:
```cpp
type array_name[size];
```

Example:
```cpp
int numbers[5]; // Array of 5 integers
```

### Initialization:

Arrays can be initialized during declaration.
```cpp
int numbers[5] = {10, 20, 30, 40, 50}; // Initialization with specific values
```

If the number of elements is omitted, the compiler calculates it automatically:
```cpp
int numbers[] = {10, 20, 30, 40, 50}; // The size will be 5
```

## 📦 Accessing Array Elements

Array elements are accessed via an index, starting from 0.

Example:
```cpp
#include <iostream>
using namespace std;

int main() {
    int numbers[5] = {10, 20, 30, 40, 50};
    cout << "First element: " << numbers[0] << endl;
    cout << "Third element: " << numbers[2] << endl;

    return 0;
}
```

## 🔄 Assigning and Modifying Values

Values in the array can be changed using the index:
```cpp
numbers[0] = 100; // Changing the first element
numbers[4] = 500; // Changing the last element
```

## 📚 Basic Operations with a One-Dimensional Array

1. **Iterating over the Array**

Arrays can be traversed using a loop.

Example:
```cpp
#include <iostream>
using namespace std;

int main() {
    int numbers[5] = {10, 20, 30, 40, 50};

    for (int i = 0; i < 5; i++) {
        cout << "Element " << i << ": " << numbers[i] << endl;
    }

    return 0;
}
```

2. **Finding Maximum and Minimum Value**
```cpp
#include <iostream>
using namespace std;

int main() {
    int numbers[5] = {10, 20, 5, 40, 15};
    int max = numbers[0];
    int min = numbers[0];

    for (int i = 1; i < 5; i++) {
        if (numbers[i] > max) max = numbers[i];
        if (numbers[i] < min) min = numbers[i];
    }

    cout << "Maximum: " << max << endl;
    cout << "Minimum: " << min << endl;

    return 0;
}
```
3. **Summing Elements**
```cpp
#include <iostream>
using namespace std;

int main() {
    int numbers[5] = {10, 20, 30, 40, 50};
    int sum = 0;

    for (int i = 0; i < 5; i++) {
        sum += numbers[i];
    }

    cout << "Sum of elements: " << sum << endl;

    return 0;
}
```

## 🧪 Common Errors and Solutions

1. **Out of Bounds**

Accessing an index outside valid boundaries leads to unexpected behavior.
```cpp
int numbers[5];
cout << numbers[5]; // Error: Index is out of bounds
```

> **Solution**: Ensure that the index is in the range [0, size - 1].

## 🚀 Applications of a One-Dimensional Array

- **Storing data**: For example, test results, temperatures over a specific period, etc.
- **Processing sequences**: For example, calculating an average value, filtering, etc.
- **Programming algorithms**: For example, sorting, searching, and other basic operations.

## 🔍 Searching in an Array

### Linear Search
The simplest method - we check each element sequentially:
```cpp
int linearSearch(int arr[], int n, int target) {
    for (int i = 0; i < n; i++) {
        if (arr[i] == target) {
            return i; // Returns the index of the found element
        }
    }
    return -1; // Element not found
}
```

**Complexity**: O(n) - in the worst case, we check all elements.

### Binary Search
Works only on a **sorted** array. Much faster than linear search:
```cpp
int binarySearch(int arr[], int n, int target) {
    int left = 0, right = n - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2; // Avoiding overflow
        
        if (arr[mid] == target) {
            return mid;
        }
        if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}
```

**Complexity**: O(log n) - each step reduces the search area by half.

## 🔄 Sorting an Array

### Bubble Sort
A simple but slow algorithm. Suitable for small arrays:
```cpp
void bubbleSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                swap(arr[j], arr[j + 1]);
            }
        }
    }
}
```

**Complexity**: O(n²)

### Selection Sort
We find the minimum element and place it in the correct position:
```cpp
void selectionSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        swap(arr[i], arr[minIndex]);
    }
}
```

**Complexity**: O(n²)

### Using STL sort
In real tasks, use the built-in `std::sort` function:
```cpp
#include <algorithm>

int arr[100];
sort(arr, arr + n); // Sort in ascending order
sort(arr, arr + n, greater<int>()); // Descending order
```

**Complexity**: O(n log n) - much faster!

## 📊 Algorithms for Processing Arrays

### Array Rotation
Moves all elements to the left or right:
```cpp
void rotateLeft(int arr[], int n, int k) {
    k = k % n; // For cases where k > n
    reverse(arr, arr + k);
    reverse(arr + k, arr + n);
    reverse(arr, arr + n);
}
```

### Finding the Second Largest Element
```cpp
int secondLargest(int arr[], int n) {
    int first = INT_MIN, second = INT_MIN;
    
    for (int i = 0; i < n; i++) {
        if (arr[i] > first) {
            second = first;
            first = arr[i];
        } else if (arr[i] > second && arr[i] != first) {
            second = arr[i];
        }
    }
    
    return second;
}
```

### Removing Duplicates from a Sorted Array
```cpp
int removeDuplicates(int arr[], int n) {
    if (n == 0) return 0;
    
    int j = 0; // Index for unique elements
    for (int i = 1; i < n; i++) {
        if (arr[i] != arr[j]) {
            j++;
            arr[j] = arr[i];
        }
    }
    return j + 1; // New size of the array
}
```

### Merging Two Sorted Arrays
```cpp
void merge(int arr1[], int n1, int arr2[], int n2, int result[]) {
    int i = 0, j = 0, k = 0;
    
    while (i < n1 && j < n2) {
        if (arr1[i] <= arr2[j]) {
            result[k++] = arr1[i++];
        } else {
            result[k++] = arr2[j++];
        }
    }
    
    // Copying the remaining elements
    while (i < n1) result[k++] = arr1[i++];
    while (j < n2) result[k++] = arr2[j++];
}
```

## 🎯 Classical Tasks

### Task 1: Kadane's Algorithm (Maximum Subarray Sum)
Finding the maximum sum of consecutive elements:
```cpp
int maxSubarraySum(int arr[], int n) {
    int maxSoFar = arr[0];
    int currentMax = arr[0];
    
    for (int i = 1; i < n; i++) {
        currentMax = max(arr[i], currentMax + arr[i]);
        maxSoFar = max(maxSoFar, currentMax);
    }
    
    return maxSoFar;
}
```

### Task 2: Two Sum (Two numbers with a given sum)
```cpp
bool twoSum(int arr[], int n, int target) {
    sort(arr, arr + n);
    int left = 0, right = n - 1;
    
    while (left < right) {
        int sum = arr[left] + arr[right];
        if (sum == target) {
            return true;
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    return false;
}
```

### Task 3: Majority Element (Element occurring more than n/2 times)
```cpp
int findMajority(int arr[], int n) {
    int count = 0, candidate = -1;
    
    // Boyer-Moore Voting Algorithm
    for (int i = 0; i < n; i++) {
        if (count == 0) {
            candidate = arr[i];
            count = 1;
        } else if (arr[i] == candidate) {
            count++;
        } else {
            count--;
        }
    }
    
    // Check if the candidate is indeed the majority
    count = 0;
    for (int i = 0; i < n; i++) {
        if (arr[i] == candidate) count++;
    }
    
    return (count > n / 2) ? candidate : -1;
}
```

## 🧮 Working with std::array (C++11)

A modern way to work with fixed-size arrays:
```cpp
#include <array>

std::array<int, 5> arr = {1, 2, 3, 4, 5};

// Advantages:
arr.size();      // Always know the size
arr.at(2);       // Boundary checking
arr.front();     // Access first element
arr.back();      // Access last element
arr.fill(0);     // Fill with 0
```

## 💡 Efficiency Tips

1. **Use vector instead of array**: More flexible and safer.
2. **Avoid copying**: Pass arrays via pointers or references.
3. **Pre-allocation**: If you know the size, pre-allocate memory.
4. **Cache locality**: Linear access is faster than random access.

## 🏁 Conclusion

The one-dimensional array is a fundamental structure that lies at the core of almost all algorithms. Mastering basic operations (searching, sorting, processing) is critical for success in competitive programming. Practice with different tasks, experiment with algorithms, and gradually you will develop intuition for effectively using arrays! 🎯