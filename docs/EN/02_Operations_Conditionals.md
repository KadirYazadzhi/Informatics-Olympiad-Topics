# ➕ Operations in C++. Conditional Operators. Compound Operator.

## Operations in C++
C++ provides a wide range of operators that can be used to perform calculations, comparisons, logical operations, and more.

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
            <td><code>+</code>, <code>-</code>, <code>*</code>, <code>/</code></td>
            <td>Used for basic mathematical operations.</td>
        </tr>
        <tr>
            <td>Logical</td>
            <td><code>&&</code>, <code>||</code>, <code>!</code></td>
            <td>Used for logical checks and conditions.</td>
        </tr>
        <tr>
            <td>Comparison Operators</td>
            <td><code>==</code>, <code>!=</code>, <code>&lt;</code>, <code>&gt;</code></td>
            <td>Compare two values and return <code>true</code> or <code>false</code>.</td>
        </tr>
        <tr>
            <td>Assignment</td>
            <td><code>=</code>, <code>+=</code>, <code>-=</code></td>
            <td>Used to assign values or modify variables.</td>
        </tr>
        <tr>
            <td>Increment/Decrement</td>
            <td><code>++</code>, <code>--</code></td>
            <td>Increase or decrease a variable's value by 1.</td>
        </tr>
    </tbody>
</table>


## Conditional Operators

Conditional operators control the execution of code based on a given condition.

### 📌 `if` statement
```cpp
if (condition) {
    // Code executes if the condition is true
}
```

Example:
```cpp
int number = 5;
if (number > 0) {
    cout << "The number is positive!" << endl;
}
```

### 📌 if-else statement
```cpp
if (condition) {
    // Code executes if the condition is true
} else {
    // Code executes if the condition is false
}
```

Example:
```cpp
int number = -3;
if (number > 0) {
    cout << "The number is positive!" << endl;
} else {
    cout << "The number is negative!" << endl;
}
```

### 📌 switch-case
```cpp
switch (value) {
    case value1:
        // Code for value1
        break;
    case value2:
        // Code for value2
        break;
    default:
        // Code for other values
}
```

Example:
```cpp
int day = 3;
switch (day) {
    case 1: cout << "Monday"; break;
    case 2: cout << "Tuesday"; break;
    case 3: cout << "Wednesday"; break;
    default: cout << "Invalid day"; break;
}
```

### Compound Operator

Compound operators are used to conveniently combine operations and assignments.

<table>
    <thead>
        <tr>
            <th>Operator</th>
            <th>Example</th>
            <th>Equivalent to</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>+=</td>
            <td>a += 5;</td>
            <td>a = a + 5;</td>
        </tr>
        <tr>
            <td>-=</td>
            <td>a -= 3;</td>
            <td>a = a - 3;</td>
        </tr>
        <tr>
            <td>*=</td>
            <td>a *= 2;</td>
            <td>a = a * 2;</td>
        </tr>
        <tr>
            <td>/=</td>
            <td>a /= 4;</td>
            <td>a = a / 4;</td>
        </tr>
        <tr>
            <td>%=</td>
            <td>a %= 2;</td>
            <td>a = a % 2;</td>
        </tr>
    </tbody>
</table>


### 🏁 Conclusion

C++ provides powerful tools for working with data and controlling program execution. Combining operators and conditional structures allows for efficient problem solving. 🚀
