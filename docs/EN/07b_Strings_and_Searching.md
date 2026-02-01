# 📝 Strings and Searching

Text processing is fundamental. In C++, use `std::string`.

## 1. `std::string` Basics

Dynamic, safe, and powerful.

### 1.1. I/O
```cpp
string s;
cin >> s; // Reads until whitespace
getline(cin, s); // Reads full line
```

### 1.2. Operations
*   `s.length()` or `s.size()`
*   `s += "text"`: Concatenation.
*   `s.substr(pos, len)`: Extract substring.
*   `s.push_back(c)` / `s.pop_back()`

## 2. Searching

### 2.1. `find()`
Returns index of first occurrence or `string::npos`.
Complexity: $O(NM)$ worst case.

```cpp
size_t pos = s.find("pattern");
if (pos != string::npos) { /* found */ }
```

### 2.2. Conversions
*   `to_string(123)` -> `"123"`
*   `stoi("123")` -> `123` (String to Int)
*   `stoll("123")` -> `123LL` (String to Long Long)

## 3. Classic Tasks

### 3.1. Palindrome
Check if $S$ reads same forwards and backwards.
```cpp
bool isPalindrome(const string& s) {
    int n = s.size();
    for (int i = 0; i < n/2; i++)
        if (s[i] != s[n-1-i]) return false;
    return true;
}
```

### 3.2. Anagrams
Sort both strings and compare.
`sort(s.begin(), s.end());`

### 3.3. StringStream
Useful for parsing space-separated values.
```cpp
stringstream ss("Ivan 25");
string name; int age;
ss >> name >> age;
```

## 4. ASCII and Chars
*   `'a'` to `'z'` are contiguous.
*   `int digit = c - '0';`
*   `char lower = toupper('a');`

## 5. Conclusion
For advanced searching (KMP, Z-algo), see Topic 36. For basic tasks, `std::string` methods are sufficient.