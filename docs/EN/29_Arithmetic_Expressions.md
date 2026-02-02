# ➗ Arithmetic Expressions: Parsing and Evaluation

Parsing and evaluation of arithmetic expressions is a fundamental task in computer science. This topic is important for creating calculators, language interpreters, compilers, and formula processing systems.

---

## 📚 Theoretical Foundations

An arithmetic expression is a combination of:
- **Operands**: Numbers, variables, or sub-expressions.
- **Operators**: +, -, *, /, ^, etc.
- **Parentheses**: For changing the priority of operations.

### Operator Precedence (from highest to lowest):
1. Parentheses `( )`
2. Unary operators (e.g., unary `-`, `!`)
3. Exponentiation `^`
4. Multiplication and division `*`, `/`, `%`
5. Addition and subtraction `+`, `-`

### Associativity:
- **Left associativity**: `a - b - c` = `(a - b) - c`.
- **Right associativity**: `a ^ b ^ c` = `a ^ (b ^ c)`.

---

## 📝 Recursive Descent Parser

This is the most flexible and intuitive method for processing expressions with different priorities, parentheses, and functions. We define a grammar and write a function for each level of priority.

### Grammar (Example):
- **Expression**: Term `+` Term | Term `-` Term | Term
- **Term**: Factor `*` Factor | Factor `/` Factor | Factor
- **Factor**: `(` Expression `)` | Number | `-`Factor | Function`(`Expression`)`

### Full Implementation with support for unary minus:
```cpp
#include <iostream>
#include <cctype>
#include <cmath>
using namespace std;

const char* p; // Current position in the string

// Function prototypes
int expression();
int term();
int factor();

// Parsing basic elements
int factor() {
    // Skip spaces
    while (*p == ' ') p++;
    
    // Handle parentheses
    if (*p == '(') {
        p++; // Skip '('
        int val = expression();
        p++; // Skip ')'
        return val;
    }
    
    // Handle unary minus
    if (*p == '-') {
        p++;
        return -factor();
    }
    
    // Handle unary plus
    if (*p == '+') {
        p++;
        return factor();
    }
    
    // Read an integer
    int val = 0;
    while (isdigit(*p)) {
        val = val * 10 + (*p - '0');
        p++;
    }
    return val;
}

// Parsing multiplication and division
int term() {
    int left = factor();
    while (*p == '*' || *p == '/' || *p == '%') {
        char op = *p++;
        int right = factor();
        if (op == '*') left *= right;
        else if (op == '/') left /= right;
        else left %= right;
    }
    return left;
}

// Parsing addition and subtraction
int expression() {
    int left = term();
    while (*p == '+' || *p == '-') {
        char op = *p++;
        int right = term();
        if (op == '+') left += right;
        else left -= right;
    }
    return left;
}

int main() {
    string expr = "3 + 5 * (2 - 8)";
    p = expr.c_str();
    cout << "Result: " << expression() << endl; // -27
    
    expr = "10 - 2 * 3";
    p = expr.c_str();
    cout << "Result: " << expression() << endl; // 4
    
    return 0;
}
```

### Extended version with exponentiation support:
```cpp
int power() {
    int left = factor();
    if (*p == '^') {
        p++;
        int right = power(); // Right associativity
        return (int)pow(left, right);
    }
    return left;
}

// We modify term() to call power() instead of factor()
int term() {
    int left = power();
    while (*p == '*' || *p == '/') {
        char op = *p++;
        int right = power();
        if (op == '*') left *= right;
        else left /= right;
    }
    return left;
}
```

---

## 🔢 Real Number Support

To work with real numbers, we change the result type from `int` to `double`:

```cpp
double factor() {
    while (*p == ' ') p++;
    
    if (*p == '(') {
        p++;
        double val = expression();
        p++;
        return val;
    }
    
    if (*p == '-') {
        p++;
        return -factor();
    }
    
    // Read a real number
    double val = 0;
    double decimal = 0;
    int decimals = 0;
    
    // Integer part
    while (isdigit(*p)) {
        val = val * 10 + (*p - '0');
        p++;
    }
    
    // Fractional part
    if (*p == '.') {
        p++;
        while (isdigit(*p)) {
            decimal = decimal * 10 + (*p - '0');
            decimals++;
            p++;
        }
        val += decimal / pow(10, decimals);
    }
    
    return val;
}
```

