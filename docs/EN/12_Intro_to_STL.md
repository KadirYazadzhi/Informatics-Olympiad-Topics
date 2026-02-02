# 📚 Introduction to the Standard Template Library (STL) and Tools for Sorting and Searching in STL

The Standard Template Library (STL) is a fundamental tool in C++ programming. It provides ready-made implementations of algorithms, containers, and iterators that facilitate application development. One of the key aspects of STL is the availability of powerful tools for sorting and searching.


## 📋 Table of Contents
1. **What is STL?**  
    - Containers  
    - Iterators  
    - Algorithms  
2. **Sorting in STL**  
    - The `std::sort` function  
    - Custom sorting  
3. **Searching in STL**  
    - Linear search with `std::find`  
    - Binary search with `std::binary_search`  
4. **Examples of Real-world Applications**  


## 🔍 What is STL?
STL is a library that includes:

### 📦 Containers
Containers represent data structures such as arrays, lists, stacks, and queues. Examples:
- `std::vector` - dynamic array
- `std::list` - linked list
- `std::map` - associative container

```cpp
#include <vector>
#include <iostream>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};
    for (int num : numbers) {
        std::cout << num << " ";
    }
    return 0;
}
```

### 🔄 Iterators
Iterators allow for the traversal of elements in containers. Example:

```cpp
#include <vector>
#include <iostream>

int main() {
    std::vector<int> numbers = {10, 20, 30, 40};
    for (auto it = numbers.begin(); it != numbers.end(); ++it) {
        std::cout << *it << " ";
    }
    return 0;
}
```

### ⚙️ Algorithms
STL provides a rich set of algorithms for sorting, searching, copying, transforming, and other operations on data.


## 🔢 Sorting in STL

### 🟢 The `std::sort` Function
`std::sort` is one of the most used sorting algorithms in C++. It uses Quick Sort (technically IntroSort) and has a time complexity of O(n log n).

**Example 1: Sorting an Array**
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> numbers = {5, 2, 9, 1, 5, 6};
    std::sort(numbers.begin(), numbers.end());

    for (int num : numbers) {
        std::cout << num << " ";
    }
    return 0;
}
```

**Example 2: Custom Sorting**

Creating your own function for sorting in descending order:
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

bool descending(int a, int b) {
    return a > b;
}

int main() {
    std::vector<int> numbers = {3, 1, 4, 1, 5, 9};
    std::sort(numbers.begin(), numbers.end(), descending);

    for (int num : numbers) {
        std::cout << num << " ";
    }
    return 0;
}
```

---

## 🔎 Searching in STL

### 🟡 Linear Search with `std::find`

`std::find` performs a linear search over a range of elements.

**Example:**
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> numbers = {10, 20, 30, 40, 50};
    auto it = std::find(numbers.begin(), numbers.end(), 30);

    if (it != numbers.end()) {
        std::cout << "Element found at position: " << (it - numbers.begin()) << std::endl;
    } else {
        std::cout << "Element not found." << std::endl;
    }

    return 0;
}
```

### 🟢 Binary Search with `std::binary_search`

`std::binary_search` is a more efficient search algorithm that requires a previously sorted container.

**Example:**
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5, 6};
    bool found = std::binary_search(numbers.begin(), numbers.end(), 4);

    if (found) {
        std::cout << "Element found." << std::endl;
    } else {
        std::cout << "Element not found." << std::endl;
    }

    return 0;
}
```


## 📈 Examples of Real-world Applications

### 1. Sorting Data from a File
```cpp
#include <algorithm>
#include <fstream>
#include <vector>
#include <iostream>

int main() {
    std::ifstream infile("data.txt");
    std::vector<int> numbers;
    int num;

    while (infile >> num) {
        numbers.push_back(num);
    }

    std::sort(numbers.begin(), numbers.end());

    for (int n : numbers) {
        std::cout << n << " ";
    }

    return 0;
}
```

### 2. Searching for a Specific Value in Sorted Data
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> data = {1, 3, 5, 7, 9};
    int target = 5;

    if (std::binary_search(data.begin(), data.end(), target)) {
        std::cout << "The value " << target << " was found." << std::endl;
    } else {
        std::cout << "The value " << target << " was not found." << std::endl;
    }

    return 0;
}
```


## 🔧 Other Useful Algorithms from STL

### 1. `std::reverse` - Reversing a Sequence
Reverses the elements in a range.
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> nums = {1, 2, 3, 4, 5};
    std::reverse(nums.begin(), nums.end());
    
    for (int n : nums) {
        std::cout << n << " "; // 5 4 3 2 1
    }
    return 0;
}
```

### 2. `std::unique` - Removing Duplicates
Removes consecutive equal elements. Works best on a sorted array.
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> nums = {1, 1, 2, 2, 3, 3, 3, 4};
    auto it = std::unique(nums.begin(), nums.end());
    nums.erase(it, nums.end()); // We remove the extra elements
    
    for (int n : nums) {
        std::cout << n << " "; // 1 2 3 4
    }
    return 0;
}
```

### 3. `std::lower_bound` and `std::upper_bound`
They search for an insertion position in a sorted array.
*   `lower_bound(x)`: the first element >= x.
*   `upper_bound(x)`: the first element > x.

```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> nums = {1, 2, 4, 4, 4, 7, 9};
    
    auto lb = std::lower_bound(nums.begin(), nums.end(), 4);
    auto ub = std::upper_bound(nums.begin(), nums.end(), 4);
    
    std::cout << "First occurrence of 4 at position: " << (lb - nums.begin()) << std::endl; // 2
    std::cout << "Number of occurrences of 4: " << (ub - lb) << std::endl; // 3
    
    return 0;
}
```

### 4. `std::next_permutation` - Permutations
Generates the next lexicographical permutation.
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> nums = {1, 2, 3};
    
    do {
        for (int n : nums) {
            std::cout << n << " ";
        }
        std::cout << std::endl;
    } while (std::next_permutation(nums.begin(), nums.end()));
    
    // Outputs all permutations: 123, 132, 213, 231, 312, 321
    return 0;
}
```

