# 🧮 Introduction to Combinatorial Configurations

Combinatorial configurations represent a major branch of discrete mathematics dealing with the study of ways to select, arrange, and combine elements of a given set. They play a key role in computer science, cryptography, and algorithms.


## 📋 Content

1. **Basic Concepts**
    - Permutations
    - Combinations
    - Variations
2. **Factorial and Binomial Coefficients**
    - Definition
    - Formulas
    - Examples
3. **Permutations**
    - Permutations with repetition
    - Permutations without repetition
    - Algorithms for generating permutations
4. **Combinations**
    - Difference between combinations and permutations
    - Algorithms for generating combinations
5. **Variations**
    - Variations with repetition
    - Variations without repetition
6. **Applications**


## 🔑 Basic Concepts

### Permutations

Permutations represent the different ways to arrange all elements of a given set. If we have a set with \( n \) elements, the number of permutations is `n!` (n factorial).

#### Example: Permutations of the set `{1, 2, 3}`
```
{1, 2, 3}, {1, 3, 2}, {2, 1, 3}, {2, 3, 1}, {3, 1, 2}, {3, 2, 1}
```

### Combinations

Combinations represent the different ways to select `k` elements from a set with `n` elements, without considering the order.

#### Formula:
```
C(n, k) = n! / (k! * (n-k)!)
```

### Variations

Variations are similar to permutations, but only a subset of `k` elements is used, where order matters.

#### Formula:
```
V(n, k) = n! / (n-k)!
```


## 🔢 Factorial and Binomial Coefficients

### Factorial
The factorial of a number `n`, denoted as `n!`, is the product of all positive integers up to `n`:
```
n! = n * (n-1) * (n-2) * ... * 1
```

#### Example:
```
5! = 5 * 4 * 3 * 2 * 1 = 120
```

### Binomial Coefficients
Binomial coefficients describe the number of combinations of `n` elements taken `k` at a time:
```
C(n, k) = n! / (k! * (n-k)!)
```

#### Example:
```
C(5, 2) = 5! / (2! * (5-2)!) = 120 / (2 * 6) = 10
```


## 🔄 Permutations

### Permutations Without Repetition
The number of permutations of `n` distinct elements is `n!`.

#### Example:
```
P(3) = 3! = 6
```

### Permutations With Repetition
When elements are not unique, the number of permutations is calculated by:
```
P(n; k1, k2, ..., kr) = n! / (k1! * k2! * ... * kr!)
```

#### Example:
```
P(6; 2, 2, 2) = 6! / (2! * 2! * 2!) = 90
```


## 🔍 Combinations

### Difference Between Combinations and Permutations
- **Combinations**: Order does not matter.
- **Permutations**: Order matters.

### Example: Generating Combinations
```cpp
#include <iostream>
#include <vector>

void generateCombinations(std::vector<int>& arr, std::vector<int>& combination, int start, int k) {
    if (k == 0) {
        for (int num : combination) std::cout << num << " ";
        std::cout << std::endl;
        return;
    }

    for (int i = start; i < arr.size(); i++) {
        combination.push_back(arr[i]);
        generateCombinations(arr, combination, i + 1, k - 1);
        combination.pop_back();
    }
}

int main() {
    std::vector<int> arr = {1, 2, 3, 4};
    int k = 2;
    std::vector<int> combination;
    generateCombinations(arr, combination, 0, k);
    return 0;
}
```


## 🎯 Conclusion
Combinatorial configurations offer a rich set of tools for calculations and problem solving. Understanding their principles is foundational for algorithms and applications in the real world.
