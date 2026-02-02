# 📚 Структура от данни: стек и опашка

Структурите от данни "стек" и "опашка" са основни елементи в програмирането и се използват широко за решаване на различни проблеми. В този материал ще разгледаме техните характеристики, основни операции и как могат да бъдат реализирани чрез стандартната библиотека (STL) на C++.


## 📋 Съдържание
1. **Какво е стек?**  
    - Основни операции  
    - Пример за използване на стек  
2. **Какво е опашка?**  
    - Основни операции  
    - Пример за използване на опашка  
3. **Вариации на стекове и опашки**  
    - `std::deque`  
    - Приоритетна опашка (`std::priority_queue`)  
4. **Реални приложения на стек и опашка**  


## 🟢 Какво е стек?

Стекът е структура от данни, която следва принципа "Last In, First Out" (LIFO). Това означава, че последният добавен елемент е първият, който се премахва.

### Основни операции
1. **`push`** - добавяне на елемент на върха на стека.
2. **`pop`** - премахване на елемент от върха на стека.
3. **`top`** - извличане на стойността на най-горния елемент без премахване.
4. **`empty`** - проверка дали стекът е празен.

### Пример за използване на стек

```cpp
#include <stack>
#include <iostream>

int main() {
    std::stack<int> stack;

    // Добавяне на елементи
    stack.push(10);
    stack.push(20);
    stack.push(30);

    // Извеждане и премахване на елементи
    while (!stack.empty()) {
        std::cout << "Върхът на стека: " << stack.top() << std::endl;
        stack.pop();
    }

    return 0;
}
```


## 🟡 Какво е опашка?

Опашката е структура от данни, която следва принципа "First In, First Out" (FIFO). Това означава, че първият добавен елемент е първият, който се премахва.

### Основни операции
1. **`push`** - добавяне на елемент в края на опашката.
2. **`pop`** - премахване на елемент от началото на опашката.
3. **`front`** - извличане на стойността на първия елемент.
4. **`back`** - извличане на стойността на последния елемент.
5. **`empty`** - проверка дали опашката е празна.

### Пример за използване на опашка

```cpp
#include <queue>
#include <iostream>

int main() {
    std::queue<int> queue;

    // Добавяне на елементи
    queue.push(1);
    queue.push(2);
    queue.push(3);

    // Извеждане и премахване на елементи
    while (!queue.empty()) {
        std::cout << "Началото на опашката: " << queue.front() << std::endl;
        queue.pop();
    }

    return 0;
}
```


## 🔄 Вариации на стекове и опашки

### 📌 Двойно свързана опашка (`std::deque`)
`std::deque` е гъвкава структура, която позволява добавяне и премахване на елементи както от началото, така и от края.

**Пример:**
```cpp
#include <deque>
#include <iostream>

int main() {
    std::deque<int> deque;

    deque.push_back(10); // Добавяне в края
    deque.push_front(20); // Добавяне в началото

    for (int num : deque) {
        std::cout << num << " ";
    }

    return 0;
}
```

### 📊 Приоритетна опашка (`std::priority_queue`)
`std::priority_queue` е специална структура, при която елементите се сортират според приоритет.

**Пример:**
```cpp
#include <queue>
#include <iostream>

int main() {
    std::priority_queue<int> pq;

    pq.push(10);
    pq.push(5);
    pq.push(20);

    while (!pq.empty()) {
        std::cout << "Най-висок приоритет: " << pq.top() << std::endl;
        pq.pop();
    }

    return 0;
}
```


## 📈 Реални приложения на стек и опашка

### 1. **Проверка на балансирани скоби**
Стекът е идеален за проверка дали скобите са балансирани в израз:
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

### 2. **Обръщане на низ със стек**
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

### 3. **Калкулатор с обратна полска нотация (RPN)**
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
// Пример: ["2", "1", "+", "3", "*"] -> (2 + 1) * 3 = 9
```

### 4. **BFS (Breadth-First Search) с опашка**
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

### 5. **Sliding Window Maximum с deque**
Намиране на максимума в всяка прозорец с размер k:
```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq; // Съхранява индекси
    vector<int> result;
    
    for (int i = 0; i < nums.size(); i++) {
        // Премахваме индекси извън прозореца
        if (!dq.empty() && dq.front() == i - k) {
            dq.pop_front();
        }
        
        // Премахваме по-малки елементи от края
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

## 🔧 Реализация от нулата

### Стек с масив
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

### Опашка с масив (Circular Queue)
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

## 🎲 Монотонен стек и опашка

### Монотонен стек
Използва се за намиране на следващ по-голям/по-малък елемент:
```cpp
// Намиране на следващ по-голям елемент за всеки елемент
vector<int> nextGreaterElement(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> s; // Съхранява индекси
    
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

### Монотонна опашка с deque
```cpp
// Намиране на минимума в прозорец с размер k
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

## 🏆 Олимпиадни задачи

### Задача 1: Stock Span Problem
Намиране на броя дни преди текущия ден, през които цената е била по-малка или равна:
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

### Задача 2: Histogram Area
Максимален правоъгълник в хистограма:
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

### Задача 3: Implement Queue using Stacks
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

## 💡 Важни забележки

1. **Сложност**: Всички основни операции (push, pop, top/front) са O(1)
2. **Памет**: Стек и опашка използват O(n) памет
3. **Thread-safety**: STL контейнерите не са thread-safe по подразбиране
4. **Избор**: Използвайте `stack` за LIFO, `queue` за FIFO, `deque` за двустранен достъп

## 🎯 Заключение

Стекът и опашката са фундаментални структури данни с широко приложение в програмирането. От проверка на балансирани скоби до обхождане на графи, тези структури са в основата на много алгоритми. STL предоставя готови имплементации, но разбирането на вътрешното им устройство е критично за решаване на сложни задачи. Практикувайте с различни задачи и експериментирайте с различните вариации! 🚀
