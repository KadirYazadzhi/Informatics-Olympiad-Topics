# 🔁 Recursion: Principles and Depth

Recursion is a technique where a function calls itself. It is the basis for solving tree-structure tasks and dynamic programming.

## 🏗️ Anatomy of Recursion
For recursion to be correct, it must have:
1. **Base Case**: Condition where recursion stops. Without it, `Stack Overflow` occurs.
2. **Recursive Step**: Calling the function with parameters that move the state closer to the base case.

## 🧠 Call Stack
Every recursive call takes memory in the system stack.
- **Recursion Depth**: Maximum number of nested calls. Limit is around $10^5 - 10^6$ in competitions.
- **Tail Recursion**: Special type of recursion that some compilers can optimize to iteration.

## 🚀 Direct vs Indirect Recursion
- **Direct**: $A() \to A()$
- **Indirect**: $A() \to B() \to A()$

---

## 🏁 Conclusion
Recursion makes code clean and easy to read, but must be used carefully due to stack overflow risk. For large inputs, always consider an iterative version.
