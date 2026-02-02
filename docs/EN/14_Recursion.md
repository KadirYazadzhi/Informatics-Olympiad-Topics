# 🔁 Concept of Recursion

Recursion is a fundamental concept in programming where a function calls itself until a specific end condition ("base case") is met. It is a powerful tool for solving complex problems by dividing the problem into smaller similar subproblems.


## 📋 Content
1. **What is Recursion?**
    - Basic elements of recursion
    - Examples of recursion
2. **🔍 Backtracking**
    - What is backtracking?
    - Example of solving problems via backtracking
3. **🧮 Searching in a Sorted Array**
    - Linear search in a sorted array
    - Binary search
4. **🛠️ Binary Search**
    - Iterative implementation
    - Recursive implementation


## 🔁 What is Recursion?

Recursion is based on three key components:
1. **Base Case**: A condition that stops the recursive calls.
2. **Recursive Case**: The part of the code that calls the same function with a modified argument.
3. **Decomposition**: Dividing the task into smaller subtasks.

### Example: Factorial of a Number
```cpp
#include <iostream>

int factorial(int n) {
    if (n == 0) return 1; // Base case
    return n * factorial(n - 1); // Recursive case
}

int main() {
    int num = 5;
    std::cout << "Factorial of " << num << " is: " << factorial(num) << std::endl;
    return 0;
}
```

### Example: Fibonacci Numbers
```cpp
#include <iostream>

int fibonacci(int n) {
    if (n <= 1) return n; // Base case
    return fibonacci(n - 1) + fibonacci(n - 2); // Recursive case
}

int main() {
    int n = 7;
    std::cout << "Fibonacci of " << n << " is: " << fibonacci(n) << std::endl;
    return 0;
}
```


## 🔍 Backtracking

Backtracking is an algorithmic method that tries all possibilities to solve a given problem. If the current solution is not successful, the algorithm goes back and tries another path.

### Basic Elements of Backtracking:
1. Generating all possible options.
2. Checking if the current solution is valid.
3. Recursively continuing to the next step.
4. Backtracking if a dead end is reached.

### Example: Solving the N-Queens Problem
```cpp
#include <iostream>
#include <vector>

bool isSafe(const std::vector<std::vector<int>>& board, int row, int col) {
    for (int i = 0; i < col; i++)
        if (board[row][i]) return false;

    for (int i = row, j = col; i >= 0 && j >= 0; i--, j--)
        if (board[i][j]) return false;

    for (int i = row, j = col; i < board.size() && j >= 0; i++, j--)
        if (board[i][j]) return false;

    return true;
}

bool solveNQueens(std::vector<std::vector<int>>& board, int col) {
    if (col >= board.size()) return true;

    for (int i = 0; i < board.size(); i++) {
        if (isSafe(board, i, col)) {
            board[i][col] = 1;

            if (solveNQueens(board, col + 1)) return true;

            board[i][col] = 0; // Backtrack
        }
    }

    return false;
}

int main() {
    int n = 8;
    std::vector<std::vector<int>> board(n, std::vector<int>(n, 0));

    if (solveNQueens(board, 0)) {
        for (const auto& row : board) {
            for (int cell : row) std::cout << cell << " ";
            std::cout << std::endl;
        }
    } else {
        std::cout << "No solution" << std::endl;
    }

    return 0;
}
```


## 🧮 Searching in a Sorted Array

### Linear Search in a Sorted Array
```cpp
#include <iostream>
#include <vector>

int linearSearch(const std::vector<int>& arr, int target) {
    for (int i = 0; i < arr.size(); i++) {
        if (arr[i] == target) return i;
    }
    return -1;
}

int main() {
    std::vector<int> arr = {1, 3, 5, 7, 9};
    int target = 5;
    int index = linearSearch(arr, target);
    std::cout << "Index of " << target << " is: " << index << std::endl;
    return 0;
}
```


## 🛠️ Binary Search

Binary search works only with sorted arrays. It divides the array into two parts and searches in the appropriate half until it finds the element or no elements remain to check.

