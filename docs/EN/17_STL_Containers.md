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