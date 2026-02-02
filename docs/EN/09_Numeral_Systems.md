# 🔢 Numeral Systems

Numeral systems are a way of representing numbers using symbols. Computers work in the binary system, but programmers often use hexadecimal. Understanding numeral systems is key to working with bitwise operations, memory optimization, and solving various Olympiad tasks.

## 1. Basic Systems

### 1.1. Decimal System (Base 10)
This is the system we use in everyday life. It uses 10 digits: 0-9.
*   **Example**: $123 = 1 \cdot 10^2 + 2 \cdot 10^1 + 3 \cdot 10^0 = 100 + 20 + 3$
*   Each position represents a power of 10 (ones, tens, hundreds, etc.).

### 1.2. Binary System (Base 2)
The primary system for computers. It uses only 2 digits: 0 and 1 (bits).
*   **Example**: $1011_2 = 1 \cdot 2^3 + 0 \cdot 2^2 + 1 \cdot 2^1 + 1 \cdot 2^0 = 8 + 0 + 2 + 1 = 11_{10}$
*   Each bit represents a power of 2.
*   Widely used for bitmasks and flags.

### 1.3. Octal System (Base 8)
Uses 8 digits: 0-7. Often encountered in Unix file permissions.
*   **Example**: $752_8 = 7 \cdot 8^2 + 5 \cdot 8^1 + 2 \cdot 8^0 = 448 + 40 + 2 = 490_{10}$
*   In C++, octal numbers are written with the prefix 0: `0752`.