---

## ⚡ Logical Expression Evaluation

The same approach applies to boolean expressions (`&&`, `||`, `!`).

### Priority:
- Lowest priority: `||` (logical OR)
- Medium priority: `&&` (logical AND)
- Highest priority: `!` (logical NOT) and `()`

### Implementation with lazy evaluation:
```cpp
bool logicalFactor();
bool logicalTerm();
bool logicalExpression();

bool logicalFactor() {
    while (*p == ' ') p++;
    
    if (*p == '!') {
        p++;
        return !logicalFactor();
    }
    
    if (*p == '(') {
        p++;
        bool val = logicalExpression();
        p++;
        return val;
    }
    
    // true/false
    if (*p == 't') {
        p += 4; // skip "true"
        return true;
    } else {
        p += 5; // skip "false"
        return false;
    }
}

bool logicalTerm() {
    bool left = logicalFactor();
    while (*p == '&' && *(p+1) == '&') {
        p += 2;
        if (!left) {
            // Lazy evaluation: if the left part is false,
            // we do not evaluate the right
            return false;
        }
        bool right = logicalFactor();
        left = left && right;
    }
    return left;
}

bool logicalExpression() {
    bool left = logicalTerm();
    while (*p == '|' && *(p+1) == '|') {
        p += 2;
        if (left) {
            // Lazy evaluation: if the left part is true,
            // we do not evaluate the right
            return true;
        }
        bool right = logicalTerm();
        left = left || right;
    }
    return left;
}
```

**Important**: Lazy evaluation is a critical optimization! If the left side of `&&` is `false`, the right side is not evaluated. This is important when:
- The right side has side effects (e.g., calling a function).
- The right side may throw an error (e.g., division by zero).

---

## 🧮 Function Support

For a calculator with built-in functions like `sin()`, `cos()`, `sqrt()`, we can add:

```cpp
double factor() {
    while (*p == ' ') p++;
    
    // Check for functions
    if (isalpha(*p)) {
        string funcName = "";
        while (isalpha(*p)) {
            funcName += *p++;
        }
        
        p++; // skip '('
        double arg = expression();
        p++; // skip ')'
        
        if (funcName == "sin") return sin(arg);
        if (funcName == "cos") return cos(arg);
        if (funcName == "sqrt") return sqrt(arg);
        if (funcName == "abs") return abs(arg);
        if (funcName == "log") return log(arg);
        
        return 0; // Unknown function
    }
    
    // ... rest of factor()
}
```

---

## 🎯 Practical Examples

### Example 1: Simple Calculator
```cpp
int main() {
    string expr;
    while (true) {
        cout << "Enter expression (or 'quit'): ";
        getline(cin, expr);
        
        if (expr == "quit") break;
        
        p = expr.c_str();
        try {
            cout << "Result: " << expression() << endl;
        } catch (...) {
            cout << "Parsing error!" << endl;
        }
    }
    return 0;
}
```

### Example 2: Balanced Parentheses Check
```cpp
bool checkBalanced(const string& expr) {
    int balance = 0;
    for (char c : expr) {
        if (c == '(') balance++;
        if (c == ')') balance--;
        if (balance < 0) return false;
    }
    return balance == 0;
}
```

---

## 🔍 Alternative Method: Shunting-yard Algorithm

Dijkstra's Shunting-yard algorithm transforms infix notation into postfix notation (Reverse Polish Notation), which is easier to evaluate.

**Advantages of Recursive Descent:**
- Easier to understand and implement.
- Naturally handles functions and variables.
- Easily extended with new operators.
- Good error messages.

**Advantages of Shunting-yard:**
- More compact code.
- Faster with many operations.
- Easily transforms into postfix form.

---

## 📋 Practice Tasks

1. **UVa 727**: Equation (Infix to Postfix).
2. **SPOJ CALC**: Simple Calculator.
3. **Codeforces 1C**: Ancient Berland Circus (calculating formulas).
4. Write a calculator with support for variables (`x = 5`, `y = x + 3`).

---

## 🏁 Conclusion

Recursive Descent is much easier to extend than Shunting-yard. If the task requires support for functions like `sin()`, `max()`, or variables, a recursive parser is the right choice. It is important to understand the connection between grammar and code structure - each priority level has its own function that calls the function for the next level.