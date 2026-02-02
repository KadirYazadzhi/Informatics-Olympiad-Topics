# 🧮 Introduction to Combinatorial Configurations

Combinatorial configurations represent a fundamental branch of discrete mathematics concerned with studying ways of selecting, arranging, and combining elements from a given set. They play a key role in computer science, cryptography, and algorithms.


## 📋 Table of Contents

1. **Basic Concepts**
    - Permutations
    - Combinations
    - Variations
2. **Factorial and Binomial Coefficients**
    - Definition
    - Formulas
    - Examples
3. **Permutations**
    - Permutations with repeated elements
    - Permutations without repeated elements
    - Algorithms for generating permutations
4. **Combinations**
    - Difference between combinations and permutations
    - Algorithms for generating combinations
5. **Variations**
    - Variations with repetitions
    - Variations without repetitions
6. **Applications**


## 🔑 Basic Concepts

### Permutations

Permutations represent the different ways of arranging all elements of a given set. If we have a set with \( n \) elements, the number of permutations is `n!` (factorial of `n`).

#### Example: Permutations of the set `{1, 2, 3}`
```
{1, 2, 3}, {1, 3, 2}, {2, 1, 3}, {2, 3, 1}, {3, 1, 2}, {3, 2, 1}
```

### Combinations

Combinations represent the different ways of selecting `k` elements from a set with `n` elements, without taking order into account.

#### Formula:
```
C(n, k) = n! / (k! * (n-k)!)
```

### Variations

Variations are similar to permutations, but only a subset of `k` elements is used, where the order matters.

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

### Permutations without repetitions
The number of permutations of `n` distinct elements is `n!`.

#### Example:
```
P(3) = 3! = 6
```

### Permutations with repetitions
When the elements are not unique, the number of permutations is calculated using:
```
P(n; k1, k2, ..., kr) = n! / (k1! * k2! * ... * kr!)
```

#### Example:
```
P(6; 2, 2, 2) = 6! / (2! * 2! * 2!) = 90
```


## 🔍 Combinations

### Difference between combinations and permutations
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


## 🔄 Generating Permutations

### Ordered Lexicographically
```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

void generatePermutations(vector<int>& arr) {
    sort(arr.begin(), arr.end());
    
    do {
        for (int x : arr) cout << x << " ";
        cout << endl;
    } while (next_permutation(arr.begin(), arr.end()));
}

int main() {
    vector<int> arr = {1, 2, 3};
    generatePermutations(arr);
    return 0;
}
```

### Recursive Generation (Heap's Algorithm)
```cpp
void heapPermutation(vector<int>& arr, int size, int n) {
    if (size == 1) {
        for (int x : arr) cout << x << " ";
        cout << endl;
        return;
    }
    
    for (int i = 0; i < size; i++) {
        heapPermutation(arr, size - 1, n);
        
        if (size % 2 == 1)
            swap(arr[0], arr[size - 1]);
        else
            swap(arr[i], arr[size - 1]);
    }
}
```

## 🎲 Catalan Numbers

Catalan numbers appear in many combinatorial tasks:
- Number of ways to parenthesize an expression with n factors.
- Number of paths in a grid without crossing the diagonal.
- Number of binary trees with n vertices.

Formula: $C_n = \frac{1}{n+1} \binom{2n}{n} = \frac{(2n)!}{(n+1)! \cdot n!}$

```cpp
long long catalanDP(int n) {
    vector<long long> catalan(n + 1, 0);
    catalan[0] = catalan[1] = 1;
    
    for (int i = 2; i <= n; i++) {
        for (int j = 0; j < i; j++) {
            catalan[i] += catalan[j] * catalan[i - j - 1];
        }
    }
    
    return catalan[n];
}
```

## 🔢 Pascal's Triangle

The coefficients in the binomial expansion form Pascal's triangle:
```
         1
       1   1
     1   2   1
   1   3   3   1
 1   4   6   4   1
```

Property: $\binom{n}{k} = \binom{n-1}{k-1} + \binom{n-1}{k}$

```cpp
vector<vector<long long>> pascalTriangle(int n) {
    vector<vector<long long>> triangle(n);
    
    for (int i = 0; i < n; i++) {
        triangle[i].resize(i + 1);
        triangle[i][0] = triangle[i][i] = 1;
        
        for (int j = 1; j < i; j++) {
            triangle[i][j] = triangle[i-1][j-1] + triangle[i-1][j];
        }
    }
    
    return triangle;
}
```

## 🎯 Inclusion-Exclusion Principle

To find the number of elements in the union of sets:
$$|A \cup B| = |A| + |B| - |A \cap B|$$

