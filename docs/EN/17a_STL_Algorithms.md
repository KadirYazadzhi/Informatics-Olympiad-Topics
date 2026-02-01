# ⚙️ Algorithms in STL

The Standard Template Library (STL) is one of the most powerful features of C++, providing a comprehensive collection of algorithms that every competitive programmer should master. The `<algorithm>` header contains over 100 well-tested, highly optimized functions for working with containers, making your code cleaner, faster, and less error-prone.

Understanding and effectively using STL algorithms is crucial for competitive programming because:
- **Performance**: STL algorithms are optimized by experts and often outperform hand-written code
- **Reliability**: They are thoroughly tested and less prone to bugs
- **Readability**: Code using STL algorithms is more expressive and easier to understand
- **Time-saving**: Writing less code means more time for problem-solving

## 🔢 Sorting and Searching Algorithms

### Sorting Functions

#### `std::sort(begin, end, [comparator])`
The workhorse sorting algorithm with **$O(N \log N)$ time complexity** and typically uses an optimized implementation of quicksort, heapsort, or introsort.

```cpp
#include <algorithm>
#include <vector>
using namespace std;

int main() {
    vector<int> v = {5, 2, 8, 1, 9};
    
    // Sort in ascending order
    sort(v.begin(), v.end());
    // Result: {1, 2, 5, 8, 9}
    
    // Sort in descending order using greater comparator
    sort(v.begin(), v.end(), greater<int>());
    // Result: {9, 8, 5, 2, 1}
    
    // Custom comparator for pairs - sort by second element
    vector<pair<int, int>> pairs = {{1, 5}, {2, 3}, {3, 7}};
    sort(pairs.begin(), pairs.end(), 
         [](const pair<int,int>& a, const pair<int,int>& b) {
             return a.second < b.second;
         });
    // Result: {{2,3}, {1,5}, {3,7}}
    
    return 0;
}
```

**Important considerations:**
- Not stable (relative order of equal elements is not guaranteed)
- Works with any random-access container (vector, array, deque)
- Requires elements to be comparable with `<` operator or custom comparator

#### `std::stable_sort(begin, end, [comparator])`
Similar to `sort()` but **guarantees stability** - elements that compare equal retain their relative order.

```cpp
struct Student {
    string name;
    int grade;
};

vector<Student> students = {
    {"Alice", 85}, {"Bob", 90}, {"Charlie", 85}, {"David", 90}
};

// First sort by name
sort(students.begin(), students.end(), 
     [](const Student& a, const Student& b) { return a.name < b.name; });
// Alice, Bob, Charlie, David

// Then stable_sort by grade - students with same grade keep name order
stable_sort(students.begin(), students.end(),
            [](const Student& a, const Student& b) { return a.grade < b.grade; });
// Result: Alice(85), Charlie(85), Bob(90), David(90)
// Notice Alice before Charlie, Bob before David
```

**Time Complexity:** $O(N \log N)$ but may use more memory than regular sort.

#### `std::partial_sort(begin, middle, end)`
When you only need the smallest (or largest) K elements sorted:

```cpp
vector<int> v = {9, 5, 7, 1, 3, 8, 2, 6, 4};

// Get the 3 smallest elements in sorted order
partial_sort(v.begin(), v.begin() + 3, v.end());
// First 3 elements: {1, 2, 3}, rest: unspecified order

// Time complexity: O(N log K) where K = 3
```

**Use case:** Finding top K elements is faster than sorting everything.

#### `std::nth_element(begin, nth, end)`
Partially sorts so that element at position `nth` is in its sorted position, all elements before it are smaller or equal, and all after are greater or equal.

```cpp
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};

// Find median
nth_element(v.begin(), v.begin() + v.size()/2, v.end());
int median = v[v.size()/2];

// Time complexity: O(N) on average - much faster than full sort!
```

**Applications:**
- Finding median in $O(N)$
- Finding k-th smallest element
- Partitioning data around a pivot

### Binary Search Functions

All binary search functions require the range to be **sorted** first!

#### `std::binary_search(begin, end, value)`
Returns `true` if value exists in the range, `false` otherwise.

```cpp
vector<int> v = {1, 2, 3, 5, 8, 13, 21};

bool exists = binary_search(v.begin(), v.end(), 5);    // true
bool missing = binary_search(v.begin(), v.end(), 7);   // false

// Time complexity: O(log N)
```

**Limitation:** Only tells if element exists, not its position.

