# ➗ Arithmetic Expressions: Parsing and Evaluation

## 📝 Recursive Descent Parser

This is the most flexible method for processing expressions with different priorities, parentheses, and functions. We define a grammar and write a function for each priority level.

### Grammar (Example):
- **Expression**: Term + Term | Term - Term
- **Term**: Factor * Factor | Factor / Factor
- **Factor**: (Expression) | Number | -Factor

### Implementation:
```cpp
const char* p; // Current position in string

int expression();
int factor();

int term() {
    int left = factor();
    while (*p == '*' || *p == '/') {
        char op = *p++;
        int right = factor();
        if (op == '*') left *= right;
        else left /= right;
    }
    return left;
}

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

int factor() {
    if (*p == '(') {
        p++;
        int val = expression();
        p++; // skip ')'
        return val;
    }
    // Read number
    int val = 0;
    while (isdigit(*p)) {
        val = val * 10 + (*p - '0');
        p++;
    }
    return val;
}
```

---

## ⚡ Evaluating Logical Expressions

The same approach applies to boolean expressions (`&&`, `||`, `!`).
- Lowest priority: `||`
- Medium priority: `&&`
- Highest priority: `!` and `()`

It is important to implement "lazy evaluation" (short-circuiting) – if the left side of `&&` is `false`, the right side is not evaluated (important if the right side has side effects or is invalid).

---

## 🏁 Conclusion

Recursive Descent is much easier to extend than Shunting-yard. If the task requires support for functions `sin()`, `max()`, or variables, a recursive parser is the right choice.
