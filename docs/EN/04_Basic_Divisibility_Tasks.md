# 🔢 Basic Tasks Related to Number Divisibility

## 🧮 Number Divisibility
Divisibility is a fundamental concept in mathematics and programming. It determines whether one number is divisible by another without a remainder. In programming, the `%` (remainder of division) operator is used to check for divisibility.

### Basic Divisibility Checks:

1. **Divisibility by 2**  
   A number is even if it is divisible by 2 without a remainder.  
   ```cpp
   #include <iostream>
   using namespace std;

   int main() {
       int num;
       cout << "Enter a number: ";
       cin >> num;

       if (num % 2 == 0) {
           cout << num << " is an even number." << endl;
       } else {
           cout << num << " is an odd number." << endl;
       }
       return 0;
   }
   ```

2. **Divisibility by 3**
  A number is divisible by 3 if the sum of its digits is divisible by 3.
    ```cpp
    #include <iostream>
    using namespace std;
    
    bool isDivisibleBy3(int num) {
        int sum = 0;
        while (num != 0) {
            sum += num % 10;
            num /= 10;
        }
        return sum % 3 == 0;
    }
    
    int main() {
        int num;
        cout << "Enter a number: ";
        cin >> num;
    
        if (isDivisibleBy3(num)) {
            cout << num << " is divisible by 3." << endl;
        } else {
            cout << num << " is not divisible by 3." << endl;
        }
        return 0;
    }
    ```

3. **Divisibility by 5**
  A number is divisible by 5 if it ends in 0 or 5.
    ```cpp
      #include <iostream>
      using namespace std;
    
      int main() {
          int num;
          cout << "Enter a number: ";
          cin >> num;
    
          if (num % 5 == 0) {
              cout << num << " is divisible by 5." << endl;
          } else {
              cout << num << " is not divisible by 5." << endl;
          }
          return 0;
      }
    ```

### 📚 Task Examples

**Finding all numbers in an interval that are divisible by a given number**
  ```cpp
  #include <iostream>
  using namespace std;
  
  int main() {
      int start, end, divisor;
      cout << "Enter the start of the interval: ";
      cin >> start;
      cout << "Enter the end of the interval: ";
      cin >> end;
      cout << "Enter the divisor: ";
      cin >> divisor;
  
      cout << "Numbers divisible by " << divisor << ": ";
      for (int i = start; i <= end; i++) {
          if (i % divisor == 0) {
              cout << i << " ";
          }
      }
      cout << endl;
      return 0;
  }
  ```
  
**Summing numbers that are divisible by a given number**
  ```cpp
  #include <iostream>
  using namespace std;
  
  int main() {
      int start, end, divisor, sum = 0;
      cout << "Enter the start of the interval: ";
      cin >> start;
      cout << "Enter the end of the interval: ";
      cin >> end;
      cout << "Enter the divisor: ";
      cin >> divisor;
  
      for (int i = start; i <= end; i++) {
          if (i % divisor == 0) {
              sum += i;
          }
      }
  
      cout << "The sum of the numbers divisible by " << divisor << " is: " << sum << endl;
      return 0;
  }
  ```

**Checking whether a given number is prime**
  ```cpp
  #include <iostream>
  using namespace std;
  
  bool isPrime(int num) {
      if (num < 2) return false;
      for (int i = 2; i <= num / 2; i++) {
          if (num % i == 0) return false;
      }
      return true;
  }
  
  int main() {
      int num;
      cout << "Enter a number: ";
      cin >> num;
  
      if (isPrime(num)) {
          cout << num << " is a prime number." << endl;
      } else {
          cout << num << " is not a prime number." << endl;
      }
      return 0;
  }
  ```

## 🔍 Advanced Divisibility Checks

### Divisibility by 4
A number is divisible by 4 if its last two digits form a number that is divisible by 4:
```cpp
bool isDivisibleBy4(int num) {
    int lastTwo = num % 100;
    return lastTwo % 4 == 0;
}
```

### Divisibility by 6
The number must be simultaneously divisible by 2 and by 3:
```cpp
bool isDivisibleBy6(int num) {
    return (num % 2 == 0) && (isDivisibleBy3(num));
}
```