### 1.4. Hexadecimal System (Base 16)
Uses 16 symbols: 0-9 and A-F (where A=10, B=11, C=12, D=13, E=14, F=15).
*   **Example**: $1A3_{16} = 1 \cdot 16^2 + 10 \cdot 16^1 + 3 \cdot 16^0 = 256 + 160 + 3 = 419_{10}$
*   A compact representation of binary data (1 hex digit = 4 bits).
*   In C++, hex numbers are written with the prefix 0x: `0x1A3`.
*   Widely used for web colors (e.g., #FF5733), memory addresses.

## 2. Converting Between Numeral Systems

### 2.1. From Decimal to Base $K$
We use division with a remainder - the classical algorithm.

**Algorithm:**
1.  Divide the number by $K$.
2.  Write down the remainder (this is the last digit in the new base).
3.  Repeat with the quotient until it becomes 0.
4.  The result consists of the written remainders in **reverse order**.

**Example: 156₁₀ → base 16**
```
156 ÷ 16 = 9, remainder 12 (C)
  9 ÷ 16 = 0, remainder 9
Result: 9C₁₆
```

**Implementation with support for negative numbers:**
```cpp
string toBase(long long n, int base) {
    if (n == 0) return "0";
    
    string chars = "0123456789ABCDEF";
    string res = "";
    bool negative = n < 0;
    n = abs(n);
    
    while (n > 0) {
        res += chars[n % base];
        n /= base;
    }
    
    if (negative) res += '-';
    reverse(res.begin(), res.end());
    return res;
}

// Examples of usage:
// toBase(255, 2)  -> "11111111"
// toBase(255, 16) -> "FF"
// toBase(64, 8)   -> "100"
// toBase(-42, 2)  -> "-101010"
```

### 2.2. From Base $K$ to Decimal
We use **Horner's method** or simple summation of powers.

**Example: 1A₁₆ → decimal**
```
"1A" (16) = 1 · 16¹ + 10 · 16⁰ = 16 + 10 = 26₁₀
```

**Method 1: Summation of powers (right to left)**
```cpp
long long toDec(string s, int base) {
    long long res = 0;
    long long power = 1;
    
    for (int i = s.length() - 1; i >= 0; i--) {
        int digit;
        if (isdigit(s[i])) 
            digit = s[i] - '0';
        else 
            digit = toupper(s[i]) - 'A' + 10;
        
        res += digit * power;
        power *= base;
    }
    return res;
}
```

**Method 2: Horner's method (left to right) - more efficient**
```cpp
long long toDecHorner(string s, int base) {
    long long res = 0;
    
    for (char c : s) {
        int digit;
        if (isdigit(c)) 
            digit = c - '0';
        else 
            digit = toupper(c) - 'A' + 10;
        
        res = res * base + digit; // Horner's method
    }
    return res;
}

// Built-in function in C++:
long long val = stoll(s, nullptr, base); // base from 2 to 36
```

**Examples:**
```cpp
toDec("1011", 2)   // 11
toDec("FF", 16)    // 255
toDec("377", 8)    // 255
toDec("100", 10)   // 100
```

### 2.3. Direct Conversion Between Arbitrary Bases
```cpp
// Conversion from base A to base B via decimal
string convertBase(string num, int fromBase, int toBase) {
    // Step 1: From fromBase to decimal
    long long decimal = toDecHorner(num, fromBase);
    
    // Step 2: From decimal to toBase
    return toBase(decimal, toBase);
}

// Example: convertBase("FF", 16, 2) -> "11111111"
```

## 3. Fast Conversions Between Powers of 2

Between bases that are powers of 2 ($2^1=2, 2^3=8, 2^4=16$), one can convert **directly** by grouping bits, without passing through the decimal system.

### 3.1. Binary → Hexadecimal (2 → 16)
We group by **4 bits** from right to left (because $2^4 = 16$).

**Example:**
```
11010101₂
Grouping: 1101 0101
           D    5
Result: D5₁₆
```

**Conversion table:**
```
0000₂ = 0₁₆    0100₂ = 4₁₆    1000₂ = 8₁₆    1100₂ = C₁₆
0001₂ = 1₁₆    0101₂ = 5₁₆    1001₂ = 9₁₆    1101₂ = D₁₆
0010₂ = 2₁₆    0110₂ = 6₁₆    1010₂ = A₁₆    1110₂ = E₁₆
0011₂ = 3₁₆    0111₂ = 7₁₆    1011₂ = B₁₆    1111₂ = F₁₆
```

### 3.2. Hexadecimal → Binary (16 → 2)
Each hex digit becomes **exactly 4 bits**.

**Example:**
```
2F₁₆
2₁₆ = 0010₂
F₁₆ = 1111₂
Result: 00101111₂
```

### 3.3. Binary → Octal (2 → 8)
We group by **3 bits** from right to left (because $2^3 = 8$).

**Example:**
```
11010101₂
Grouping: 011 010 101
           3   2   5
Result: 325₈
```

### 3.4. Octal → Binary (8 → 2)
Each octal digit becomes **exactly 3 bits**.

**Example:**
```
57₈
5₈ = 101₂
7₈ = 111₂
Result: 101111₂
```

## 4. Working with Numeral Systems in C++

### 4.1. Input/Output Manipulators
```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    int x = 255;
    
    // Output in different bases
    cout << dec << x << endl;      // 255 (decimal)
    cout << hex << x << endl;      // ff (hexadecimal, lowercase)
    cout << uppercase << hex << x << endl; // FF (uppercase)
    cout << oct << x << endl;      // 377 (octal)
    
    // Showing the prefix (0x for hex, 0 for oct)
    cout << showbase << hex << x << endl;  // 0xff
    cout << oct << x << endl;              // 0377
    
    // Returning to decimal system (important!)
    cout << dec << x << endl;      // 255
    
    // Reading in different bases
    int a, b, c;
    cin >> hex >> a;  // Expects hex input (e.g., FF)
    cin >> oct >> b;  // Expects oct input (e.g., 377)
    cin >> dec >> c;  // Returns to decimal (e.g., 255)
    
    return 0;
}
```

### 4.2. Prefixes for Literals
```cpp
int decimal = 42;          // Decimal
int binary = 0b101010;     // Binary (C++14 and newer)
int octal = 052;           // Octal (prefix 0)
int hex = 0x2A;            // Hexadecimal (prefix 0x)

// All are equal to 42₁₀
```

### 4.3. Conversion Functions
```cpp
#include <string>
#include <bitset>

// String to number in different bases
int num1 = stoi("101010", nullptr, 2);    // from binary
int num2 = stoi("52", nullptr, 8);        // from octal
int num3 = stoi("2A", nullptr, 16);       // from hex
long long num4 = stoll("FFFF", nullptr, 16); // for larger numbers

// Number to string
string bin = bitset<8>(42).to_string();   // "00101010"

// Using stringstream
#include <sstream>
stringstream ss;
ss << hex << 255;
string hexStr = ss.str();  // "ff"
```

## 5. Practical Applications and Typical Tasks

### 5.1. Checking Whether a Number is Valid in Base K
```cpp
bool isValidInBase(string num, int base) {
    for (char c : num) {
        int digit;
        if (isdigit(c)) 
            digit = c - '0';
        else 
            digit = toupper(c) - 'A' + 10;
        
        // The digit must be smaller than the base
        if (digit >= base) return false;
    }
    return true;
}

// isValidInBase("189", 8) -> false (9 >= 8)
// isValidInBase("177", 8) -> true
```

### 5.2. Finding the Sum of Digits in Base K
```cpp
int digitSum(long long n, int base) {
    int sum = 0;
    while (n > 0) {
        sum += n % base;
        n /= base;
    }
    return sum;
}

// digitSum(255, 10) -> 2+5+5 = 12
// digitSum(255, 16) -> F+F = 15+15 = 30
```

### 5.3. Palindrome in Different Bases
```cpp
bool isPalindromeInBase(long long n, int base) {
    string repr = toBase(n, base);
    string rev = repr;
    reverse(rev.begin(), rev.end());
    return repr == rev;
}

// 585₁₀ = 1001001001₂ (palindrome in binary)
// 45₁₀ = 2D₁₆ (not a palindrome in hex)
```

### 5.4. Counting Ones in Binary Representation
```cpp
// Method 1: Via conversion to string
int countOnes_v1(long long n) {
    return __builtin_popcountll(n); // Built-in function
}

// Method 2: Manual counting
int countOnes_v2(long long n) {
    int count = 0;
    while (n > 0) {
        count += n % 2;
        n /= 2;
    }
    return count;
}

// Method 3: Brian Kernighan's Algorithm (fastest)
int countOnes_v3(long long n) {
    int count = 0;
    while (n) {
        n &= (n - 1); // Removes the rightmost 1 bit
        count++;
    }
    return count;
}
```

## 6. Important Boundaries and Edge Cases

### 6.1. Overflow
```cpp
// ATTENTION: Overflow with large numbers!
string binary = "1111111111111111111111111111111111111111111111111111111111111111"; // 64 ones
// This cannot fit into long long (64 bits)!

// Solution 1: Use unsigned long long
unsigned long long maxULL = ULLONG_MAX; // 2^64 - 1

// Solution 2: BigInteger library or string operations
// Solution 3: Work only with string representation without converting to a number
```

### 6.2. Special Cases
```cpp
// Zero in different bases
toBase(0, 2)  -> "0"
toBase(0, 16) -> "0"

// Negative numbers (require special handling)
toBase(-15, 2) -> "-1111"

// Leading zeros are ignored
toDec("007", 8) == toDec("7", 8) // true

// Validation check
isValidInBase("1G9", 16) -> true (Wait, G is valid in base 17+, but here base is 16? Correct would be false!)
isValidInBase("1Z9", 16) -> false (Z > F, invalid)
```

### 6.3. Bases Beyond Standard Ones
```cpp
// Base 36 - uses 0-9 and A-Z
string base36 = toBase(1000000, 36); // "LFLS"

// Base 62 - uses 0-9, A-Z, a-z (often for URL shortening)
// Requires extended chars table: "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
```

## 7. Optimization Tricks

### 7.1. Fast Doubling and Division by 2 in Binary
```cpp
// Instead of n * 2 use bitwise shift
int doubled = n << 1;  // Multiplication by 2

// Instead of n / 2
int halved = n >> 1;   // Division by 2

// Check if the number is even
bool isEven = (n & 1) == 0;  // Faster than n % 2 == 0
```

### 7.2. Fast Raising to a Power of 2
```cpp
// Check if n is a power of 2
bool isPowerOf2 = (n > 0) && ((n & (n - 1)) == 0);

// Finding the next power of 2
int nextPowerOf2(int n) {
    n--;
    n |= n >> 1;
    n |= n >> 2;
    n |= n >> 4;
    n |= n >> 8;
    n |= n >> 16;
    return n + 1;
}
```

## 8. Practice Tasks

### 8.1. Olympiad Tasks
1.  **Codeforces 492C**: Vanya and Exams (requires long long).
2.  **UVa 10018**: Reverse and Add (palindromes in different bases).
3.  **SPOJ ADDREV**: Adding Reversed Numbers.
4.  **Codeforces 1017C**: The Phone Number (working with digits).
5.  **AtCoder ABC192 - C**: All 4-digit Numbers (generation in bases).

### 8.2. Classical Problems
*   **IP address conversion** (4 bytes ↔ 32-bit number)
*   **RGB to hex and vice versa** (#FF5733 ↔ rgb(255, 87, 51))
*   **Base64 encoding/decoding** (base 64 with a special table)
*   **ISBN/Barcode validation** (specific algorithms with modular operations)

## 🏁 Conclusion

**Key points to remember:**
*   Always check for **overflow** when working with large numbers.
*   For numbers over 64 bits, use a **BigInteger** library or work with **strings**.
*   Bases that are powers of 2 (2, 4, 8, 16) allow for **direct conversions** without the decimal system.
*   **Bitwise operations** are much faster than arithmetic ones for powers of 2.
*   Be careful with **leading zeros** in the octal system (0777 != 777).
*   In competitions, use built-in functions (`stoll`, `bitset`, manipulators) for **fewer errors**.

**Tips for Olympiads:**
*   Read the task description carefully - which base is used by default.
*   Always test with edge cases: 0, 1, maximum number.
*   When in doubt, use `long long` instead of `int`.
*   Remember that `unsigned` types can store $2	imes$ larger positive numbers.
