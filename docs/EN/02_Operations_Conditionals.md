# ➕ Operations in C++. Conditional Operators. Compound Operator.

C++ is a powerful programming language that provides a wide range of operators and control structures. Mastering these fundamental concepts is key to effective programming and solving algorithmic tasks in competitive programming.

## 1. Operations in C++

C++ provides a rich set of operators that can be used for calculations, comparisons, logical operations, and data manipulation. Understanding operator precedence and associativity is critical for writing correct code.

### 1.1. Basic Operators Table

<table>
    <thead>
        <tr>
            <th>Operator Type</th>
            <th>Example</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Arithmetic</td>
            <td><code>+</code>, <code>-</code>, <code>*</code>, <code>/</code>, <code>%</code></td>
            <td>Used for basic mathematical operations. The % operator works only with integers.</td>
        </tr>
        <tr>
            <td>Logical</td>
            <td><code>&&</code>, <code>||</code>, <code>!</code></td>
            <td>Used for logical checks and conditions. && is AND, || is OR, ! is NOT.</td>
        </tr>
        <tr>
            <td>Comparison Operators</td>
            <td><code>==</code>, <code>!=</code>, <code>&lt;</code>, <code>&gt;</code>, <code>&lt;=</code>, <code>&gt;=</code></td>
            <td>Compare two values and return <code>true</code> or <code>false</code>.</td>
        </tr>
        <tr>
            <td>Assignment</td>
            <td><code>=</code>, <code>+=</code>, <code>-=</code>, <code>*=</code>, <code>/=</code>, <code>%=</code></td>
            <td>Used for assigning values or changing variables.</td>
        </tr>
        <tr>
            <td>Increment/Decrement</td>
            <td><code>++</code>, <code>--</code></td>
            <td>Increase or decrease a variable's value by 1. There are prefix (++i) and postfix (i++) variants.</td>
        </tr>
        <tr>
            <td>Bitwise Operations</td>
            <td><code>&</code>, <code>|</code>, <code>^</code>, <code>~</code>, <code>&lt;&lt;</code>, <code>&gt;&gt;</code></td>
            <td>Work at the bit level. Very useful in competitive programming.</td>
        </tr>
        <tr>
            <td>Ternary Operator</td>
            <td><code>condition ? value1 : value2</code></td>
            <td>Shorthand for an if-else construction.</td>
        </tr>
    </tbody>
</table>

### 1.2. Details on Arithmetic Operators

```cpp
int a = 10, b = 3;
cout << a + b << endl;   // 13 - addition
cout << a - b << endl;   // 7 - subtraction
cout << a * b << endl;   // 30 - multiplication
cout << a / b << endl;   // 3 - integer division (not 3.33!)
cout << a % b << endl;   // 1 - remainder of division

// IMPORTANT: Division and remainder with negative numbers
int c = -10, d = 3;
cout << c / d << endl;   // -3
cout << c % d << endl;   // -1 (the sign follows the dividend in C++)

// For real division, use double
double x = 10.0, y = 3.0;
cout << x / y << endl;   // 3.33333...
```

### 1.3. Logical Operators and Short-circuiting

**Short-circuit evaluation**: An important optimization!

```cpp
// AND (&&): If the first condition is false, the second is not checked
if (x != 0 && y / x > 5) {  // Safe - avoids division by zero
    // code
}

// OR (||): If the first condition is true, the second is not checked
if (x == 0 || y / x < 5) {  // Safe
    // code
}

// NOT (!): Reverses the boolean value
bool flag = true;
if (!flag) {  // Will be false
    // code
}
```

### 1.4. Bitwise Operations

Bitwise operations are extremely powerful and frequently used in competitive programming:

```cpp
int a = 5;      // 0101 in binary
int b = 3;      // 0011 in binary

cout << (a & b) << endl;   // 1 - AND (0001)
cout << (a | b) << endl;   // 7 - OR (0111)
cout << (a ^ b) << endl;   // 6 - XOR (0110)
cout << (~a) << endl;      // -6 - NOT (inverts all bits)
cout << (a << 1) << endl;  // 10 - left shift (multiplication by 2)
cout << (a >> 1) << endl;  // 2 - right shift (division by 2)

// Practical applications:
// Check if a number is even
if ((n & 1) == 0) { /* even */ }

// Multiply/divide by powers of 2
int doubled = n << 1;      // n * 2
int halved = n >> 1;       // n / 2

// Check the i-th bit
bool is_set = (n & (1 << i)) != 0;

// Set the i-th bit
n |= (1 << i);

// Clear the i-th bit
n &= ~(1 << i);

// Toggle the i-th bit
n ^= (1 << i);
```

### 1.5. Operator Precedence

It is important to know precedence to avoid unexpected results:

```cpp
int result = 2 + 3 * 4;     // 14, not 20 (* has higher precedence)
bool check = 5 < 3 || 10 > 8;  // true

// Use parentheses for clarity
int result2 = (2 + 3) * 4;  // 20

// Be careful with bitwise operations and comparisons
if ((x & mask) == 0) {  // Parentheses needed because == has higher precedence than &
    // code
}
```