#### `std::lower_bound(begin, end, value)`
Returns iterator to **first element that is NOT less than** value (i.e., $\ge$ value).

```cpp
vector<int> v = {1, 2, 2, 2, 5, 8, 8, 13};

auto it = lower_bound(v.begin(), v.end(), 2);
// Points to first 2 at index 1

int index = it - v.begin();  // Get index: 1

auto it2 = lower_bound(v.begin(), v.end(), 7);
// Points to 8 (first element >= 7)
```

**Common use cases:**
```cpp
// Count occurrences of value in sorted array
auto lb = lower_bound(v.begin(), v.end(), value);
auto ub = upper_bound(v.begin(), v.end(), value);
int count = ub - lb;

// Insert value maintaining sorted order
v.insert(lower_bound(v.begin(), v.end(), value), value);

// Check if value exists and get position
auto it = lower_bound(v.begin(), v.end(), value);
if (it != v.end() && *it == value) {
    // Found at position (it - v.begin())
}
```

#### `std::upper_bound(begin, end, value)`
Returns iterator to **first element that is greater than** value (i.e., $>$ value).

```cpp
vector<int> v = {1, 2, 2, 2, 5, 8, 8, 13};

auto it = upper_bound(v.begin(), v.end(), 2);
// Points to 5 (first element > 2)

// Find range of equal elements
auto lb = lower_bound(v.begin(), v.end(), 2);
auto ub = upper_bound(v.begin(), v.end(), 2);
// Range [lb, ub) contains all 2's
```

#### `std::equal_range(begin, end, value)`
Returns a pair of iterators `[lower_bound, upper_bound)` for the given value.

```cpp
vector<int> v = {1, 2, 2, 2, 5, 8};

auto range = equal_range(v.begin(), v.end(), 2);
// range.first = lower_bound, range.second = upper_bound
int count = range.second - range.first;  // 3 occurrences
```

## 🛠️ Data Manipulation Algorithms

### Modifying Sequences

#### `std::reverse(begin, end)`
Reverses the order of elements in the range.

```cpp
vector<int> v = {1, 2, 3, 4, 5};
reverse(v.begin(), v.end());
// Result: {5, 4, 3, 2, 1}

string s = "hello";
reverse(s.begin(), s.end());
// Result: "olleh"

// Time complexity: O(N)
```

#### `std::rotate(begin, middle, end)`
Rotates elements so that `middle` becomes the first element.

```cpp
vector<int> v = {1, 2, 3, 4, 5};
rotate(v.begin(), v.begin() + 2, v.end());
// Result: {3, 4, 5, 1, 2}

// Useful for cyclic shifts
// Left shift by k positions:
rotate(v.begin(), v.begin() + k, v.end());

// Right shift by k positions:
rotate(v.rbegin(), v.rbegin() + k, v.rend());
```

#### `std::unique(begin, end)`
Removes **consecutive** duplicate elements. Returns iterator to new end.

**Critical**: Must sort first to remove ALL duplicates!

```cpp
vector<int> v = {1, 2, 2, 3, 2, 4, 4, 5};

// WRONG: Without sorting
auto new_end = unique(v.begin(), v.end());
// Only removes consecutive duplicates: {1, 2, 3, 2, 4, 5}

// CORRECT: With sorting
sort(v.begin(), v.end());
// {1, 2, 2, 2, 3, 4, 4, 5}
new_end = unique(v.begin(), v.end());
v.erase(new_end, v.end());  // Actually remove elements
// Result: {1, 2, 3, 4, 5}

// One-liner idiom:
v.erase(unique(v.begin(), v.end()), v.end());
```

#### `std::fill(begin, end, value)` and `std::fill_n(begin, count, value)`
Assigns value to all elements in range.

```cpp
vector<int> v(5);
fill(v.begin(), v.end(), 42);
// Result: {42, 42, 42, 42, 42}

int arr[10];
fill_n(arr, 10, -1);
// All elements set to -1
```

#### `std::copy(begin, end, dest_begin)`
Copies elements from one range to another.

```cpp
vector<int> src = {1, 2, 3, 4, 5};
vector<int> dest(5);

copy(src.begin(), src.end(), dest.begin());
// dest = {1, 2, 3, 4, 5}

// Copy to output
copy(src.begin(), src.end(), ostream_iterator<int>(cout, " "));
// Prints: 1 2 3 4 5
```

