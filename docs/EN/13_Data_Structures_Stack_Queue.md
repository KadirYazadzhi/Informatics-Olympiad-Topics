# 📚 Data Structures: Stack and Queue

The data structures "stack" and "queue" are fundamental elements in programming and are widely used to solve various problems. In this material, we will examine their characteristics, basic operations, and how they can be implemented using the Standard Template Library (STL) of C++.


## 📋 Table of Contents
1. **What is a Stack?**  
    - Basic operations  
    - Example of using a stack  
2. **What is a Queue?**  
    - Basic operations  
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
3. **`top`** - retrieving the value of the topmost element without removing it.
4. **`empty`** - checking whether the stack is empty.

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
        std::cout << "Top of the stack: " << stack.top() << std::endl;
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
5. **`empty`** - checking whether the queue is empty.

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
        std::cout << "Front of the queue: " << queue.front() << std::endl;
        queue.pop();
    }

    return 0;
}
```


## 🔄 Variations of Stacks and Queues

### 📌 Double-ended Queue (`std::deque`)
`std::deque` is a flexible structure that allows for adding and removing elements from both the beginning and the end.

**Example:**
```cpp
#include <deque>
#include <iostream>

int main() {
    std::deque<int> deque;

    deque.push_back(10); // Adding at the end
    deque.push_front(20); // Adding at the beginning

    for (int num : deque) {
        std::cout << num << " ";
    }

    return 0;
}
```

### 📊 Priority Queue (`std::priority_queue`)
`std::priority_queue` is a special structure where elements are sorted according to a priority.

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

### 1. **Balanced Parentheses Check**
A stack is ideal for checking whether parentheses are balanced in an expression:
```cpp
#include <stack>
#include <string>
#include <iostream>
using namespace std;

bool areParenthesesBalanced(const string& expr) {
    stack<char> s;
    
    for (char ch : expr) {
        if (ch == '(' || ch == '[' || ch == '{') {
            s.push(ch);
        } else if (ch == ')' || ch == ']' || ch == '}') {
            if (s.empty()) return false;
            
            char top = s.top();
            if ((ch == ')' && top == '(') ||
                (ch == ']' && top == '[') ||
                (ch == '}' && top == '{')) {
                s.pop();
            } else {
                return false;
            }
        }
    }
    
    return s.empty();
}

