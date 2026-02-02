# 🔁 Recursion: Principles and Depth

Recursion is a fundamental concept in programming where a function calls itself to solve a smaller instance of the same problem. It is at the heart of algorithms for graph traversal (DFS), Divide and Conquer, Dynamic Programming, and more.

## 1. Anatomy of Recursion

To work correctly, every recursive function must contain two mandatory components:

1.  **Base Case (Recursion Depth Base)**:
    *   This is the simplest case of the task that can be solved directly, without further calls.
    *   It serves to terminate the recursion.
    *   *Its absence leads to an infinite loop and a `Stack Overflow` error.*

2.  **Recursive Step**:
    *   The logic that divides the task into smaller subtasks.
    *   Calls the function with values that bring it closer to the base case.

### Example: Factorial
$n! = n \times (n-1)!$, with the base condition $0! = 1$.

```cpp
long long factorial(int n) {
    // 1. Base case
    if (n == 0) return 1;
    
    // 2. Recursive step
    return n * factorial(n - 1);
}
```

## 2. How does it work "under the hood"? (Call Stack)

When you call a function, the computer allocates a block of memory in the **Stack** (Call Stack), called a "Stack Frame". It stores:
*   The values of local variables.
*   The function parameters.
*   The return address (where the program should continue after completion).

In recursion, these frames are stacked on top of each other.

### Visualization of `factorial(3)`:

1.  `main` calls `factorial(3)`. A frame for $n=3$ enters the stack.
2.  `factorial(3)` calls `factorial(2)`. A new frame on top for $n=2$.
3.  `factorial(2)` calls `factorial(1)`. A new frame for $n=1$.
4.  `factorial(1)` calls `factorial(0)`. A new frame for $n=0$.
5.  `factorial(0)` hits the base case and returns `1`. The frame for $n=0$ is destroyed.
6.  `factorial(1)` receives `1`, calculates $1 \times 1 = 1$, and returns `1`. The frame is destroyed.
7.  `factorial(2)` receives `1`, calculates $2 \times 1 = 2$, and returns `2`.
8.  `factorial(3)` receives `2`, calculates $3 \times 2 = 6$, and returns `6`. The stack is empty.

**Stack Limit**: In competitions, the stack is usually limited (e.g., 256MB), which allows for about $10^5$ to $10^6$ nested calls (depending on the number of variables in the function).

## 3. Examples of Recursion

### 3.1. Fibonacci Numbers (Classical example and warning)
$F_n = F_{n-1} + F_{n-2}$, $F_0=0, F_1=1$.

```cpp
int fib(int n) {
    if (n <= 1) return n;
    return fib(n - 1) + fib(n - 2);
}
```
**Problem**: This implementation has exponential complexity $O(2^n)$ because it recalculates the same values multiple times. For $N=50$, it would work for millions of years.
**Solution**: Memoization (storing results) or iteration. This is the connection with Dynamic Programming.

### 3.2. Greatest Common Divisor (Euclid)
Euclid's algorithm is naturally recursive.
$\\gcd(a, b) = \\gcd(b, a \\% b)$, base: $\\gcd(a, 0) = a$.

```cpp
int gcd(int a, int b) {
    if (b == 0) return a;
    return gcd(b, a % b);
}
```
This is an example of **Tail Recursion** – the recursive call is the *last* action in the function. Compilers can easily optimize this into a loop, saving memory.

### 3.3. Printing the Digits of a Number
If we want to print the digits of the number 12345 backwards, it is easy with a loop (`n % 10`). But in the correct order?

```cpp
void printDigits(int n) {
    if (n < 10) {
        cout << n << " ";
        return;
    }
    printDigits(n / 10); // First print the leading ones
    cout << n % 10 << " "; // Then the current one
}
```

### 3.4. Fast Modular Exponentiation
Calculating $a^b \pmod m$ in $O(\\log b)$.
Idea:
*   If $b$ is even: $a^b = (a^{b/2})^2$
*   If $b$ is odd: $a^b = a \cdot a^{b-1}$

```cpp
long long power(long long a, long long b, long long m) {
    if (b == 0) return 1;
    long long temp = power(a, b / 2, m);
    long long res = (temp * temp) % m;
    if (b % 2 == 1) res = (res * a) % m;
    return res;
}
```

