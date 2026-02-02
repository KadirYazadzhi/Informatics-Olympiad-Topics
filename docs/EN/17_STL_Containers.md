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
# 📦 STL Containers: Complete Guide

The Standard Template Library (STL) provides powerful, optimized, and ready-to-use data structures. Mastering them is essential for competitive programming.

## 1. Sequence Containers

### 1.1. `std::vector` (Dynamic Array)
The most used container. Stores elements contiguously in memory.
*   **Access**: $O(1)$ via `v[i]`.
*   **Push Back**: $O(1)$ amortized.
*   **Insert/Erase** (middle): $O(N)$ (slow!).
*   **Size**: `v.size()`, `v.empty()`.

```cpp
vector<int> v = {1, 2, 3};
v.push_back(4);
v.pop_back(); // Removes last
sort(v.begin(), v.end());
```

### 1.2. `std::deque` (Double Ended Queue)
Allows push/pop from both ends in $O(1)$.
*   Memory is not guaranteed to be contiguous (chunks).
*   Slightly slower `[]` access than vector, but faster `push_front`.

```cpp
deque<int> d;
d.push_front(1);
d.push_back(2);
```

## 2. Associative Containers
Elements are **sorted**. Implemented via **Red-Black Trees**.

### 2.1. `std::set`
Stores unique elements in sorted order.
*   **Insert/Erase/Find**: $O(\log N)$.
*   **Iterate**: In sorted order.

```cpp
set<int> s;
s.insert(5);
if (s.count(5)) cout << "Found";
```

### 2.2. `std::map` (Dictionary)
Stores key-value pairs sorted by key.
*   **Access**: `m[key]` returns reference (creates if missing).
*   **Complexity**: $O(\log N)$ for all operations.

```cpp
map<string, int> scores;
scores["Alice"] = 100;
for (auto& p : scores) cout << p.first << ": " << p.second << endl;
```

### 2.3. `multiset` / `multimap`
Allow duplicate keys.
*   `s.erase(val)` removes **all** instances.
*   `s.erase(s.find(val))` removes only **one** instance.

## 3. Unordered Containers
Based on **Hash Tables**.
*   **Complexity**: $O(1)$ average, $O(N)$ worst case (collisions).
*   **Warning**: Vulnerable to "anti-hash" tests in Codeforces. Use custom hash function.

## 4. Container Adapters

### 4.1. `std::stack` (LIFO)
*   `push()`, `pop()`, `top()`. All $O(1)$.
*   Used for DFS, Bracket matching.

### 4.2. `std::queue` (FIFO)
*   `push()`, `pop()`, `front()`. All $O(1)$.
*   Used for BFS.

### 4.3. `std::priority_queue` (Heap)
Always gives the largest element (`top()`).
*   **Push/Pop**: $O(\log N)$.
*   **Top**: $O(1)$.
*   Min-Heap: `priority_queue<int, vector<int>, greater<int>> pq;`

## 5. Special Containers

### 5.1. `std::bitset`
Optimized array of bits (1 bit per element).
*   Supports bitwise ops (`&`, `|`, `^`, `<<`) on the whole array.
*   Very fast for Knapsack-like DP.
*   Size must be compile-time constant.

```cpp
bitset<8> b("10101010");
b[0] = 0;
cout << b.count(); // Number of set bits
```

## 6. Iterators
*   `begin()`: Points to first element.
*   `end()`: Points **after** last element.
*   `rbegin()`: Reverse iterator.

```cpp
auto it = s.find(10);
if (it != s.end()) { /* found */ }
```

## 7. Conclusion
*   Array/Index -> `vector`.
*   Search/Unique -> `set`.
*   Key-Value -> `map`.
*   BFS -> `queue`.
*   Dijkstra -> `priority_queue`.