### Iterative Implementation
```cpp
#include <iostream>
#include <vector>

int binarySearch(const std::vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) return mid;
        if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}

int main() {
    std::vector<int> arr = {1, 3, 5, 7, 9};
    int target = 7;
    int index = binarySearch(arr, target);
    std::cout << "Index of " << target << " is: " << index << std::endl;
    return 0;
}
```

### Recursive Implementation
```cpp
#include <iostream>
#include <vector>

int binarySearchRecursive(const std::vector<int>& arr, int left, int right, int target) {
    if (left > right) return -1;

    int mid = left + (right - left) / 2;

    if (arr[mid] == target) return mid;
    if (arr[mid] < target) return binarySearchRecursive(arr, mid + 1, right, target);

    return binarySearchRecursive(arr, left, mid - 1, target);
}

int main() {
    std::vector<int> arr = {1, 3, 5, 7, 9};
    int target = 9;
    int index = binarySearchRecursive(arr, 0, arr.size() - 1, target);
    std::cout << "Index of " << target << " is: " << index << std::endl;
    return 0;
}
```


## 🎯 Conclusion
These algorithms and approaches offer powerful tools for solving various problems in programming. Understanding their logic and effective implementation is key to writing optimized code.
# 🔁 Recursion: Principles and Depth

Recursion is a fundamental concept where a function calls itself to solve a smaller instance of the same problem. It is the basis for DFS, Divide and Conquer, and Dynamic Programming.

## 1. Anatomy of Recursion

A correct recursive function must have:
1.  **Base Case**: The condition that stops the recursion. Without it, you get infinite recursion and `Stack Overflow`.
2.  **Recursive Step**: Calling the function with parameters that move towards the base case.

### Example: Factorial
$n! = n \times (n-1)!$, base case $0! = 1$.

```cpp
long long factorial(int n) {
    if (n == 0) return 1; // Base case
    return n * factorial(n - 1); // Recursive step
}
```

## 2. The Call Stack

When a function is called, a "Stack Frame" is pushed onto the system stack. It holds local variables and the return address.
*   **Recursion Depth**: The maximum number of nested calls. Usually limited (e.g., 256MB stack allows $\approx 10^5-10^6$ calls).
*   **Stack Overflow**: Occurs when the recursion is too deep.

### Visualization `factorial(3)`
1. `factorial(3)` calls `factorial(2)`
2. `factorial(2)` calls `factorial(1)`
3. `factorial(1)` calls `factorial(0)`
4. `factorial(0)` returns `1` -> stack pops
5. `factorial(1)` returns `1*1` -> stack pops
6. `factorial(2)` returns `2*1` -> stack pops
7. `factorial(3)` returns `3*2` -> stack pops

## 3. Examples

### 3.1. Fibonacci (Naive vs DP)
$F_n = F_{n-1} + F_{n-2}$.
Naive recursion is $O(2^n)$ (Exponential!).
**Fix**: Use Memoization (store results in an array) to make it $O(N)$.

### 3.2. GCD (Euclid)
```cpp
int gcd(int a, int b) {
    if (b == 0) return a;
    return gcd(b, a % b);
}
```
This is **Tail Recursion** (recursive call is the last action). Compilers can optimize this into a loop.

### 3.3. Printing Digits
To print digits of `n` in order:
```cpp
void printDigits(int n) {
    if (n < 10) { cout << n << " "; return; }
    printDigits(n / 10);
    cout << n % 10 << " ";
}
```

### 3.4. Fast Modular Exponentiation
Compute $a^b \pmod m$ in $O(\log b)$.
```cpp
long long power(long long a, long long b, long long m) {
    if (b == 0) return 1;
    long long res = power(a, b / 2, m);
    res = (res * res) % m;
    if (b % 2 == 1) res = (res * a) % m;
    return res;
}
```

## 4. Common Pitfalls
1.  **Missing Base Case**: Immediate crash.
2.  **Global vs Local**: Large arrays inside recursive functions cause stack overflow. Use global variables or pass by reference.
3.  **Redundant Computations**: Like in naive Fibonacci. Always check if you are solving the same subproblem multiple times.

## 5. Practice
1.  Check if a string is a palindrome using recursion.
2.  Binary Search (Recursive implementation).
3.  Towers of Hanoi.
4.  Flood Fill (DFS in a grid).