For three sets:
$$|A \cup B \cup C| = |A| + |B| + |C| - |A \cap B| - |A \cap C| - |B \cap C| + |A \cap B \cap C|$$

**Example**: How many numbers from 1 to 100 are divisible by 2, 3, or 5?

```cpp
int divisibleBy2or3or5(int n) {
    int div2 = n / 2;
    int div3 = n / 3;
    int div5 = n / 5;
    int div6 = n / 6;  // LCM(2,3)
    int div10 = n / 10; // LCM(2,5)
    int div15 = n / 15; // LCM(3,5)
    int div30 = n / 30; // LCM(2,3,5)
    
    return div2 + div3 + div5 - div6 - div10 - div15 + div30;
}
```

## 🔄 Generating Subsets

### Bitmasks
```cpp
void generateSubsets(vector<int>& arr) {
    int n = arr.size();
    
    // 2^n subsets
    for (int mask = 0; mask < (1 << n); mask++) {
        cout << "{ ";
        for (int i = 0; i < n; i++) {
            if (mask & (1 << i)) {
                cout << arr[i] << " ";
            }
        }
        cout << "}" << endl;
    }
}
```

### Recursive Generation
```cpp
void subsetsRecursive(vector<int>& arr, vector<int>& current, int index) {
    if (index == arr.size()) {
        cout << "{ ";
        for (int x : current) cout << x << " ";
        cout << "}" << endl;
        return;
    }
    
    // Do not include the current element
    subsetsRecursive(arr, current, index + 1);
    
    // Include the current element
    current.push_back(arr[index]);
    subsetsRecursive(arr, current, index + 1);
    current.pop_back();
}
```

## 🎲 Distributions and Surjections

### Distribution of n balls into k boxes

1. **Distinguishable balls, distinguishable boxes**: $k^n$
2. **Distinguishable balls, identical boxes**: Stirling numbers of the second kind.
3. **Identical balls, distinguishable boxes**: $\binom{n+k-1}{k-1}$
4. **Identical balls, identical boxes**: Number of partitions of n.

### Stirling Numbers of the Second Kind
The number of ways to distribute n distinguishable objects into k non-empty indistinguishable groups:

```cpp
long long stirling(int n, int k) {
    vector<vector<long long>> S(n + 1, vector<long long>(k + 1, 0));
    
    S[0][0] = 1;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= min(i, k); j++) {
            S[i][j] = j * S[i-1][j] + S[i-1][j-1];
        }
    }
    
    return S[n][k];
}
```

## 🧮 Fibonacci Sequences and Combinatorics

Fibonacci numbers appear in many combinatorial tasks:
- Number of ways to climb a staircase of n steps (with steps of 1 or 2).
- Number of binary strings without two consecutive ones.

```cpp
long long countWaysToClimb(int n) {
    if (n <= 2) return n;
    
    vector<long long> fib(n + 1);
    fib[1] = 1;
    fib[2] = 2;
    
    for (int i = 3; i <= n; i++) {
        fib[i] = fib[i-1] + fib[i-2];
    }
    
    return fib[n];
}
```

## 🏆 Olympiad Tasks

1. **Number of paths in a grid**: From (0,0) to (m,n) using only moves right and up.
   - Answer: $\binom{m+n}{m}$

2. **Counting Eulerian paths**: A problem for combinatorics and graph theory.

3. **N-queens problem**: Placing n queens on an n×n chessboard such that they do not attack each other.

4. **Partitioning numbers**: In how many ways can we decompose n as a sum of positive integers?

```cpp
// Number of ways to decompose n as a sum
int partitions(int n) {
    vector<int> p(n + 1, 0);
    p[0] = 1;
    
    for (int i = 1; i <= n; i++) {
        for (int j = i; j <= n; j++) {
            p[j] += p[j - i];
        }
    }
    
    return p[n];
}
```

## 💡 Important Formulas

- $P(n) = n!$ - permutations
- $V(n, k) = \frac{n!}{(n-k)!}$ - variations
- $C(n, k) = \binom{n}{k} = \frac{n!}{k!(n-k)!}$ - combinations
- $\sum_{k=0}^n \binom{n}{k} = 2^n$ - sum of binomial coefficients
- $\binom{n}{k} = \binom{n}{n-k}$ - symmetry

## 🎯 Conclusion

Combinatorial configurations are at the foundation of algorithmic thinking and are encountered in all Olympiads. From generating permutations to calculating complex combinatorial objects, these techniques are necessary for every competitor. Practice with various tasks, remember the basic formulas, and develop an intuition for recognizing combinatorial structures in problems! 🚀