### 5. `std::min_element` and `std::max_element`
Find an iterator to the minimal/maximal element.
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> nums = {3, 1, 4, 1, 5, 9, 2, 6};
    
    auto minIt = std::min_element(nums.begin(), nums.end());
    auto maxIt = std::max_element(nums.begin(), nums.end());
    
    std::cout << "Minimum: " << *minIt << std::endl; // 1
    std::cout << "Maximum: " << *maxIt << std::endl; // 9
    std::cout << "Max position: " << (maxIt - nums.begin()) << std::endl; // 5
    
    return 0;
}
```

### 6. `std::count` and `std::count_if`
Count the occurrences of an element or elements matching a condition.
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> nums = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    
    int countFives = std::count(nums.begin(), nums.end(), 5); // 1
    
    // Count of even numbers
    int countEven = std::count_if(nums.begin(), nums.end(), [](int x) {
        return x % 2 == 0;
    });
    
    std::cout << "Even numbers: " << countEven << std::endl; // 4
    return 0;
}
```

## 🎲 Lambda Expressions
Lambda functions are anonymous functions that can be used directly in STL algorithms.

### Syntax
```cpp
[capture](parameters) -> return_type { body }
```

### Examples
```cpp
#include <algorithm>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> nums = {5, 2, 9, 1, 7};
    
    // Sorting in descending order with a lambda
    std::sort(nums.begin(), nums.end(), [](int a, int b) {
        return a > b;
    });
    
    // Filtering - keeping only even numbers
    auto it = std::remove_if(nums.begin(), nums.end(), [](int x) {
        return x % 2 != 0;
    });
    nums.erase(it, nums.end());
    
    for (int n : nums) {
        std::cout << n << " ";
    }
    
    return 0;
}
```

## 📊 Complexity of Algorithms
Understanding time complexity is critical for competitive programming.

| Algorithm | Complexity | Notes |
|-----------|------------|-----------|
| `std::sort` | O(n log n) | IntroSort (Quick + Heap + Insertion) |
| `std::stable_sort` | O(n log n) | Preserves order of equal elements |
| `std::find` | O(n) | Linear search |
| `std::binary_search` | O(log n) | Requires a sorted container |
| `std::lower_bound` | O(log n) | Binary search |
| `std::upper_bound` | O(log n) | Binary search |
| `std::min_element` | O(n) | Traverses the entire container |
| `std::max_element` | O(n) | Traverses the entire container |
| `std::reverse` | O(n) | Swaps elements |
| `std::unique` | O(n) | Removes duplicates |

## 💡 Practical Tips for Competitions

### 1. Always sort before `binary_search`
```cpp
std::vector<int> nums = {5, 2, 9, 1, 7};
std::sort(nums.begin(), nums.end()); // MANDATORY!
bool found = std::binary_search(nums.begin(), nums.end(), 7);
```

### 2. Use `lower_bound` to find a position
```cpp
auto it = std::lower_bound(nums.begin(), nums.end(), x);
if (it != nums.end() && *it == x) {
    std::cout << "Found at position: " << (it - nums.begin()) << std::endl;
}
```

### 3. Combining `sort` + `unique` to remove duplicates
```cpp
std::sort(nums.begin(), nums.end());
nums.erase(std::unique(nums.begin(), nums.end()), nums.end());
```

### 4. Custom Sorting of Structures
```cpp
struct Student {
    std::string name;
    int grade;
};

std::vector<Student> students = {{"Ivan", 85}, {"Maria", 92}, {"Peter", 78}};

// Sorting by grade (descending)
std::sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
    return a.grade > b.grade;
});
```

## 🧪 Practice Tasks

1. **Word Sorting**: Read N words and sort them lexicographically.
2. **Median**: Find the median of an array of numbers (use `nth_element`).
3. **Removing Duplicates**: From an unsorted array (combine `sort` + `unique`).
4. **K-th Smallest**: Find the K-th smallest number in an array in O(n) (use `nth_element`).
5. **Binary Search**: Write a program that answers Q queries whether a given number occurs in an array.
6. **Permutations**: Generate all permutations of an array of N elements (N ≤ 8).
7. **Sorting by Absolute Value**: Sort an array by the absolute value of its elements.
8. **Inversion Count**: Use `merge` to count inversions in an array (advanced).

## 🎯 Conclusion
STL is a powerful tool that significantly simplifies working with data. Sorting and searching are fundamental operations that, thanks to STL, are realized easily and effectively. Understanding the `sort`, `binary_search`, `lower_bound`, `upper_bound`, and other algorithms is of critical importance for success in competitive programming. Lambda functions provide flexibility in customizing algorithm behavior. Always remember the time complexity of operations and choose the correct algorithm for the task. Practice regularly with different tasks to fully master the capabilities of STL.