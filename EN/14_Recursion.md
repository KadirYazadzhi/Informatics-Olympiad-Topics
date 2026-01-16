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
