# 🔄 Loops, Nested Loops, Functions, and Strings in C++

## 🔄 Loops
Loops in C++ allow for the execution of the same block of code multiple times. They are used for handling repetitions and iterating over data structures.

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
    Used when we do not know in advance how many iterations will be needed.
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
    Guarantees the execution of the block at least once, even if the condition is false.
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

Result:
```cpp
i: 0, j: 0
i: 0, j: 1
i: 0, j: 2
...
i: 2, j: 2
```

## 🔧 Functions in C++

Functions allow for dividing the program into smaller modules, making the code more organized and easier to maintain.

### Defining a Function:

```cpp
#include <iostream>
using namespace std;

// Defining a function
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

- **Functions without arguments and return value**:
  ```cpp
  void greet() {
      cout << "Hello, world!" << endl;
  }
  ```

- **Functions with arguments**:
  ```cpp
  void printNumber(int num) {
      cout << "Number: " << num << endl;
  }
  ```

- **Functions with a return value**:
  ```cpp
    int square(int x) {
        return x * x;
    }
  ```
  
## 📝 Strings

Strings are sequences of characters. In C++, there are two main types of strings:

**C-style strings (character arrays):**
```cpp
#include <iostream>
using namespace std;

int main() {
    char str[] = "Hello";
    cout << str << endl;
    return 0;
}
```

**Standard strings (std::string):**
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string greeting = "Hello, world!";
    cout << greeting << endl;
    return 0;
}
```

**Basic string operations:**

- **Concatenation**:
  ```cpp
  string first = "Hello";
  string second = " world";
  string result = first + second;
  cout << result << endl; // Hello world
  ```

- **String length**:
  ```cpp
  string text = "Programming";
  cout << "Length: " << text.length() << endl;
  ```

- **Accessing characters**:
  ```cpp
  string word = "C++";
  cout << word[0] << endl; // C
  ```

### 4. **Range-based for loop (C++11)**
A modern and convenient syntax for iterating over containers:
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> numbers = {1, 2, 3, 4, 5};
    
    for (int num : numbers) {
        cout << num << " ";
    }
    cout << endl;
    
    // To modify elements, use a reference
    for (int& num : numbers) {
        num *= 2;
    }
    
    return 0;
}
```

### 5. **break and continue**
- `break`: Terminates the execution of the loop
- `continue`: Skips the current iteration and proceeds to the next one

```cpp
// Example with break
for (int i = 1; i <= 10; i++) {
    if (i == 5) break; // Stops at i = 5
    cout << i << " ";
}
// Output: 1 2 3 4