### Permutation Algorithms

#### `std::next_permutation(begin, end)`
Transforms range to next lexicographically greater permutation. Returns `true` if successful, `false` if already at last permutation.

```cpp
vector<int> v = {1, 2, 3};

do {
    for(int x : v) cout << x << " ";
    cout << "\n";
} while(next_permutation(v.begin(), v.end()));

// Output:
// 1 2 3
// 1 3 2
// 2 1 3
// 2 3 1
// 3 1 2
// 3 2 1
```

**Important for competitive programming:**
```cpp
// Generate all permutations of a string
string s = "ABC";
sort(s.begin(), s.end());  // Must start sorted!
do {
    cout << s << "\n";
} while(next_permutation(s.begin(), s.end()));

// Count permutations until condition met
int count = 0;
do {
    count++;
    // Check some condition...
} while(next_permutation(v.begin(), v.end()));
```

#### `std::prev_permutation(begin, end)`
Similar to `next_permutation` but generates previous permutation.

```cpp
vector<int> v = {3, 2, 1};  // Start from largest

do {
    for(int x : v) cout << x << " ";
    cout << "\n";
} while(prev_permutation(v.begin(), v.end()));
```

## 📊 Mathematical and Numeric Algorithms

### Finding Extrema

#### `std::max_element(begin, end)` / `std::min_element(begin, end)`
Returns iterator to the maximum/minimum element.

```cpp
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};

auto max_it = max_element(v.begin(), v.end());
cout << "Maximum: " << *max_it << "\n";           // 9
cout << "Position: " << (max_it - v.begin()) << "\n";  // 5

auto min_it = min_element(v.begin(), v.end());
cout << "Minimum: " << *min_it << "\n";           // 1

// Custom comparator for complex types
struct Point { int x, y; };
vector<Point> points = {{1, 2}, {3, 1}, {2, 5}};

auto furthest = max_element(points.begin(), points.end(),
    [](const Point& a, const Point& b) {
        return (a.x*a.x + a.y*a.y) < (b.x*b.x + b.y*b.y);
    });
```

#### `std::minmax_element(begin, end)`
Returns both minimum and maximum in one pass (more efficient).

```cpp
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};

auto [min_it, max_it] = minmax_element(v.begin(), v.end());
cout << "Min: " << *min_it << ", Max: " << *max_it << "\n";
// Min: 1, Max: 9
```

### Numeric Operations (from `<numeric>`)

#### `std::accumulate(begin, end, init, [binary_op])`
Computes sum or custom fold operation.

```cpp
#include <numeric>

vector<int> v = {1, 2, 3, 4, 5};

// Sum all elements
int sum = accumulate(v.begin(), v.end(), 0);
// Result: 15

// Product of all elements
int product = accumulate(v.begin(), v.end(), 1, multiplies<int>());
// Result: 120

// Custom operation - concatenate strings
vector<string> words = {"Hello", " ", "World"};
string sentence = accumulate(words.begin(), words.end(), string(""));
// Result: "Hello World"

// Sum of squares
int sum_of_squares = accumulate(v.begin(), v.end(), 0,
    [](int acc, int x) { return acc + x * x; });
// Result: 55
```

#### `std::partial_sum(begin, end, dest_begin)`
Computes prefix sums (cumulative sum).

```cpp
vector<int> v = {1, 2, 3, 4, 5};
vector<int> prefix(5);

partial_sum(v.begin(), v.end(), prefix.begin());
// prefix = {1, 3, 6, 10, 15}

// Can also do in-place
partial_sum(v.begin(), v.end(), v.begin());
```

#### `std::iota(begin, end, start_value)`
Fills range with incrementing values starting from `start_value`.

```cpp
vector<int> v(10);
iota(v.begin(), v.end(), 1);
// Result: {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// Create index array
vector<int> indices(n);
iota(indices.begin(), indices.end(), 0);
// indices = {0, 1, 2, ..., n-1}
```

## 🔍 Search and Query Algorithms

#### `std::find(begin, end, value)`
Linear search for value. Returns iterator to first occurrence or `end` if not found.

```cpp
vector<int> v = {1, 2, 3, 4, 5};

auto it = find(v.begin(), v.end(), 3);
if (it != v.end()) {
    cout << "Found at position: " << (it - v.begin()) << "\n";
}

// Time complexity: O(N) - use binary_search for sorted arrays!
```

#### `std::find_if(begin, end, predicate)`
Finds first element satisfying predicate.

