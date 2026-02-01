# 📚 Introduction to the Standard Template Library (STL) and Sorting/Searching Tools

The Standard Template Library (STL) is a fundamental tool in C++ programming. It provides ready-made implementations of algorithms, containers, and iterators that facilitate application development. One of the key aspects of STL is the availability of powerful tools for sorting and searching.


## 📋 Content
1. **What is STL?**  
    - Containers  
    - Iterators  
    - Algorithms  
2. **Sorting in STL**  
    - Function `std::sort`  
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
Iterators allow traversing elements in containers. Example:

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

### 🟢 Function `std::sort`
`std::sort` is one of the most used sorting algorithms in C++. It uses Quick Sort and has a time complexity of O(n log n).

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

Creating a custom function for sorting in descending order:
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

`std::find` performs a linear search on a range of elements.

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

`std::binary_search` is a more efficient searching algorithm that requires a sorted container.

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
        std::cout << "Value " << target << " found." << std::endl;
    } else {
        std::cout << "Value " << target << " not found." << std::endl;
    }

    return 0;
}
```


## 🎯 Conclusion
STL is a powerful tool that significantly simplifies working with data. Sorting and searching are fundamental operations which, thanks to STL, are implemented easily and efficiently. Start with the basic algorithms and gradually expand your knowledge to use the full potential of the library.
