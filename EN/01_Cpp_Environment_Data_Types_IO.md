# 🖥️ C++ Programming Environment

## 🚀 Introduction  
C++ is one of the most powerful and widely used programming languages, combining high performance with object-oriented programming capabilities. It is used in various fields such as:

- Software Development
- Gaming Industry
- System Programming
- Embedded Systems

In this material, we will explore how to set up a programming environment, basic data types, and how to use input and output in C++.


## 🛠️ Setting up the Programming Environment

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

An IDE facilitates writing, compiling, and testing code. Popular choices for C++ include:

- 🔵 **Visual Studio** (Windows)
- 🟢 **Code::Blocks** (Windows/Linux/Mac)
- 🟣 **CLion** (all platforms, paid)
- 🖋️ **Visual Studio Code** (requires adding extensions for C++).


# 🚀 Creating and Running Your First Program

Once you have a compiler and an IDE:

1. Create a file with the `.cpp` extension (e.g., `main.cpp`).
2. Write the following code:

   ```cpp
   #include <iostream>
   using namespace std;

   int main() {
       cout << "Hello, World!" << endl;
       return 0;
   }
   ```

3. Compile the program with the command:

   ```
   g++ main.cpp -o main
   ```

4. Run the executable file:

   ```
   ./main
   ```

# 🔢 Simple Data Types in C++

C++ offers a variety of data types that you can use to store and process information.

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
      <td>Floating point number</td>
      <td>4 bytes</td>
      <td>~±3.4×10⁻³⁸ to ±3.4×10³⁸</td>
      <td><code>float b = 3.14;</code></td>
    </tr>
    <tr>
      <td><code>double</code></td>
      <td>Double precision floating point</td>
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
      <td>Boolean type (true/false)</td>
      <td>1 byte</td>
      <td><code>true</code> or <code>false</code></td>
      <td><code>bool e = true;</code></td>
    </tr>
  </tbody>
</table>


### 🔑 Example of Working with Data Types
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

C++ uses `cin` to read data from the keyboard.
Example:
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

### 📤 Output to Console

To print results, we use `cout`.
Example:
```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "Welcome to the world of programming!" << endl;
    return 0;
}
```

### 💡 Note on Formatting

`endl` is used for a new line.
You can use `\n` as an alternative.

## ✅ Basic Concepts

- Program Flow
   - Code executes from top to bottom.
   - Conditional statements like `if` and loops like `for` and `while` allow control over execution.

- Comments in C++

   - Single-line comments:
      ```cpp
      // This is a single-line comment
      ```

   - Multi-line comments:
      ```cpp
      /*
         This is a multi-line
         comment
      */
      ```

# 🏁 Conclusion

C++ is an incredibly powerful language that can be used for numerous projects – from simple programs to complex systems. With proper preparation and understanding of the basics, you can easily dive into programming. Good luck! 🚀