```cpp
vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8};

// Find first even number
auto it = find_if(v.begin(), v.end(), [](int x) { return x % 2 == 0; });
// Points to 2

// Find first number > 5
auto it2 = find_if(v.begin(), v.end(), [](int x) { return x > 5; });
// Points to 6
```

#### `std::count(begin, end, value)` / `std::count_if(begin, end, predicate)`
Counts occurrences of value or elements satisfying predicate.

```cpp
vector<int> v = {1, 2, 2, 3, 2, 4, 5};

int count = count(v.begin(), v.end(), 2);
// Result: 3

int even_count = count_if(v.begin(), v.end(), [](int x) { return x % 2 == 0; });
// Result: 4 (elements: 2, 2, 2, 4)
```

#### `std::all_of`, `std::any_of`, `std::none_of`
Check if predicate is true for all/any/none of the elements.

```cpp
vector<int> v = {2, 4, 6, 8, 10};

bool all_even = all_of(v.begin(), v.end(), [](int x) { return x % 2 == 0; });
// true

bool has_negative = any_of(v.begin(), v.end(), [](int x) { return x < 0; });
// false

bool no_odds = none_of(v.begin(), v.end(), [](int x) { return x % 2 == 1; });
// true
```

## 🎯 Set Operations (on Sorted Ranges)

All set operations require **sorted** input ranges!

#### `std::set_union`, `std::set_intersection`, `std::set_difference`

```cpp
vector<int> a = {1, 2, 3, 4, 5};
vector<int> b = {3, 4, 5, 6, 7};
vector<int> result;

// Union: all elements from both (no duplicates)
set_union(a.begin(), a.end(), b.begin(), b.end(), back_inserter(result));
// result = {1, 2, 3, 4, 5, 6, 7}

result.clear();

// Intersection: common elements
set_intersection(a.begin(), a.end(), b.begin(), b.end(), back_inserter(result));
// result = {3, 4, 5}

result.clear();

// Difference: elements in a but not in b
set_difference(a.begin(), a.end(), b.begin(), b.end(), back_inserter(result));
// result = {1, 2}
```

## 🚀 Advanced Algorithms

#### `std::partition(begin, end, predicate)`
Reorders elements so those satisfying predicate come first. Returns iterator to first element not satisfying predicate.

```cpp
vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8};

auto pivot = partition(v.begin(), v.end(), [](int x) { return x % 2 == 0; });
// Possible result: {2, 4, 6, 8, 1, 3, 5, 7}
// pivot points to 1

// Time complexity: O(N)
// Not stable - use stable_partition to preserve relative order
```

#### `std::transform(begin, end, dest_begin, unary_op)`
Applies operation to each element and stores result.

```cpp
vector<int> v = {1, 2, 3, 4, 5};
vector<int> squares(5);

transform(v.begin(), v.end(), squares.begin(), 
          [](int x) { return x * x; });
// squares = {1, 4, 9, 16, 25}

// In-place transformation
transform(v.begin(), v.end(), v.begin(),
          [](int x) { return x * 2; });
// v = {2, 4, 6, 8, 10}
```

#### `std::remove(begin, end, value)` and `std::remove_if(begin, end, predicate)`
"Removes" elements by moving them to the end. **Must call `erase()` to actually delete!**

```cpp
vector<int> v = {1, 2, 3, 2, 4, 2, 5};

// Remove all 2's
auto new_end = remove(v.begin(), v.end(), 2);
v.erase(new_end, v.end());
// v = {1, 3, 4, 5}

// Erase-remove idiom (one line):
v.erase(remove(v.begin(), v.end(), value), v.end());

// Remove even numbers
v.erase(remove_if(v.begin(), v.end(), 
                  [](int x) { return x % 2 == 0; }), 
        v.end());
```

## 💡 Performance Tips and Best Practices

### 1. Choose the Right Algorithm
```cpp
// BAD: O(N) linear search on sorted array
vector<int> v = {1, 2, 3, ..., 1000000};  // sorted
auto it = find(v.begin(), v.end(), target);  // O(N)

// GOOD: O(log N) binary search
bool found = binary_search(v.begin(), v.end(), target);  // O(log N)
```

### 2. Avoid Unnecessary Copies
```cpp
// BAD: Multiple passes
sort(v.begin(), v.end());
auto new_end = unique(v.begin(), v.end());
v.erase(new_end, v.end());

// GOOD: Combined operations
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
```

