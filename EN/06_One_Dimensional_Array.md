# 🧮 One-dimensional Array in C++

A one-dimensional array is a fundamental data structure used to store multiple values of the same type. It is a contiguous sequence of elements that can be accessed via indices.


## 📝 Definition and Declaration of a One-dimensional Array

A one-dimensional array is declared using the following syntax:
```cpp
type array_name[size];
```

Example:
```cpp
int numbers[5]; // Array of 5 integers
```

### Initialization:

Arrays can be initialized at the time of declaration.
```cpp
int numbers[5] = {10, 20, 30, 40, 50}; // Initialization with specific values
```

If the number of elements is omitted, the compiler calculates it automatically:
```cpp
int numbers[] = {10, 20, 30, 40, 50}; // Size will be 5
```

## 📦 Accessing Array Elements

Array elements are accessed via an index, starting from 0.

Example:
```cpp
#include <iostream>
using namespace std;

int main() {
    int numbers[5] = {10, 20, 30, 40, 50};
    cout << "First element: " << numbers[0] << endl;
    cout << "Third element: " << numbers[2] << endl;

    return 0;
}
```

## 🔄 Assignment and Modification of Values

Values in the array can be changed using the index:
```cpp
numbers[0] = 100; // Change the first element
numbers[4] = 500; // Change the last element
```

## 📚 Basic Operations with One-dimensional Array

1. Traversing the array

Arrays can be traversed using a loop.

Example:
```cpp
#include <iostream>
using namespace std;

int main() {
    int numbers[5] = {10, 20, 30, 40, 50};

    for (int i = 0; i < 5; i++) {
        cout << "Element " << i << ": " << numbers[i] << endl;
    }

    return 0;
}
```

2. Finding Maximum and Minimum Value
```cpp
#include <iostream>
using namespace std;

int main() {
    int numbers[5] = {10, 20, 5, 40, 15};
    int max = numbers[0];
    int min = numbers[0];

    for (int i = 1; i < 5; i++) {
        if (numbers[i] > max) max = numbers[i];
        if (numbers[i] < min) min = numbers[i];
    }

    cout << "Maximum: " << max << endl;
    cout << "Minimum: " << min << endl;

    return 0;
}
```
3. Summing Elements
```cpp
#include <iostream>
using namespace std;

int main() {
    int numbers[5] = {10, 20, 30, 40, 50};
    int sum = 0;

    for (int i = 0; i < 5; i++) {
        sum += numbers[i];
    }

    cout << "Sum of elements: " << sum << endl;

    return 0;
}
```

## 🧪 Common Errors and Solutions

1. Out of Bounds

Accessing an index outside valid boundaries leads to unexpected behavior.
```cpp
int numbers[5];
cout << numbers[5]; // Error: Index is out of bounds
```

> Solution: Ensure the index is in the range [0, size - 1].

## 🚀 Applications of One-dimensional Array

- Storing data: For example, test scores, temperatures over a period, etc.
- Processing sequences: For example, calculating average value, filtering, etc.
- Programming algorithms: For example, sorting, searching, and other basic operations.

## 🏁 Conclusion

The one-dimensional array is a powerful tool for working with multiple data points. With it, you can store, manipulate, and analyze values efficiently. With practice, you will develop intuition for when and how to use arrays in real programs. 🎯