// Example with continue
for (int i = 1; i <= 5; i++) {
    if (i == 3) continue; // Skips 3
    cout << i << " ";
}
// Output: 1 2 4 5
```

## 🎯 Practical Tasks with Loops

### Sum of numbers from 1 to N
```cpp
int sumToN(int n) {
    int sum = 0;
    for (int i = 1; i <= n; i++) {
        sum += i;
    }
    return sum;
    // Faster: return n * (n + 1) / 2;
}
```

### Factorial
```cpp
long long factorial(int n) {
    long long result = 1;
    for (int i = 2; i <= n; i++) {
        result *= i;
    }
    return result;
}
```

### Fibonacci Numbers
```cpp
void fibonacci(int n) {
    long long a = 0, b = 1;
    
    for (int i = 0; i < n; i++) {
        cout << a << " ";
        long long next = a + b;
        a = b;
        b = next;
    }
}
```

### Palindrome Check (using loops)
```cpp
bool isPalindrome(string s) {
    int left = 0, right = s.length() - 1;
    
    while (left < right) {
        if (s[left] != s[right]) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

## 🔁 Nested Loops - Advanced Applications

### Pattern Printing
```cpp
// Triangle of asterisks
void printTriangle(int n) {
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= i; j++) {
            cout << "* ";
        }
        cout << endl;
    }
}

// Diamond
void printDiamond(int n) {
    // Upper half
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n - i; j++) cout << " ";
        for (int j = 1; j <= 2 * i - 1; j++) cout << "*";
        cout << endl;
    }
    // Lower half
    for (int i = n - 1; i >= 1; i--) {
        for (int j = 1; j <= n - i; j++) cout << " ";
        for (int j = 1; j <= 2 * i - 1; j++) cout << "*";
        cout << endl;
    }
}
```

### Multiplication Table
```cpp
void multiplicationTable(int n) {
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cout << i * j << "\t";
        }
        cout << endl;
    }
}
```

## 🔧 Functions - Advanced Concepts

### Pass by Value vs Pass by Reference
```cpp
// By value - creates a copy
void incrementValue(int x) {
    x++; // Modifies only the copy
}

// By reference - works with the original
void incrementReference(int& x) {
    x++; // Modifies the original variable
}

int main() {
    int a = 5;
    incrementValue(a);
    cout << a << endl; // 5 (unchanged)
    
    incrementReference(a);
    cout << a << endl; // 6 (modified)
}
```

### Functions with Default Values
```cpp
int power(int base, int exp = 2) {
    int result = 1;
    for (int i = 0; i < exp; i++) {
        result *= base;
    }
    return result;
}

int main() {
    cout << power(5) << endl;    // 25 (5^2)
    cout << power(5, 3) << endl; // 125 (5^3)
}
```

### Function Overloading
```cpp
int add(int a, int b) {
    return a + b;
}

double add(double a, double b) {
    return a + b;
}

string add(string a, string b) {
    return a + b;
}
```

### Recursive Functions - Basics
```cpp
// Factorial recursively
int factorialRecursive(int n) {
    if (n <= 1) return 1;
    return n * factorialRecursive(n - 1);
}

// Exponentiation recursively
int powerRecursive(int base, int exp) {
    if (exp == 0) return 1;
    return base * powerRecursive(base, exp - 1);
}
```

## 📝 Strings - Advanced Operations

### Iterating over a String
```cpp
string text = "Programming";

// Classical way
for (int i = 0; i < text.length(); i++) {
    cout << text[i] << " ";
}

// Range-based for
for (char c : text) {
    cout << c << " ";
}
```

### Comparing Strings
```cpp
string s1 = "abc";
string s2 = "abd";

if (s1 == s2) cout << "Equal" << endl;
if (s1 < s2) cout << "s1 is lexicographically smaller" << endl;
```

### Searching in a String
```cpp
string text = "Programming in C++";

// Finding a substring
size_t pos = text.find("in");
if (pos != string::npos) {
    cout << "Found at position: " << pos << endl;
}

// Checking if starts/ends with a substring
if (text.substr(0, 4) == "Prog") {
    cout << "Starts with 'Prog'" << endl;
}
```

### Modifying Strings
```cpp
string s = "hello";

// Uppercase
for (char& c : s) {
    c = toupper(c);
}
// s = "HELLO"

// Lowercase
for (char& c : s) {
    c = tolower(c);
}

// Reversing
reverse(s.begin(), s.end());

// Insertion
s.insert(2, "XYZ"); // Inserts at position 2

// Deletion
s.erase(2, 3); // Deletes 3 characters from position 2
```

### Tokenization
```cpp
#include <sstream>

string text = "apple banana cherry";
stringstream ss(text);
string word;

while (ss >> word) {
    cout << word << endl;
}
```

### Numbers and Strings
```cpp
// String to number
string numStr = "12345";
int num = stoi(numStr);        // string to int
long long bigNum = stoll(numStr); // string to long long
double d = stod("3.14");       // string to double

// Number to string
int x = 42;
string str = to_string(x);
```

## 🎯 Practical Tasks with Strings

### Counting Vowels and Consonants
```cpp
int countVowels(string s) {
    int count = 0;
    string vowels = "aeiouAEIOU";
    
    for (char c : s) {
        if (vowels.find(c) != string::npos) {
            count++;
        }
    }
    return count;
}
```

### Removing Spaces
```cpp
string removeSpaces(string s) {
    string result = "";
    for (char c : s) {
        if (c != ' ') {
            result += c;
        }
    }
    return result;
}
```

### Word Count
```cpp
int countWords(string s) {
    int count = 0;
    bool inWord = false;
    
    for (char c : s) {
        if (c != ' ' && !inWord) {
            count++;
            inWord = true;
        } else if (c == ' ') {
            inWord = false;
        }
    }
    return count;
}
```

### Anagram Check
```cpp
bool areAnagrams(string s1, string s2) {
    if (s1.length() != s2.length()) return false;
    
    sort(s1.begin(), s1.end());
    sort(s2.begin(), s2.end());
    
    return s1 == s2;
}
```

## 💡 Efficiency Tips

1. **Use the right loop**: `for` for a known number of iterations, `while` for unknown.
2. **Avoid redundant checks**: Place conditions outside the loop where possible.
3. **Be careful with nested loops**: Complexity grows quickly (O(n²), O(n³)).
4. **Pass strings by reference**: `void func(const string& s)` to avoid copying.
5. **Pre-reserve memory**: `string.reserve()` if you know the approximate size.

## 🏁 Conclusion

Loops, functions, and strings are the fundamental building blocks of programming. Mastering these concepts is critical for solving algorithmic tasks. Practice regularly with different problems, experiment with various approaches, and gradually you will develop intuition for effectively using these tools! 🚀