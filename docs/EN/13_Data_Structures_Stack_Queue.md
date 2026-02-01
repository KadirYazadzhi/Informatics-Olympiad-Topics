# 📚 Data Structures: Stack and Queue

Data structures "stack" and "queue" are fundamental elements in programming and are widely used to solve various problems. In this material, we will examine their characteristics, basic operations, and how they can be implemented via the Standard Template Library (STL) of C++.


## 📋 Content
1. **What is a Stack?**  
    - Basic Operations  
    - Example of using a stack  
2. **What is a Queue?**  
    - Basic Operations  
    - Example of using a queue  
3. **Variations of Stacks and Queues**  
    - `std::deque`  
    - Priority Queue (`std::priority_queue`)  
4. **Real-world Applications of Stack and Queue**  


## 🟢 What is a Stack?

A stack is a data structure that follows the "Last In, First Out" (LIFO) principle. This means that the last added element is the first one to be removed.

### Basic Operations
1. **`push`** - adding an element to the top of the stack.
2. **`pop`** - removing an element from the top of the stack.
3. **`top`** - retrieving the value of the top element without removing it.
4. **`empty`** - checking if the stack is empty.

### Example of Using a Stack

```cpp
#include <stack>
#include <iostream>

int main() {
    std::stack<int> stack;

    // Adding elements
    stack.push(10);
    stack.push(20);
    stack.push(30);

    // Outputting and removing elements
    while (!stack.empty()) {
        std::cout << "Top of stack: " << stack.top() << std::endl;
        stack.pop();
    }

    return 0;
}
```


## 🟡 What is a Queue?

A queue is a data structure that follows the "First In, First Out" (FIFO) principle. This means that the first added element is the first one to be removed.

### Basic Operations
1. **`push`** - adding an element to the end of the queue.
2. **`pop`** - removing an element from the beginning of the queue.
3. **`front`** - retrieving the value of the first element.
4. **`back`** - retrieving the value of the last element.
5. **`empty`** - checking if the queue is empty.

### Example of Using a Queue

```cpp
#include <queue>
#include <iostream>

int main() {
    std::queue<int> queue;

    // Adding elements
    queue.push(1);
    queue.push(2);
    queue.push(3);

    // Outputting and removing elements
    while (!queue.empty()) {
        std::cout << "Front of queue: " << queue.front() << std::endl;
        queue.pop();
    }

    return 0;
}
```


## 🔄 Variations of Stacks and Queues

### 📌 Double-ended Queue (`std::deque`)
`std::deque` is a flexible structure that allows adding and removing elements from both the beginning and the end.

**Example:**
```cpp
#include <deque>
#include <iostream>

int main() {
    std::deque<int> deque;

    deque.push_back(10); // Add to end
    deque.push_front(20); // Add to beginning

    for (int num : deque) {
        std::cout << num << " ";
    }

    return 0;
}
```

### 📊 Priority Queue (`std::priority_queue`)
`std::priority_queue` is a special structure where elements are sorted according to priority.

**Example:**
```cpp
#include <queue>
#include <iostream>

int main() {
    std::priority_queue<int> pq;

    pq.push(10);
    pq.push(5);
    pq.push(20);

    while (!pq.empty()) {
        std::cout << "Highest priority: " << pq.top() << std::endl;
        pq.pop();
    }

    return 0;
}
```


## 📈 Real-world Applications of Stack and Queue

### 1. **Stack in Parentheses Analysis**
```cpp
#include <stack>
#include <string>
#include <iostream>

bool areParenthesesBalanced(const std::string& expr) {
    std::stack<char> stack;
    for (char ch : expr) {
        if (ch == '(') {
            stack.push(ch);
        } else if (ch == ')') {
            if (stack.empty()) {
                return false;
            }
            stack.pop();
        }
    }
    return stack.empty();
}

int main() {
    std::string expr = "(a+b)*(c-d)";
    if (areParenthesesBalanced(expr)) {
        std::cout << "Parentheses are balanced." << std::endl;
    } else {
        std::cout << "Parentheses are not balanced." << std::endl;
    }
    return 0;
}
```

### 2. **Queue for Task Management**
```cpp
#include <queue>
#include <iostream>

int main() {
    std::queue<std::string> tasks;

    tasks.push("Task 1");
    tasks.push("Task 2");
    tasks.push("Task 3");

    while (!tasks.empty()) {
        std::cout << "Executing: " << tasks.front() << std::endl;
        tasks.pop();
    }

    return 0;
}
```


## 🎯 Conclusion
Data structures "stack" and "queue" are key tools for data management. Through STL, their implementation and usage become extremely efficient and easy to understand. Mastering these structures is an important step towards advanced programming.
