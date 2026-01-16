# 🔢 Basic Tasks Related to Number Divisibility

## 🧮 Number Divisibility
Divisibility is a fundamental concept in mathematics and programming. It determines whether one number divides another without a remainder. In programming, the `%` operator (modulus) is used to check for divisibility.

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

### 📚 Example Tasks

  Finding all numbers in a range that are divisible by a given number
  ```cpp
  #include <iostream>
  using namespace std;
  
  int main() {
      int start, end, divisor;
      cout << "Enter start of range: ";
      cin >> start;
      cout << "Enter end of range: ";
      cin >> end;
      cout << "Enter divisor: ";
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
  
  Summing numbers divisible by a given number
  ```cpp
  #include <iostream>
  using namespace std;
  
  int main() {
      int start, end, divisor, sum = 0;
      cout << "Enter start of range: ";
      cin >> start;
      cout << "Enter end of range: ";
      cin >> end;
      cout << "Enter divisor: ";
      cin >> divisor;
  
      for (int i = start; i <= end; i++) {
          if (i % divisor == 0) {
              sum += i;
          }
      }
  
      cout << "The sum of numbers divisible by " << divisor << " is: " << sum << endl;
      return 0;
  }
  ```

  Checking if a number is prime
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

## 🏁 Conclusion

Understanding divisibility is important for solving many programming problems. Knowing basic checks and algorithms can simplify working with numeric structures and optimize problem solving. 🚀
