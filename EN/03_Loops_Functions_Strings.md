# 🔄 Loops, Nested Loops, Functions, and Strings in C++

## 🔄 Loops
Loops in C++ allow executing the same block of code multiple times. They are used for repetition and iterating over data structures.

### Types of Loops:

1. **for loop**:  
   Used when the number of iterations is known in advance.

   ```cpp
   #include <iostream>
   using namespace std;

   int main() {
       for (int i = 0; i < 5; i++) {
           cout << "Iteration: " << i << endl;
       }
       return 0;
   }
   ```
   
2. **while loop**:
    Used when we don't know in advance how many iterations will be needed.
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int i = 0;
        while (i < 5) {
            cout << "Iteration: " << i << endl;
            i++;
        }
        return 0;
    }
    ```
    
3. **do-while loop**:
    Guarantees execution of the block at least once, even if the condition is false.
    ```cpp
    #include <iostream>
    using namespace std;

    int main() {
        int i = 0;
        do {
            cout << "Iteration: " << i << endl;
            i++;
        } while (i < 5);
        return 0;
    }
    ```

## 🔁 Nested Loops

Nested loops are loops located inside other loops. They are useful for working with two-dimensional structures, such as matrices.
Example:
```cpp
#include <iostream>
using namespace std;

int main() {
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            cout << "i: " << i << ", j: " << j << endl;
        }
    }
    return 0;
}
```

Output:
```
i: 0, j: 0
i: 0, j: 1
i: 0, j: 2
...
i: 2, j: 2
```

## 🔧 Functions in C++

Functions allow dividing the program into smaller modules, making the code more organized and easier to maintain.

### Function Definition:

```cpp
#include <iostream>
using namespace std;

// Function definition
int add(int a, int b) {
    return a + b;
}

int main() {
    int result = add(5, 7);
    cout << "Result: " << result << endl;
    return 0;
}
```

### Types of Functions:

- Functions without arguments and return value:
  ```cpp
  void greet() {
      cout << "Hello, World!" << endl;
  }
  ```

- Functions with arguments:
  ```cpp
  void printNumber(int num) {
      cout << "Number: " << num << endl;
  }
  ```

- Functions with a return value:
  ```cpp
  int square(int x) {
      return x * x;
  }
  ```
  
## 📝 Strings

Strings are sequences of characters. In C++, there are two main types of strings:

C-style strings (character arrays):
```cpp
#include <iostream>
using namespace std;

int main() {
    char str[] = "Hello";
    cout << str << endl;
    return 0;
}
```

Standard strings (std::string):
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string greeting = "Hello, World!";
    cout << greeting << endl;
    return 0;
}
```

Basic string operations:

- Concatenation:
  ```cpp
  string first = "Hello";
  string second = " World";
  string result = first + second;
  cout << result << endl; // Hello World
  ```

- String length:
  ```cpp
  string text = "Programming";
  cout << "Length: " << text.length() << endl;
  ```

- Accessing characters:
  ```cpp
  string word = "C++";
  cout << word[0] << endl; // C
  ```

## 🏁 Conclusion

Loops, functions, and strings are fundamental components of C++ programming. Understanding and using them correctly will help you build efficient and well-structured programs. 🚀