### Divisibility by 7 (Complex check)
Method: Separate the last digit, multiply it by 2, and subtract it from the remaining number. Repeat the process:
```cpp
bool isDivisibleBy7(int num) {
    if (num < 0) num = -num;
    if (num == 0 || num == 7) return true;
    if (num < 10) return false;
    
    return isDivisibleBy7(num / 10 - 2 * (num % 10));
}
```

### Divisibility by 8
The last three digits form a number divisible by 8:
```cpp
bool isDivisibleBy8(int num) {
    int lastThree = num % 1000;
    return lastThree % 8 == 0;
}
```

### Divisibility by 9
The sum of the digits is divisible by 9:
```cpp
bool isDivisibleBy9(int num) {
    int sum = 0;
    while (num != 0) {
        sum += num % 10;
        num /= 10;
    }
    return sum % 9 == 0;
}
```

### Divisibility by 11
The difference between the sum of the digits at odd positions and the sum of the digits at even positions is divisible by 11:
```cpp
bool isDivisibleBy11(int num) {
    int oddSum = 0, evenSum = 0;
    bool isOdd = true;
    
    while (num != 0) {
        if (isOdd) {
            oddSum += num % 10;
        } else {
            evenSum += num % 10;
        }
        num /= 10;
        isOdd = !isOdd;
    }
    
    int diff = oddSum - evenSum;
    return (diff % 11 == 0);
}
```

## 🧮 Greatest Common Divisor (GCD)

The GCD of two numbers is the largest number that divides both without a remainder.

### Euclidean Algorithm
```cpp
int gcd(int a, int b) {
    while (b != 0) {
        int temp = b;
        b = a % b;
        a = temp;
    }
    return a;
}

// Recursive version
int gcdRecursive(int a, int b) {
    if (b == 0) return a;
    return gcdRecursive(b, a % b);
}

// C++17 standard function
#include <numeric>
int result = std::gcd(a, b);
```

### GCD of more than two numbers
```cpp
int gcdMultiple(vector<int>& arr) {
    int result = arr[0];
    for (int i = 1; i < arr.size(); i++) {
        result = gcd(result, arr[i]);
        if (result == 1) break; // Optimization
    }
    return result;
}
```

## 🔢 Least Common Multiple (LCM)

The LCM of two numbers is the smallest number that is divisible by both.

Formula: $\text{LCM}(a, b) = \frac{a \times b}{\text{GCD}(a, b)}$

```cpp
long long lcm(int a, int b) {
    return (long long)a * b / gcd(a, b);
}

// To avoid overflow
long long lcmSafe(int a, int b) {
    return (long long)a / gcd(a, b) * b;
}

// C++17
#include <numeric>
long long result = std::lcm(a, b);
```

## 🌟 Prime Numbers

### Primality Test (Optimized)
```cpp
bool isPrimeOptimized(int num) {
    if (num <= 1) return false;
    if (num <= 3) return true;
    if (num % 2 == 0 || num % 3 == 0) return false;
    
    // Check only numbers of the form 6k ± 1
    for (int i = 5; i * i <= num; i += 6) {
        if (num % i == 0 || num % (i + 2) == 0)
            return false;
    }
    return true;
}
```

### Finding all prime factors
```cpp
vector<int> primeFactors(int num) {
    vector<int> factors;
    
    while (num % 2 == 0) {
        factors.push_back(2);
        num /= 2;
    }
    
    for (int i = 3; i * i <= num; i += 2) {
        while (num % i == 0) {
            factors.push_back(i);
            num /= i;
        }
    }
    
    if (num > 2) {
        factors.push_back(num);
    }
    
    return factors;
}
```

### Sieve of Eratosthenes
Finding all prime numbers up to N:
```cpp
vector<bool> sieveOfEratosthenes(int n) {
    vector<bool> isPrime(n + 1, true);
    isPrime[0] = isPrime[1] = false;
    
    for (int i = 2; i * i <= n; i++) {
        if (isPrime[i]) {
            for (int j = i * i; j <= n; j += i) {
                isPrime[j] = false;
            }
        }
    }
    
    return isPrime;
}
```

