# 📦 STL Containers: Complete Guide

The Standard Template Library (STL) in C++ provides powerful data structures that are optimized, tested, and ready for use. Knowing their characteristics and complexity is mandatory for every competitor.

## 1. Sequence Containers

### 1.1. `std::vector` (Dynamic Array)
The most used container. Keeps elements sequentially in memory.
*   **Access**: $O(1)$ via `v[i]`.
*   **Push Back**: $O(1)$ amortized (sometimes reallocates memory).
*   **Insert/Erase** (in the middle): $O(N)$ (slow!).
*   **Size**: `v.size()`, `v.empty()`.

```cpp
vector<int> v = {1, 2, 3};
v.push_back(4);
v.pop_back(); // Removes the last one
sort(v.begin(), v.end()); // Sorting
```

### 1.2. `std::deque` (Double Ended Queue)
Allows adding/removing from both ends in $O(1)$.
*   Does not guarantee sequentiality in memory (consists of chunks).
*   Slightly slower access `[]` than vector, but faster push_front.

```cpp
deque<int> d;
d.push_front(1);
d.push_back(2);
```

### 1.3. `std::list` (Doubly Linked List)
Allows $O(1)$ insert/erase anywhere if you have an iterator.
*   Slow access: $O(N)$ to reach the k-th element.
*   Rarely used in competitions, except for specific tasks (e.g., LRU Cache).

## 2. Sorted Associative Containers
Maintain elements **sorted**. They are implemented via **Red-Black Trees** (Self-balancing BST).

### 2.1. `std::set`
Stores unique elements in sorted order.
*   **Insert/Erase/Find**: $O(\log N)$.
*   **Iterate**: In sorted order.

```cpp
set<int> s;
s.insert(5);
s.insert(3);
s.insert(5); // No effect, 5 is already there
if (s.count(3)) cout << "Found";
```