## 4. Common Errors

1.  **Missing base case**: The program "freezes" and crashes with a "Segmentation fault" (due to Stack Overflow).
2.  **Wrong base case**: For example, in factorial, if you omit `if (n == 0)`, it will continue to `factorial(-1)`.
3.  **Local vs. Global variables**:
    *   If you define an array `int arr[100000]` *inside* a recursive function, the stack will overflow almost immediately. Large structures should be global or passed by reference (`vector<int>& v`).
4.  **Too deep recursion**: If $N=10^7$, recursion is not suitable. Use a loop.

## 5. Practice Tasks
1.  Write a recursive function to check whether a string is a palindrome.
2.  Write a recursive function for binary search.
3.  Towers of Hanoi (classical task).
4.  Flood Fill (filling an area in a matrix).

Recursion requires practice to "see" the solution. Start with small examples and draw the call tree on paper.

---

## 6. Recursion vs. Iteration

Every recursive function can be transformed into an iterative one (by using a stack). Sometimes iteration is faster and uses less memory.

### Transformation Examples

**Recursive Factorial:**
```cpp
long long factorial(int n) {
    if (n == 0) return 1;
    return n * factorial(n - 1);
}
```

**Iterative Factorial:**
```cpp
long long factorial(int n) {
    long long result = 1;
    for (int i = 2; i <= n; i++) {
        result *= i;
    }
    return result;
}
```

**Recursive Fibonacci with Memoization:**
```cpp
long long memo[100];

long long fib(int n) {
    if (n <= 1) return n;
    if (memo[n] != -1) return memo[n];
    return memo[n] = fib(n-1) + fib(n-2);
}
```

**Iterative Fibonacci:**
```cpp
long long fib(int n) {
    if (n <= 1) return n;
    long long prev2 = 0, prev1 = 1;
    for (int i = 2; i <= n; i++) {
        long long current = prev1 + prev2;
        prev2 = prev1;
        prev1 = current;
    }
    return prev1;
}
```

### When to choose iteration?
*   When the recursion depth is large ($> 10^5$).
*   When performance is critical (iteration avoids the overhead of function calls).
*   When the solution with a loop is more intuitive.

### When to choose recursion?
*   Tasks with a tree-like structure (tree traversal, graphs).
*   "Divide and Conquer" algorithms (Merge Sort, Quick Sort, Binary Search on a tree).
*   When the problem is recursive by nature (Towers of Hanoi, combinatorics).

---

## 7. Backtracking

Backtracking is a special form of recursion where we try all possible solutions, and when we reach a dead end, we "go back" and try another path.

### Examples:
*   **N-Queens problem**: Place $N$ queens on an $N \times N$ chessboard such that they do not attack each other.
*   **Sudoku solving**.
*   **Generating all subsets**.

### Template:
```cpp
void backtrack(int position, vector<int>& current) {
    // Base case: we found a valid solution
    if (isComplete(current)) {
        printSolution(current);
        return;
    }
    
    // We try every possible step
    for (int choice : getPossibleChoices(position)) {
        if (isValid(choice, current)) {
            current.push_back(choice); // Choose
            backtrack(position + 1, current); // Recursion
            current.pop_back(); // Go back (undoing)
        }
    }
}
```

---

## 8. Additional Examples

### 8.1. Sum of Digits
```cpp
int digitSum(int n) {
    if (n == 0) return 0;
    return n % 10 + digitSum(n / 10);
}
```

### 8.2. Reversing a Linked List
```cpp
struct Node {
    int val;
    Node* next;
};

Node* reverse(Node* head) {
    if (!head || !head->next) return head;
    Node* newHead = reverse(head->next);
    head->next->next = head;
    head->next = nullptr;
    return newHead;
}
```

---

## 🏁 Conclusion

Recursion is a powerful tool, but it requires careful use. Key principles are:
*   Always define a clear base case.
*   Ensure that each recursive call approaches the base case.
*   Be careful with the recursion depth and use memoization where possible.

Practice with different tasks to develop an intuition for when recursion is the most appropriate approach!