## 📊 Perfect, Deficient, and Abundant Numbers

### Perfect Number
The sum of the proper divisors is equal to the number itself:
```cpp
bool isPerfect(int num) {
    int sum = 1; // 1 is always a divisor
    
    for (int i = 2; i * i <= num; i++) {
        if (num % i == 0) {
            sum += i;
            if (i != num / i) {
                sum += num / i;
            }
        }
    }
    
    return sum == num && num != 1;
}
// Examples: 6, 28, 496, 8128
```

### Abundant Number
The sum of the proper divisors is greater than the number:
```cpp
bool isAbundant(int num) {
    int sum = 1;
    for (int i = 2; i * i <= num; i++) {
        if (num % i == 0) {
            sum += i;
            if (i != num / i) sum += num / i;
        }
    }
    return sum > num;
}
// Examples: 12, 18, 20, 24
```

## 🔄 Reversing and Palindromic Numbers

### Reversing a number
```cpp
int reverseNumber(int num) {
    int reversed = 0;
    
    while (num != 0) {
        reversed = reversed * 10 + num % 10;
        num /= 10;
    }
    
    return reversed;
}
```

### Checking for a palindromic number
```cpp
bool isPalindromeNumber(int num) {
    if (num < 0) return false;
    return num == reverseNumber(num);
}
```

## 🎲 Armstrong Numbers (Narcissistic Numbers)

A number is equal to the sum of its own digits, each raised to the power of the number of digits:
```cpp
bool isArmstrong(int num) {
    int original = num;
    int sum = 0;
    int digits = to_string(num).length();
    
    while (num != 0) {
        int digit = num % 10;
        sum += pow(digit, digits);
        num /= 10;
    }
    
    return sum == original;
}
// Examples: 153 = 1³ + 5³ + 3³
//          9474 = 9⁴ + 4⁴ + 7⁴ + 4⁴
```

## 🔢 Sum and Product of Digits

### Sum of Digits
```cpp
int digitSum(int num) {
    int sum = 0;
    while (num != 0) {
        sum += num % 10;
        num /= 10;
    }
    return sum;
}
```

### Product of Digits
```cpp
int digitProduct(int num) {
    int product = 1;
    while (num != 0) {
        product *= num % 10;
        num /= 10;
    }
    return product;
}
```

## 🎯 Olympiad Tasks

### Task 1: Finding Number of Divisors
```cpp
int countDivisors(int num) {
    int count = 0;
    for (int i = 1; i * i <= num; i++) {
        if (num % i == 0) {
            count++;
            if (i != num / i) {
                count++;
            }
        }
    }
    return count;
}
```

### Task 2: Sum of Divisors
```cpp
int sumOfDivisors(int num) {
    int sum = 0;
    for (int i = 1; i * i <= num; i++) {
        if (num % i == 0) {
            sum += i;
            if (i != num / i && i != 1) {
                sum += num / i;
            }
        }
    }
    return sum;
}
```

### Task 3: Fibonacci Numbers that are Prime
```cpp
void primeFibonacci(int n) {
    long long a = 0, b = 1;
    
    for (int i = 0; i < n; i++) {
        if (isPrimeOptimized(b)) {
            cout << b << " ";
        }
        long long next = a + b;
        a = b;
        b = next;
    }
}
```

### Task 4: Amicable Numbers
Two numbers are amicable if the sum of the proper divisors of one is equal to the other:
```cpp
bool areAmicable(int a, int b) {
    return sumOfDivisors(a) == b && sumOfDivisors(b) == a;
}
// Example: 220 and 284
```

## 💡 Important Tips

1. **Divisibility Optimization**: Check only up to √n when searching for divisors.
2. **Use long long**: When multiplying large numbers to avoid overflow.
3. **Cache results**: If you check divisibility repeatedly, use a Sieve.
4. **Be careful with negative numbers**: The modulo operation may yield unexpected results.

## 🏁 Conclusion

Divisibility tasks are fundamental in programming and are frequently encountered in Olympiads. From simple parity checks to complex algorithms like the Sieve of Eratosthenes, mastering these techniques is critical for success. Practice regularly, remember divisibility rules, and experiment with various optimizations! 🚀