### 2.2. `std::map` (Dictionary / Associative Array)
Stores pairs `(key, value)`, sorted by key.
*   **Access**: `m[key]` returns a reference to the value (creates it if it doesn't exist, with default value 0/empty).
*   **Complexity**: $O(\log N)$ for all operations.

```cpp
map<string, int> scores;
scores["Ivan"] = 100;
scores["Ana"] = 95;
for (auto& p : scores) {
    cout << p.first << ": " << p.second << endl; // Ana: 95, Ivan: 100
}
```

### 2.3. `multiset` / `multimap`
Allow duplicate keys.
*   `s.erase(val)` erases **all** instances of `val`.
*   `s.erase(s.find(val))` erases only **one** instance.

## 3. Unordered Associative Containers
Based on **Hash Tables**. Elements are in arbitrary order.

### 3.1. `std::unordered_set` / `std::unordered_map`
*   **Complexity**: $O(1)$ average, but $O(N)$ in the worst case (collisions).
*   **Important**: In Codeforces, there are "anti-hash" tests that make these containers slow ($O(N^2)$ overall). Use a custom hash function (see Topic 09b).

## 4. Container Adapters

### 4.1. `std::stack` (LIFO)
*   `push()`, `pop()`, `top()`. Everything is $O(1)$.
*   Used for DFS, parentheses checks, Shunting-yard.

### 4.2. `std::queue` (FIFO)
*   `push()`, `pop()`, `front()`. Everything is $O(1)$.
*   Used for BFS.

### 4.3. `std::priority_queue` (Heap)
Always gives the largest element (`top()`).
*   **Push/Pop**: $O(\log N)$.
*   **Top**: $O(1)$.
*   For Min-Heap: `priority_queue<int, vector<int>, greater<int>> pq;`

## 5. Special Containers

### 5.1. `std::bitset`
Optimized array of bits. Occupies 1 bit per element (8 times less than `bool` or `vector<bool>`).
*   Supports bitwise operations (`&`, `|`, `^`, `~`, `<<`, `>>`) on the entire array.
*   Extremely fast for set tasks (Knapsack, subset sum).
*   Size must be a constant at compile-time: `bitset<1000> b;`.

```cpp
bitset<8> b("10101010");
b[0] = 0; // Changes the rightmost bit (indexing from back to front!)
cout << b.count(); // Number of ones
```

### 5.2. `std::pair` and `std::tuple`
Convenient for grouping data without creating a `struct`.
*   They are compared lexicographically automatically.

```cpp
pair<int, int> p = {1, 2};
vector<pair<int, int>> v; // Often used for graphs (neighbor, weight)
```

## 6. Iterators
Iterators are "smart pointers" that allow traversing containers.
*   `begin()`: points to the first element.
*   `end()`: points **after** the last element.
*   `rbegin()`, `rend()`: for reverse traversal.

```cpp
vector<int>::iterator it = v.begin();
auto it2 = v.find(x); // For set/map
if (it2 != v.end()) { /* found */ }
```

## 7. Practical Examples and Tasks

### 7.1. Task: Number of Unique Words in a Text
Use `set` for automatic removal of duplicates.
```cpp
#include <iostream>
#include <set>
#include <string>

int main() {
    std::set<std::string> uniqueWords;
    std::string word;
    
    while (std::cin >> word) {
        uniqueWords.insert(word);
    }
    
    std::cout << "Number of unique words: " << uniqueWords.size() << std::endl;
    
    return 0;
}
```

### 7.2. Task: Word Frequency
Use `map` for counting occurrences.
```cpp
#include <iostream>
#include <map>
#include <string>

int main() {
    std::map<std::string, int> wordCount;
    std::string word;
    
    while (std::cin >> word) {
        wordCount[word]++;
    }
    
    // Finding the most frequent word
    std::string mostFrequent;
    int maxCount = 0;
    
    for (const auto& p : wordCount) {
        if (p.second > maxCount) {
            maxCount = p.second;
            mostFrequent = p.first;
        }
    }
    
    std::cout << "Most frequent word: " << mostFrequent << " (" << maxCount << " times)" << std::endl;
    
    return 0;
}
```

### 7.3. Task: Anagram Check
Two words are anagrams if they contain the same letters with the same frequency.
```cpp
#include <iostream>
#include <map>
#include <string>

bool areAnagrams(const std::string& s1, const std::string& s2) {
    if (s1.length() != s2.length()) return false;
    
    std::map<char, int> freq1, freq2;
    
    for (char c : s1) freq1[c]++;
    for (char c : s2) freq2[c]++;
    
    return freq1 == freq2;
}

int main() {
    std::string word1 = "listen";
    std::string word2 = "silent";
    
    if (areAnagrams(word1, word2)) {
        std::cout << "The words are anagrams" << std::endl;
    } else {
        std::cout << "The words are NOT anagrams" << std::endl;
    }
    
    return 0;
}
```

### 7.4. Task: K-th Largest Number
Use `priority_queue` for efficient finding.
```cpp
#include <iostream>
#include <queue>
#include <vector>

int findKthLargest(std::vector<int>& nums, int k) {
    std::priority_queue<int, std::vector<int>, std::greater<int>> minHeap;
    
    for (int num : nums) {
        minHeap.push(num);
        if (minHeap.size() > k) {
            minHeap.pop();
        }
    }
    
    return minHeap.top();
}

int main() {
    std::vector<int> nums = {3, 2, 1, 5, 6, 4};
    int k = 2;
    
    std::cout << k << "-th largest number is: " << findKthLargest(nums, k) << std::endl; // 5
    
    return 0;
}
```

### 7.5. Task: Balanced Parentheses Check
Use `stack` to check whether parentheses are balanced.
```cpp
#include <iostream>
#include <stack>
#include <string>

bool isBalanced(const std::string& str) {
    std::stack<char> s;
    
    for (char c : str) {
        if (c == '(' || c == '[' || c == '{') {
            s.push(c);
        } else if (c == ')' || c == ']' || c == '}') {
            if (s.empty()) return false;
            
            char top = s.top();
            s.pop();
            
            if ((c == ')' && top != '(') ||
                (c == ']' && top != '[') ||
                (c == '}' && top != '{')) {
                return false;
            }
        }
    }
    
    return s.empty();
}

int main() {
    std::string expr = "{[()]}";
    
    if (isBalanced(expr)) {
        std::cout << "The parentheses are balanced" << std::endl;
    } else {
        std::cout << "The parentheses are NOT balanced" << std::endl;
    }
    
    return 0;
}
```

### 7.6. Task: Sliding Window Maximum
Find the maximum in each window of size K.
```cpp
#include <iostream>
#include <deque>
#include <vector>

std::vector<int> maxSlidingWindow(const std::vector<int>& nums, int k) {
    std::deque<int> dq; // Stores indices
    std::vector<int> result;
    
    for (int i = 0; i < nums.size(); i++) {
        // Remove elements outside the window
        if (!dq.empty() && dq.front() <= i - k) {
            dq.pop_front();
        }
        
        // Remove smaller elements from the end
        while (!dq.empty() && nums[dq.back()] < nums[i]) {
            dq.pop_back();
        }
        
        dq.push_back(i);
        
        // Add result after the first k-1 elements
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    
    return result;
}

int main() {
    std::vector<int> nums = {1, 3, -1, -3, 5, 3, 6, 7};
    int k = 3;
    
    std::vector<int> result = maxSlidingWindow(nums, k);
    
    std::cout << "Window maximums: ";
    for (int num : result) {
        std::cout << num << " "; // 3 3 5 5 6 7
    }
    std::cout << std::endl;
    
    return 0;
}
```

## 8. Additional Tips for Competitive Programming

### 8.1. Choosing the Right Container
| Operation | Recommended Container | Complexity |
|-----------|-----------------------|------------|
| Fast access by index | `vector` | O(1) |
| Adding/removing from both ends | `deque` | O(1) |
| Unique elements, sorted | `set` | O(log n) |
| Key-value, sorted | `map` | O(log n) |
| Fast search (unsorted) | `unordered_set` | O(1) average |
| Priority queue | `priority_queue` | O(log n) |
| LIFO operations | `stack` | O(1) |
| FIFO operations | `queue` | O(1) |

### 8.2. Common Errors

#### Error 1: Modification During Iteration
```cpp
// WRONG!
for (auto x : s) {
    s.erase(x); // Undefined behavior!
}

// CORRECT
while (!s.empty()) {
    s.erase(s.begin());
}
```

#### Error 2: Accessing a Non-existent Key in `map`
```cpp
std::map<std::string, int> m;
// m["Ivan"] creates an element with value 0 if it doesn't exist!

// Safer:
if (m.count("Ivan")) {
    std::cout << m["Ivan"];
} else {
    std::cout << "No such key";
}

// Or use find:
auto it = m.find("Ivan");
if (it != m.end()) {
    std::cout << it->second;
}
```

#### Error 3: Using `unordered_map` without a custom hash
In competitions, there can be specially designed tests that lead to collisions.
```cpp
// It is safer to use an ordinary map
std::map<long long, int> m; // Always works well

// Or add a custom hash:
struct CustomHash {
    size_t operator()(long long x) const {
        return x ^ (x >> 16);
    }
};
std::unordered_map<long long, int, CustomHash> m;
```

### 8.3. Optimizations

#### Reserving memory for `vector`
```cpp
std::vector<int> v;
v.reserve(1000000); // Avoids multiple reallocations
for (int i = 0; i < 1000000; i++) {
    v.push_back(i);
}
```

#### Using `emplace` instead of `push`
```cpp
std::vector<std::pair<int, int>> v;
v.emplace_back(1, 2); // Faster than v.push_back({1, 2})
```

## 🏁 Conclusion
*   For fixed-size array/indexing -> `vector`.
*   For searching/uniqueness -> `set` or `unordered_set`.
*   For keys/values -> `map`.
*   For BFS -> `queue`.
*   For DFS/parentheses check -> `stack`.
*   For Dijkstra/Prim -> `priority_queue`.
*   For bitmasks -> `bitset`.

Knowledge of STL containers and their complexity is mandatory for every competitor. Practice regularly with different tasks to develop an intuition for which container to use in various situations. Always remember the time and memory complexity of operations. Combine different containers to solve complex tasks effectively.
