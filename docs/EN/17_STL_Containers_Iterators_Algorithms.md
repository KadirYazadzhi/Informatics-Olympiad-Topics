# 📚 STL Standard Library: Containers, Iterators, and Basic Algorithms

## Introduction to STL
The Standard Template Library (STL) is an integral part of C++ and provides a rich set of components for working with data. It includes containers, iterators, and algorithms used for effective data management.

**STL essentially consists of three main components:**

1. **Containers:** Basic data structures that store data in different ways (arrays, lists, sets, maps, etc.).
2. **Iterators:** A method for sequentially traversing elements in containers.
3. **Algorithms:** Primarily templates for sorting, searching, validation, and more.


## Containers in STL

Containers are means for building data structures and are divided into three main categories:

### 1. **Sequence Containers**
Store elements in a linear order.

#### **Example with `vector`:**
```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};

    // Adding an element
    vec.push_back(6);

    // Traversing elements
    for (int val : vec) {
        std::cout << val << " ";
    }

    return 0;
}
```

### 2. **Associative Containers**
Store data in key-value pairs (e.g., with `map`).

#### **Example with `map`:**
```cpp
#include <iostream>
#include <map>

int main() {
    std::map<std::string, int> age;

    // Adding data
    age["Alice"] = 25;
    age["Bob"] = 30;

    // Output
    for (const auto& [key, value] : age) {
        std::cout << key << ": " << value << "\n";
    }

    return 0;
}
```

### 3. **Container Adaptors**
Mainly used for specific cases where we need to work with stacks (`stack`), queues (`queue`), and double-ended queues (`deque`).

#### **Example with `stack`:**
```cpp
#include <iostream>
#include <stack>

int main() {
    std::stack<int> s;

    // Adding elements
    s.push(1);
    s.push(2);
    s.push(3);

    // Removing and outputting
    while (!s.empty()) {
        std::cout << s.top() << " ";
        s.pop();
    }

    return 0;
}
```


## Iterators in STL

Iterators are a fundamental tool for accessing elements in containers. They abstract the process of traversing elements.

### **Example with Iterator:**
```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec = {10, 20, 30, 40};
    std::vector<int>::iterator it;

    for (it = vec.begin(); it != vec.end(); ++it) {
        std::cout << *it << " ";
    }

    return 0;
}
```


## Basic Algorithms

STL offers numerous algorithms that simplify working with containers. These include sorting, searching, manipulation, and validation of elements.

### **Example with `sort`:**
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {5, 3, 8, 1, 2};

    std::sort(vec.begin(), vec.end());

    for (int val : vec) {
        std::cout << val << " ";
    }

    return 0;
}
```


## Conclusion
STL is a powerful tool for C++ developers. Understanding containers, iterators, and algorithms is essential for writing efficient and readable code.
