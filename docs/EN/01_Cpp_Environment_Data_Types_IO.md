# 🖥️ C++ Programming Environment

## 🚀 Introduction  
C++ is one of the most powerful and widely used programming languages, combining high performance with object-oriented programming capabilities. It is used in various fields such as:  

- Software Development  
- Gaming Industry  
- System Programming  
- Embedded Systems  

In this material, we will examine how to prepare a programming environment, basic data types, and how to use input and output in C++.


## 🛠️ Preparing the Programming Environment  

1. **Installing a Compiler**  
   A compiler is a tool that translates your C++ code into an executable file. The most commonly used compilers are:  
   - 🖥️ **GCC (GNU Compiler Collection)** - a popular choice for Linux.  
   - 🖥️ **Clang** - a modern compiler, often used with macOS.  
   - 🖥️ **MSVC (Microsoft Visual C++)** - part of Visual Studio.  

   **Example of installing GCC on Linux:**  
   ```bash
   sudo apt update
   sudo apt install build-essential
   ```

# 🖥️ Choosing an Integrated Development Environment (IDE)

An IDE facilitates writing, compiling, and testing code. The most popular choices for C++ include:

- 🔵 **Visual Studio** (Windows)  
- 🟢 **Code::Blocks** (Windows/Linux/Mac)  
- 🟣 **CLion** (all platforms, paid)  
- 🖋️ **Visual Studio Code** (requires adding C++ extensions).  


# 🚀 Creating and Running Your First Program

Once you have a compiler and a development environment:

1. Create a file with a `.cpp` extension (e.g., `main.cpp`).  
2. Write the following code:  

   ```cpp
   #include <iostream>
   using namespace std;

   int main() {
       cout << "Hello, world!" << endl;
       return 0;
   }
   ```

3. Compile the program with the command:

   ```
   g++ main.cpp -o main
   ```

4. Run the executable:

   ```
   ./main
   ```

# 🔢 Simple Data Types in C++

In C++, there is a variety of data types that you can use for storing and processing information.

<table>
  <thead>
    <tr>
      <th>Data Type</th>
      <th>Description</th>
      <th>Size</th>
      <th>Range</th>
      <th>Example</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>int</code></td>
      <td>Integer</td>
      <td>4 bytes</td>
      <td>-2,147,483,648 to 2,147,483,647</td>
      <td><code>int a = 42;</code></td>
    </tr>
    <tr>
      <td><code>float</code></td>
      <td>Floating-point number</td>
      <td>4 bytes</td>
      <td>~±3.4×10⁻³⁸ to ±3.4×10³⁸</td>
      <td><code>float b = 3.14;</code></td>
    </tr>
    <tr>
      <td><code>double</code></td>
      <td>Double precision floating-point</td>
      <td>8 bytes</td>
      <td>~±1.7×10⁻³⁰⁸ to ±1.7×10³⁰⁸</td>
      <td><code>double c = 2.71828;</code></td>
    </tr>
    <tr>
      <td><code>char</code></td>
      <td>Character (ASCII)</td>
      <td>1 byte</td>
      <td>0 to 255</td>
      <td><code>char d = 'A';</code></td>
    </tr>
    <tr>
      <td><code>bool</code></td>
      <td>Boolean (true/false)</td>
      <td>1 byte</td>
      <td><code>true</code> or <code>false</code></td>
      <td><code>bool e = true;</code></td>
    </tr>
  </tbody>
</table>


### 🔑 Example of working with data types
```cpp
#include <iostream>
using namespace std;

int main() {
    int number = 10;
    float pi = 3.14;
    char letter = 'A';
    bool isTrue = true;

    cout << "Number: " << number << endl;
    cout << "Pi: " << pi << endl;
    cout << "Letter: " << letter << endl;
    cout << "Boolean value: " << isTrue << endl;

    return 0;
}
```

## 🔤 Program Input and Output

### 📥 User Input

C++ uses `cin` for reading data from the keyboard.

**Reading a single number:**
```cpp
#include <iostream>
using namespace std;

int main() {
    int age;
    cout << "Enter your age: ";
    cin >> age;
    cout << "You are " << age << " years old." << endl;
    return 0;
}
```

**Reading multiple variables:**
```cpp
int a, b;
cout << "Enter two numbers: ";
cin >> a >> b;
cout << "The sum is: " << (a + b) << endl;
```

**Reading a string with spaces:**
```cpp
string name;
cout << "Enter your name: ";
getline(cin, name);  // Reads an entire line
cout << "Hello, " << name << "!" << endl;
```

### 📤 Console Output

For displaying results, we use `cout`.

**Basic example:**
```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "Welcome to the world of programming!" << endl;
    return 0;
}
```

**Formatting output:**
```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double pi = 3.14159265359;
    
    // Setting precision
    cout << fixed << setprecision(2);
    cout << "Pi is approximately: " << pi << endl;  // 3.14
    
    // Setting width
    cout << setw(10) << "Name" << setw(10) << "Age" << endl;
    cout << setw(10) << "Ivan" << setw(10) << 25 << endl;
    
    return 0;
}
```

### 💡 Note on Formatting

- `endl` is used for a new line and flushing the buffer
- `\n` is a faster alternative (does not flush the buffer)
- `flush` flushes the buffer without adding a new line

```cpp
cout << "Text" << endl;   // New line + flush
cout << "Text\n";         // New line only
cout << "Text" << flush;  // Flush only
```

## 📁 Working with Files