## 2. Conditional Operators

Conditional operators control the program's execution flow based on boolean conditions. They are the foundation of branching in algorithms.

### 2.1. if condition

The simplest form of a conditional construct:

```cpp
if (condition) {
    // Code is executed if the condition is true
}
```

Example:
```cpp
int number = 5;
if (number > 0) {
    cout << "The number is positive!" << endl;
}

// You can skip parentheses for a single expression (NOT RECOMMENDED)
if (number > 0)
    cout << "Positive!" << endl;  // Dangerous - easy to make mistakes
```

### 2.2. if-else condition

Allows alternative code if the condition is not met:

```cpp
if (condition) {
    // Code is executed if the condition is true
} else {
    // Code is executed if the condition is false
}
```

Example:
```cpp
int number = -3;
if (number > 0) {
    cout << "The number is positive!" << endl;
} else {
    cout << "The number is negative or zero!" << endl;
}

// Good practice is to always use braces, even for a single line
if (x > 0) {
    y = 1;
} else {
    y = -1;
}
```

### 2.3. if-else if-else chain

For checking multiple conditions sequentially:

```cpp
int score = 85;

if (score >= 90) {
    cout << "Excellent" << endl;
} else if (score >= 80) {
    cout << "Very Good" << endl;
} else if (score >= 70) {
    cout << "Good" << endl;
} else if (score >= 60) {
    cout << "Average" << endl;
} else {
    cout << "Poor" << endl;
}
```

### 2.4. Nested if constructs

```cpp
int age = 25;
bool hasLicense = true;

if (age >= 18) {
    if (hasLicense) {
        cout << "You can drive" << endl;
    } else {
        cout << "License needed" << endl;
    }
} else {
    cout << "You are too young" << endl;
}
```

### 2.5. Ternary operator (? :)

Shorthand form of if-else for simple assignments:

```cpp
// Syntax: condition ? value_if_true : value_if_false
int a = 5, b = 10;
int max = (a > b) ? a : b;  // max = 10

// Equivalent to:
int max;
if (a > b) {
    max = a;
} else {
    max = b;
}

// Can be nested (but don't overdo it)
int result = (x > 0) ? 1 : (x < 0) ? -1 : 0;  // signum function
```

### 2.6. switch-case construct

Efficient for many cases on a single variable:

```cpp
switch (variable) {
    case value1:
        // Code for value1
        break;  // IMPORTANT: break exits the switch
    case value2:
        // Code for value2
        break;
    case value3:
    case value4:  // We can group cases
        // Code for value3 or value4
        break;
    default:
        // Code for all other values
        break;  // Optional for default, but good practice
}
```

Examples:

```cpp
// Example 1: Days of the week
int day = 3;
switch (day) {
    case 1: cout << "Monday"; break;
    case 2: cout << "Tuesday"; break;
    case 3: cout << "Wednesday"; break;
    case 4: cout << "Thursday"; break;
    case 5: cout << "Friday"; break;
    case 6: cout << "Saturday"; break;
    case 7: cout << "Sunday"; break;
    default: cout << "Invalid day"; break;
}

// Example 2: Calculator
char op = '+';
int a = 10, b = 5;
int result;

switch (op) {
    case '+':
        result = a + b;
        break;
    case '-':
        result = a - b;
        break;
    case '*':
        result = a * b;
        break;
    case '/':
        if (b != 0) result = a / b;
        else cout << "Error: division by zero" << endl;
        break;
    default:
        cout << "Invalid operation" << endl;
}

// Example 3: Fall-through (no break - executes following cases)
int month = 2;
int days;
switch (month) {
    case 1: case 3: case 5: case 7: case 8: case 10: case 12:
        days = 31;
        break;
    case 4: case 6: case 9: case 11:
        days = 30;
        break;
    case 2:
        days = 28;  // Simplified, no leap year
        break;
    default:
        days = 0;
}
```

**Important notes for switch:**
- Works only with integers and characters (int, char, enum)
- Does NOT work with string (use if-else)
- Forgetting a break leads to "fall-through" - a common error
- It is good practice to have a default clause

## 3. Compound Assignment Operators

Compound assignment operators combine an arithmetic operation with assignment, making code more compact and often more efficient.

### 3.1. Compound Operators Table