int main() {
    cout << areParenthesesBalanced("({[]})") << endl;  // 1 (true)
    cout << areParenthesesBalanced("([)]") << endl;    // 0 (false)
    return 0;
}
```

### 2. **Reversing a String with a Stack**
```cpp
string reverseString(string s) {
    stack<char> st;
    
    for (char c : s) {
        st.push(c);
    }
    
    string reversed = "";
    while (!st.empty()) {
        reversed += st.top();
        st.pop();
    }
    
    return reversed;
}
```

### 3. **Reverse Polish Notation (RPN) Calculator**
```cpp
int evaluateRPN(vector<string>& tokens) {
    stack<int> s;
    
    for (string& token : tokens) {
        if (token == "+" || token == "-" || token == "*" || token == "/") {
            int b = s.top(); s.pop();
            int a = s.top(); s.pop();
            
            if (token == "+") s.push(a + b);
            else if (token == "-") s.push(a - b);
            else if (token == "*") s.push(a * b);
            else s.push(a / b);
        } else {
            s.push(stoi(token));
        }
    }
    
    return s.top();
}
// Example: ["2", "1", "+", "3", "*"] -> (2 + 1) * 3 = 9
```

### 4. **BFS (Breadth-First Search) with a Queue**
```cpp
void BFS(vector<vector<int>>& graph, int start) {
    queue<int> q;
    vector<bool> visited(graph.size(), false);
    
    q.push(start);
    visited[start] = true;
    
    while (!q.empty()) {
        int node = q.front();
        q.pop();
        cout << node << " ";
        
        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}
```

### 5. **Sliding Window Maximum with a deque**
Finding the maximum in each window of size k:
```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq; // Stores indices
    vector<int> result;
    
    for (int i = 0; i < nums.size(); i++) {
        // Remove indices outside the window
        if (!dq.empty() && dq.front() == i - k) {
            dq.pop_front();
        }
        
        // Remove smaller elements from the end
        while (!dq.empty() && nums[dq.back()] < nums[i]) {
            dq.pop_back();
        }
        
        dq.push_back(i);
        
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    
    return result;
}
```

## 🔧 Implementation from Scratch

### Stack with an Array
```cpp
class Stack {
private:
    int* arr;
    int top;
    int capacity;
    
public:
    Stack(int size) {
        arr = new int[size];
        capacity = size;
        top = -1;
    }
    
    ~Stack() {
        delete[] arr;
    }
    
    void push(int x) {
        if (top == capacity - 1) {
            cout << "Stack Overflow" << endl;
            return;
        }
        arr[++top] = x;
    }
    
    int pop() {
        if (top == -1) {
            cout << "Stack Underflow" << endl;
            return -1;
        }
        return arr[top--];
    }
    
    int peek() {
        if (top == -1) {
            cout << "Stack is empty" << endl;
            return -1;
        }
        return arr[top];
    }
    
    bool isEmpty() {
        return top == -1;
    }
};
```

### Queue with an Array (Circular Queue)
```cpp
class Queue {
private:
    int* arr;
    int front, rear, size, capacity;
    
public:
    Queue(int cap) {
        capacity = cap;
        arr = new int[capacity];
        front = size = 0;
        rear = capacity - 1;
    }
    
    ~Queue() {
        delete[] arr;
    }
    
    bool isFull() {
        return size == capacity;
    }
    
    bool isEmpty() {
        return size == 0;
    }
    
    void enqueue(int x) {
        if (isFull()) {
            cout << "Queue is full" << endl;
            return;
        }
        rear = (rear + 1) % capacity;
        arr[rear] = x;
        size++;
    }
    
    int dequeue() {
        if (isEmpty()) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        int item = arr[front];
        front = (front + 1) % capacity;
        size--;
        return item;
    }
    
    int getFront() {
        if (isEmpty()) return -1;
        return arr[front];
    }
    
    int getRear() {
        if (isEmpty()) return -1;
        return arr[rear];
    }
};
```

## 🎲 Monotonic Stack and Queue

### Monotonic Stack
Used for finding the next greater/smaller element:
```cpp
// Finding the next greater element for each element
vector<int> nextGreaterElement(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> s; // Stores indices
    
    for (int i = 0; i < n; i++) {
        while (!s.empty() && nums[s.top()] < nums[i]) {
            result[s.top()] = nums[i];
            s.pop();
        }
        s.push(i);
    }
    
    return result;
}
```

### Monotonic Queue with a deque
```cpp
// Finding the minimum in a window of size k
vector<int> minSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;
    vector<int> result;
    
    for (int i = 0; i < nums.size(); i++) {
        if (!dq.empty() && dq.front() == i - k) {
            dq.pop_front();
        }
        
        while (!dq.empty() && nums[dq.back()] > nums[i]) {
            dq.pop_back();
        }
        
        dq.push_back(i);
        
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    
    return result;
}
```

## 🏆 Olympiad Tasks

### Task 1: Stock Span Problem
Finding the number of days before the current day during which the price was smaller or equal:
```cpp
vector<int> calculateSpan(vector<int>& prices) {
    int n = prices.size();
    vector<int> span(n);
    stack<int> s;
    
    for (int i = 0; i < n; i++) {
        while (!s.empty() && prices[s.top()] <= prices[i]) {
            s.pop();
        }
        
        span[i] = s.empty() ? (i + 1) : (i - s.top());
        s.push(i);
    }
    
    return span;
}
```

### Task 2: Histogram Area
Maximum rectangle in a histogram:
```cpp
int largestRectangleArea(vector<int>& heights) {
    stack<int> s;
    int maxArea = 0;
    int n = heights.size();
    
    for (int i = 0; i <= n; i++) {
        int h = (i == n) ? 0 : heights[i];
        
        while (!s.empty() && heights[s.top()] > h) {
            int height = heights[s.top()];
            s.pop();
            int width = s.empty() ? i : i - s.top() - 1;
            maxArea = max(maxArea, height * width);
        }
        s.push(i);
    }
    
    return maxArea;
}
```

### Task 3: Implement Queue using Stacks
```cpp
class MyQueue {
private:
    stack<int> s1, s2;
    
public:
    void push(int x) {
        s1.push(x);
    }
    
    int pop() {
        if (s2.empty()) {
            while (!s1.empty()) {
                s2.push(s1.top());
                s1.pop();
            }
        }
        int val = s2.top();
        s2.pop();
        return val;
    }
    
    int peek() {
        if (s2.empty()) {
            while (!s1.empty()) {
                s2.push(s1.top());
                s1.pop();
            }
        }
        return s2.top();
    }
    
    bool empty() {
        return s1.empty() && s2.empty();
    }
};
```

## 💡 Important Notes

1. **Complexity**: All basic operations (push, pop, top/front) are O(1).
2. **Memory**: Stack and queue use O(n) memory.
3. **Thread-safety**: STL containers are not thread-safe by default.
4. **Choice**: Use `stack` for LIFO, `queue` for FIFO, `deque` for double-ended access.

## 🎯 Conclusion

Stack and queue are fundamental data structures with wide application in programming. From balanced parentheses checks to graph traversal, these structures lie at the core of many algorithms. STL provides ready-made implementations, but understanding their internal mechanism is critical for solving complex tasks. Practice with different tasks and experiment with various variations! 🚀