### Reading from a File
```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    ifstream input("input.txt");
    
    if (!input.is_open()) {
        cout << "Error opening file!" << endl;
        return 1;
    }
    
    int number;
    while (input >> number) {
        cout << number << " ";
    }
    
    input.close();
    return 0;
}
```

### Writing to a File
```cpp
#include <fstream>
using namespace std;

int main() {
    ofstream output("output.txt");
    
    output << "Result: " << 42 << endl;
    output << "Successfully written!" << endl;
    
    output.close();
    return 0;
}
```

## 🔧 Modifiers and Data Types

### Additional Types
```cpp
// Large numbers
long long bigNum = 1000000000000LL;

// Unsigned types
unsigned int positiveOnly = 4000000000U;

// Short and long
short smallNum = 32767;
long int longNum = 2147483647L;

// Type sizes
cout << "int: " << sizeof(int) << " bytes" << endl;
cout << "double: " << sizeof(double) << " bytes" << endl;
```

### Constants
```cpp
const double PI = 3.14159;
const int MAX_SIZE = 100;

// C++11 constexpr for compile-time constants
constexpr int DAYS_IN_WEEK = 7;
```

### Automatic Type Deduction (C++11)
```cpp
auto x = 5;        // int
auto y = 3.14;     // double
auto z = "text";   // const char*
auto s = string("hello");  // string
```

## 🎯 Practical Examples

### Calculator
```cpp
#include <iostream>
using namespace std;

int main() {
    double a, b;
    char op;
    
    cout << "Enter expression (e.g., 5 + 3): ";
    cin >> a >> op >> b;
    
    switch(op) {
        case '+': cout << "Result: " << (a + b) << endl; break;
        case '-': cout << "Result: " << (a - b) << endl; break;
        case '*': cout << "Result: " << (a * b) << endl; break;
        case '/':
            if (b != 0)
                cout << "Result: " << (a / b) << endl;
            else
                cout << "Error: Division by zero!" << endl;
            break;
        default:
            cout << "Invalid operation!" << endl;
    }
    
    return 0;
}
```

### Temperature Converter
```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double celsius;
    cout << "Enter temperature in Celsius: ";
    cin >> celsius;
    
    double fahrenheit = celsius * 9.0 / 5.0 + 32;
    double kelvin = celsius + 273.15;
    
    cout << fixed << setprecision(2);
    cout << celsius << "°C = " << fahrenheit << "°F" << endl;
    cout << celsius << "°C = " << kelvin << "K" << endl;
    
    return 0;
}
```

### Multiplication Table
```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    int n;
    cout << "Enter a number: ";
    cin >> n;
    
    cout << "Multiplication table for " << n << ":" << endl;
    for (int i = 1; i <= 10; i++) {
        cout << setw(2) << n << " x " << setw(2) << i 
             << " = " << setw(3) << (n * i) << endl;
    }
    
    return 0;
}
```

## ✅ Basic Concepts

### Program Flow
- Code is executed from top to bottom
- Conditional statements (`if`, `else`, `switch`) change the flow
- Loops (`for`, `while`, `do-while`) repeat execution

### Operators
```cpp
// Arithmetic
int sum = 5 + 3;      // 8
int diff = 5 - 3;     // 2
int prod = 5 * 3;     // 15
int quot = 5 / 3;     // 1 (integer division)
int mod = 5 % 3;      // 2 (remainder)

// Increment/Decrement
int x = 5;
x++;  // x = 6 (post-increment)
++x;  // x = 7 (pre-increment)

// Assignment operators
x += 5;  // x = x + 5
x *= 2;  // x = x * 2
```

### Comments in C++

**Single-line comments:**
```cpp
// This is a single-line comment
int x = 5;  // Comment after code
```

**Multi-line comments:**
```cpp
/* 
   This is a multi-line comment
   It can span multiple lines
   Useful for documentation
*/
```

### Variables and Scopes
```cpp
#include <iostream>
using namespace std;

int globalVar = 10;  // Global variable

int main() {
    int localVar = 5;  // Local variable
    
    {
        int blockVar = 3;  // Variable within an inner block
        cout << blockVar << endl;  // OK
    }
    
    // cout << blockVar << endl;  // ERROR! blockVar is not visible
    cout << localVar << endl;     // OK
    cout << globalVar << endl;    // OK
    
    return 0;
}
```

## 🛡️ Error Handling

### Input Validation
```cpp
#include <iostream>
#include <limits>
using namespace std;

int main() {
    int num;
    cout << "Enter a number: ";
    
    while (!(cin >> num)) {
        cout << "Invalid input! Try again: ";
        cin.clear();  // Clears error flags
        cin.ignore(numeric_limits<streamsize>::max(), '\n');  // Ignores bad input
    }
    
    cout << "You entered: " << num << endl;
    return 0;
}
```

## 💡 Efficiency Tips

1. **Use appropriate types**: `int` for integers, `double` for fractions
2. **Avoid unnecessary copies**: Use references for large objects
3. **Optimize I/O**: For competitive programming, use:
   ```cpp
   ios_base::sync_with_stdio(false);
   cin.tie(NULL);
   ```
4. **Reuse variables**: Do not create new variables if not necessary

## 🏁 Conclusion

C++ is a powerful language with rich programming capabilities. A proper understanding of data types, input/output, and basic concepts is critical for success in programming. With practice and experimentation, you will develop confidence in using C++ to solve a variety of tasks. Good luck! 🚀