<table>
    <thead>
        <tr>
            <th>Operator</th>
            <th>Example</th>
            <th>Equivalent To</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>+=</td>
            <td>a += 5;</td>
            <td>a = a + 5;</td>
            <td>Add and assign</td>
        </tr>
        <tr>
            <td>-=</td>
            <td>a -= 3;</td>
            <td>a = a - 3;</td>
            <td>Subtract and assign</td>
        </tr>
        <tr>
            <td>*=</td>
            <td>a *= 2;</td>
            <td>a = a * 2;</td>
            <td>Multiply and assign</td>
        </tr>
        <tr>
            <td>/=</td>
            <td>a /= 4;</td>
            <td>a = a / 4;</td>
            <td>Divide and assign</td>
        </tr>
        <tr>
            <td>%=</td>
            <td>a %= 2;</td>
            <td>a = a % 2;</td>
            <td>Remainder and assign</td>
        </tr>
        <tr>
            <td>&=</td>
            <td>a &= b;</td>
            <td>a = a & b;</td>
            <td>Bitwise AND and assign</td>
        </tr>
        <tr>
            <td>|=</td>
            <td>a |= b;</td>
            <td>a = a | b;</td>
            <td>Bitwise OR and assign</td>
        </tr>
        <tr>
            <td>^=</td>
            <td>a ^= b;</td>
            <td>a = a ^ b;</td>
            <td>Bitwise XOR and assign</td>
        </tr>
        <tr>
            <td>&lt;&lt;=</td>
            <td>a &lt;&lt;= 2;</td>
            <td>a = a &lt;&lt; 2;</td>
            <td>Left shift and assign</td>
        </tr>
        <tr>
            <td>&gt;&gt;=</td>
            <td>a &gt;&gt;= 2;</td>
            <td>a = a &gt;&gt; 2;</td>
            <td>Right shift and assign</td>
        </tr>
    </tbody>
</table>

### 3.2. Examples of Use

```cpp
int score = 100;
score += 50;     // score becomes 150
score -= 20;     // score becomes 130
score *= 2;      // score becomes 260
score /= 4;      // score becomes 65
score %= 10;     // score becomes 5

// Bitwise operations
int flags = 0b0001;
flags |= 0b0100;   // Sets bits: flags = 0b0101 (5)
flags &= 0b0111;   // Clears bits: flags = 0b0101 (5)
flags ^= 0b0011;   // Toggle bits: flags = 0b0110 (6)

// In loops - very frequently used
int sum = 0;
for (int i = 1; i <= 10; i++) {
    sum += i;  // Shorter than sum = sum + i
}

// With arrays
int count[100] = {0};
count[index]++;  // Increases the value at position index
```

### 3.3. Prefix vs Postfix Increment/Decrement

An important difference that is often overlooked:

```cpp
int a = 5;
int b = ++a;  // Prefix: first increases a, then assigns to b
              // a = 6, b = 6

int c = 5;
int d = c++;  // Postfix: first assigns to d, then increases c
              // c = 6, d = 5

// In competitive programming:
// Prefix (++i) is slightly faster in loops
for (int i = 0; i < n; ++i) {  // Preferred
    // code
}

// But the difference is negligible for int
// For iterators, however, there may be a difference:
for (auto it = v.begin(); it != v.end(); ++it) {  // Better
    // code
}
```

## 4. Practical Tips and Common Errors

### 4.1. Common Errors

```cpp
// ERROR 1: Using = instead of == in conditions
if (x = 5) {  // ERROR! This assigns 5 to x, does not compare
    // code
}
if (x == 5) {  // CORRECT
    // code
}

// ERROR 2: Forgotten break in switch
switch (x) {
    case 1:
        doSomething();
        // Forgotten break - will fall into case 2!
    case 2:
        doSomethingElse();
        break;
}

// ERROR 3: Comparing float with ==
double x = 0.1 + 0.2;
if (x == 0.3) {  // May not work due to rounding
    // code
}
// Better:
const double EPS = 1e-9;
if (abs(x - 0.3) < EPS) {  // CORRECT
    // code
}

// ERROR 4: Integer division
int a = 5, b = 2;
double result = a / b;  // result = 2.0, not 2.5!
// Correct:
double result = (double)a / b;  // or double(a) / b
```

### 4.2. Good Practices

```cpp
// Always use braces for if/else, even for a single line
if (condition) {
    doSomething();
}

// Name boolean variables clearly
bool isEmpty = (size == 0);
bool hasWon = (score >= 100);

// Use const for values that do not change
const int MAX_SIZE = 1000;
const double PI = 3.14159265359;

// Avoid magic numbers - use constants
// BAD:
if (status == 404) { ... }
// GOOD:
const int HTTP_NOT_FOUND = 404;
if (status == HTTP_NOT_FOUND) { ... }
```

### 4.3. Optimizations in Competitive Programming

```cpp
// Bitwise operations instead of arithmetic (faster)
n *= 2;  →  n <<= 1;  // Multiplication by 2
n /= 2;  →  n >>= 1;  // Division by 2
n % 2;   →  n & 1;    // Parity check

// Swap without a temporary variable (use std::swap however)
a ^= b;
b ^= a;
a ^= b;
// But it is better to use:
swap(a, b);  // Standard function

// Fast check if a number is a power of 2
bool isPowerOf2 = (n > 0) && ((n & (n - 1)) == 0);
```

### 🏁 Conclusion

C++ provides powerful tools for working with data and controlling program execution. Key points:

- **Understand operator precedence** - use parentheses when in doubt
- **Be careful with data types** - especially with division and float comparisons
- **Use the appropriate construct** - if-else for few cases, switch for many
- **Bitwise operations are powerful** - learn them well for competitive programming
- **Always use braces** with if/else - prevents many errors

Combining operators and conditional constructs allows for efficient solving of various algorithmic tasks. Practice regularly to develop intuition on which construct is most suitable! 🚀