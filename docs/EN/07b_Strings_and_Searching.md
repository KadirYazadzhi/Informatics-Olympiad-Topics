# 📝 Strings and Searching: Foundations

Working with text is an essential part of programming. In C++, the primary tool for this is the `std::string` class.

## 1. Characters and ASCII
Computers work with numbers. Characters (`char`) are stored as codes (usually ASCII).
*   `'A'` is 65, `'a'` is 97, `'0'` is 48.
*   The difference between a lowercase and an uppercase letter is 32 (`'a' - 'A' == 32`).
*   Digit to number: `int digit = c - '0';`.

## 2. The `std::string` Class

Unlike C-style strings (`char[]`), `std::string` is dynamic, safe, and easy to use.

### 2.1. Input and Output
```cpp
string s;
cin >> s; // Reads up to the first space/newline!
```

If you want to read an entire line (including spaces):
```cpp
string line;
getline(cin, line);
```
*Important*: If you used `cin >> x` before `getline`, a newline character remains in the buffer, which `getline` will "consume" immediately (resulting in an empty string). Use `cin.ignore()` before `getline`.

### 2.2. Basic Operations
```cpp
string s = "Hello";

// 1. Length
int len = s.length(); // or s.size()

// 2. Accessing a character (0-indexed)
char c = s[0]; // 'H'
s[0] = 'h'; // "hello"

// 3. Appending
s += " World"; // "hello World"
s.push_back('!'); // "hello World!"

// 4. Substring (start, length)
string sub = s.substr(0, 5); // "hello"
string tail = s.substr(6);   // "World!" (to the end)

// 5. Erasing (start, count)
s.erase(5, 1); // Erases the space at position 5
```

## 3. Searching in a String

### 3.1. The Built-in `find` Method
Returns the index of the first occurrence or the special constant `string::npos` if not found.
```cpp
string text = "banana";
size_t pos = text.find("ana");

if (pos != string::npos) {
    cout << "Found at index: " << pos << endl; // 1
} else {
    cout << "Not found" << endl;
}

// Searching for the next occurrence
pos = text.find("ana", pos + 1); // Searches from index 2 -> finds at 3
```
The complexity of `find` in the worst case is $O(N \cdot M)$, but it usually works quickly.

### 3.2. Other Searching Methods
*   `rfind(str)`: Searches from the end towards the beginning (last occurrence).
*   `find_first_of("aeiou")`: Finds the first character that is a vowel.
*   `find_first_not_of(" ")`: Finds the first character that is NOT a space (for trim).

## 4. Converting (Numbers <-> Strings)

### From Number to String
```cpp
int x = 123;
string s = to_string(x);
```

### From String to Number
```cpp
string s = "456";
int x = stoi(s);       // String to Int
long long y = stoll(s); // String to Long Long
double z = stod(s);    // String to Double
```

## 5. Classical Tasks

### 5.1. Palindrome
Checking whether a string reads the same forwards and backwards.
```cpp
bool isPalindrome(string s) {
    int n = s.length();
    for (int i = 0; i < n / 2; i++) {
        if (s[i] != s[n - 1 - i]) return false;
    }
    return true;
}
```

### 5.2. Anagrams
Two strings are anagrams if they contain the same letters (e.g., "listen" and "silent").
Solution: Sort both strings and compare.
```cpp
bool areAnagrams(string s1, string s2) {
    sort(s1.begin(), s1.end());
    sort(s2.begin(), s2.end());
    return s1 == s2;
}
```

### 5.3. Reversing
```cpp
string s = "abc";
reverse(s.begin(), s.end()); // "cba"
// Requires #include <algorithm>
```

---

## 6. StringStream
A useful class for "parsing" strings as if reading from the console.
```cpp
#include <sstream>
// ...
string data = "Ivan 20 5.50";
stringstream ss(data);

string name;
int age;
double grade;

ss >> name >> age >> grade;
// name="Ivan", age=20, grade=5.50
```

---

## 7. Additional String Operations

### 7.1. String Tokenization
Often it is necessary to split a string into words or components. The simplest method is via `stringstream`:
```cpp
string text = "Apple Banana Cherry";
stringstream ss(text);
string word;

while (ss >> word) {
    cout << word << endl; // Apple, Banana, Cherry
}
```

To split by a specific delimiter (e.g., a comma):
```cpp
string data = "Ivan,20,Sofia";
size_t pos = 0;
while ((pos = data.find(',')) != string::npos) {
    cout << data.substr(0, pos) << endl;
    data.erase(0, pos + 1);
}
cout << data << endl; // The last element
```

### 7.2. Removing Spaces (Trim)
Input data often contain leading or trailing spaces:
```cpp
string trim(string s) {
    size_t start = s.find_first_not_of(" \t\n");
    size_t end = s.find_last_not_of(" \t\n");
    
    if (start == string::npos) return ""; // Empty string
    return s.substr(start, end - start + 1);
}
```

### 7.3. Replacing a Substring (Replace)
```cpp
string text = "Hello World";
size_t pos = text.find("World");
if (pos != string::npos) {
    text.replace(pos, 5, "C++"); // "Hello C++"
}
```

Or for replacing all occurrences:
```cpp
void replaceAll(string& str, const string& from, const string& to) {
    size_t pos = 0;
    while ((pos = str.find(from, pos)) != string::npos) {
        str.replace(pos, from.length(), to);
        pos += to.length();
    }
}
```

### 7.4. Checking for Prefix/Suffix
C++20 introduced `starts_with` and `ends_with`, but if you are using an older version:
```cpp
bool startsWith(const string& str, const string& prefix) {
    return str.size() >= prefix.size() && 
           str.substr(0, prefix.size()) == prefix;
}

bool endsWith(const string& str, const string& suffix) {
    return str.size() >= suffix.size() && 
           str.substr(str.size() - suffix.size()) == suffix;
}
```

---

## 8. String Comparison

### 8.1. Lexicographical Comparison
The `<` operator compares strings lexicographically (dictionary order):
```cpp
string a = "apple", b = "banana";
if (a < b) cout << "Apple comes before Banana"; // True
```

To ignore case (case-insensitive):
```cpp
bool compareIgnoreCase(string a, string b) {
    transform(a.begin(), a.end(), a.begin(), ::tolower);
    transform(b.begin(), b.end(), b.begin(), ::tolower);
    return a == b;
}
```

---

## 9. Practice Tasks

1. **CSES String Matching**: Basic substring search.
2. **Codeforces 71A**: Way Too Long Words (string manipulation).
3. **LeetCode 14**: Longest Common Prefix.
4. **UVa 10082**: WERTYU (character transformation).

---

## 🏁 Conclusion

`std::string` is a powerful tool for text manipulation. Mastering basic operations like `find`, `substr`, conversion, and comparison will save you a lot of time in competitions. For more advanced tasks (such as KMP, Z-algorithm, suffix arrays), see **Topic 36: String Algorithms**. 

Tips:
*   Always check the result of `find` for `string::npos`.
*   Be careful with indices in `substr` and `erase`.
*   Use `stringstream` for easy data parsing.