### 3. Use Appropriate Containers
```cpp
// If you need frequent lookups: use set/unordered_set instead of vector+sort
set<int> s;  // Automatic sorting, O(log N) lookup
unordered_set<int> us;  // O(1) average lookup
```

### 4. Lambda Expressions for Custom Logic
```cpp
// Clean and readable
sort(students.begin(), students.end(),
     [](const Student& a, const Student& b) {
         return a.grade > b.grade;  // Sort by grade descending
     });
```

## 🎓 Common Competitive Programming Patterns

### Pattern 1: Sort + Binary Search
```cpp
// Problem: Count elements in range [L, R]
sort(v.begin(), v.end());
auto left = lower_bound(v.begin(), v.end(), L);
auto right = upper_bound(v.begin(), v.end(), R);
int count = right - left;
```

### Pattern 2: Sort + Unique (Remove Duplicates)
```cpp
// Problem: Count distinct elements
sort(v.begin(), v.end());
int distinct = unique(v.begin(), v.end()) - v.begin();
```

### Pattern 3: Sort + Two Pointers
```cpp
// Problem: Find pair with sum equal to target
sort(v.begin(), v.end());
int left = 0, right = n - 1;
while (left < right) {
    int sum = v[left] + v[right];
    if (sum == target) {
        // Found pair
        break;
    } else if (sum < target) {
        left++;
    } else {
        right--;
    }
}
```

### Pattern 4: Prefix Sums
```cpp
// Problem: Range sum queries
vector<int> prefix(n + 1, 0);
partial_sum(v.begin(), v.end(), prefix.begin() + 1);

// Sum of range [L, R]
int range_sum = prefix[R + 1] - prefix[L];
```

### Pattern 5: Generate and Test Permutations
```cpp
// Problem: Find permutation satisfying condition
sort(v.begin(), v.end());
do {
    if (check_condition(v)) {
        return true;
    }
} while (next_permutation(v.begin(), v.end()));
```

## ⚠️ Common Pitfalls

### 1. Forgetting to Sort Before Binary Search
```cpp
vector<int> v = {5, 2, 8, 1, 9};
binary_search(v.begin(), v.end(), 5);  // WRONG! Undefined behavior!

sort(v.begin(), v.end());
binary_search(v.begin(), v.end(), 5);  // CORRECT
```

### 2. Not Using Erase After Remove
```cpp
remove(v.begin(), v.end(), value);  // Elements still in vector!
v.erase(remove(v.begin(), v.end(), value), v.end());  // CORRECT
```

### 3. Modifying Container While Iterating
```cpp
// WRONG
for (auto it = v.begin(); it != v.end(); ++it) {
    if (*it == value) v.erase(it);  // Iterator invalidated!
}

// CORRECT
v.erase(remove(v.begin(), v.end(), value), v.end());
```

---

## 🏁 Conclusion

The STL algorithms are an essential tool in every competitive programmer's arsenal. Key takeaways:

1. **Don't Reinvent the Wheel**: STL algorithms are tested, optimized, and less error-prone than custom implementations
2. **Know Your Complexities**: Understanding time complexity helps choose the right algorithm
3. **Sort First**: Many algorithms require sorted data (binary search, unique, set operations)
4. **Use Lambdas**: They make code more readable and allow custom logic
5. **Practice Common Patterns**: Sort+binary search, erase-remove idiom, prefix sums, etc.

Master these algorithms and you'll write cleaner, faster, and more reliable code in competitions. The time saved from avoiding bugs and implementing optimized solutions can be the difference between solving one more problem or not!

### Quick Reference Table

| Algorithm | Time Complexity | Requires Sorted | Use Case |
|-----------|----------------|-----------------|----------|
| `sort` | $O(N \log N)$ | No | General sorting |
| `binary_search` | $O(\log N)$ | Yes | Check existence |
| `lower_bound` | $O(\log N)$ | Yes | Find position |
| `unique` | $O(N)$ | **Yes** | Remove duplicates |
| `reverse` | $O(N)$ | No | Reverse order |
| `next_permutation` | $O(N)$ | No | Generate permutations |
| `max_element` | $O(N)$ | No | Find maximum |
| `accumulate` | $O(N)$ | No | Sum/fold |
| `partial_sum` | $O(N)$ | No | Prefix sums |

Remember: STL algorithms save time, reduce bugs, and make your code more professional!
