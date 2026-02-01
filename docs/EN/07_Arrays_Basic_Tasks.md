# 🧮 One-dimensional Arrays and Basic Tasks

One-dimensional arrays are one of the most important data structures used for storing and processing multiple values of the same type. In this topic, we will examine in detail what they represent, how they are used, and how to solve basic tasks with them.

## 📚 One-dimensional Arrays

### What is a One-dimensional Array?

A one-dimensional array is a continuous sequence of elements that can be accessed via an index. Indices start from 0 and increment by 1 up to the array size minus one.

### Declaration and Initialization of Arrays

**Declaration:**
```cpp
type array_name[size];
```

Examples:
```cpp
int numbers[5]; // Array of 5 integers
float values[10]; // Array of 10 floating point numbers
```

Initialization:
```cpp
int numbers[5] = {1, 2, 3, 4, 5};
float values[] = {3.14, 2.71, 1.41};
```

## 🔄 Basic Tasks with One-dimensional Arrays

1. Summing Elements
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int numbers[5] = {1, 2, 3, 4, 5};
        int sum = 0;
    
        for (int i = 0; i < 5; i++) {
            sum += numbers[i];
        }
    
        cout << "Sum: " << sum << endl;
        return 0;
    }
    ```

2. Finding Maximum and Minimum Element
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int numbers[5] = {3, 7, 1, 9, 4};
        int max = numbers[0];
        int min = numbers[0];
    
        for (int i = 1; i < 5; i++) {
            if (numbers[i] > max) max = numbers[i];
            if (numbers[i] < min) min = numbers[i];
        }
    
        cout << "Maximum: " << max << ", Minimum: " << min << endl;
        return 0;
    }
    ```

3. Sorting an Array

Sorting is the process of arranging array elements in ascending or descending order.

## 🔍 Introduction to Sorting Algorithms

Sorting algorithms play a key role in data processing. The most commonly used methods are:

1. Bubble Sort
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int numbers[5] = {5, 3, 8, 6, 2};
    
        for (int i = 0; i < 5 - 1; i++) {
            for (int j = 0; j < 5 - i - 1; j++) {
                if (numbers[j] > numbers[j + 1]) {
                    swap(numbers[j], numbers[j + 1]);
                }
            }
        }
    
        for (int i = 0; i < 5; i++) {
            cout << numbers[i] << " ";
        }
        return 0;
    }
    ```

2. Selection Sort
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int numbers[5] = {5, 3, 8, 6, 2};
    
        for (int i = 0; i < 5 - 1; i++) {
            int minIndex = i;
            for (int j = i + 1; j < 5; j++) {
                if (numbers[j] < numbers[minIndex]) {
                    minIndex = j;
                }
            }
            swap(numbers[i], numbers[minIndex]);
        }
    
        for (int i = 0; i < 5; i++) {
            cout << numbers[i] << " ";
        }
        return 0;
    }
    ```

## 📝 String Manipulation Tools and Searching within Strings

What is a string?
A string is a sequence of characters processed as a single text value. In C++, strings can be processed via character arrays or via the `string` class.

### Basic String Operations

Example of basic operations:
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string text = "Hello, world!";
    cout << "String length: " << text.length() << endl;
    cout << "First character: " << text[0] << endl;
    cout << "Last character: " << text[text.length() - 1] << endl;
    return 0;
}
```

### Searching in Strings

Searching for a substring:
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string text = "Hello, world!";
    string search = "world";

    size_t found = text.find(search);
    if (found != string::npos) {
        cout << "Found at position: " << found << endl;
    } else {
        cout << "Substring not found." << endl;
    }

    return 0;
}
```

### String Comparison
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string str1 = "apple";
    string str2 = "banana";

    if (str1 < str2) {
        cout << str1 << " is before " << str2 << " in lexicographical order." << endl;
    } else {
        cout << str2 << " is before " << str1 << " in lexicographical order." << endl;
    }

    return 0;
}
```

## 🎯 Conclusion

One-dimensional arrays, sorting algorithms, and string operations are foundational concepts in programming. They provide powerful tools for working with data, searching, and sorting, which are at the core of many algorithms and applications.
