# 📝 Strings and Basic Searching: Foundations

String manipulation is one of the most common tasks in programming contests. In C++, the `std::string` class provides a dynamic and robust way to handle text.

---

## 1. The `std::string` Class

Unlike C-style strings (`char[]`), `std::string` manages its own memory and provides many safety features.

### 1.1. Input and Output
```cpp
string s;
cin >> s; // Reads until the first whitespace (space, tab, newline)
getline(cin, s); // Reads an entire line, including spaces
```
*Note*: If using `getline` after `cin >> x`, always call `cin.ignore()` to consume the leftover newline character.

### 1.2. Common Operations
*   **Size**: `s.size()` or `s.length()`. Complexity: $O(1)$.
*   **Access**: `s[i]` (0-indexed). Complexity: $O(1)$.
*   **Append**: `s += " more text"` or `s.push_back('!')`.
*   **Substring**: `s.substr(pos, len)`. Returns a string starting at `pos` with length `len`. Complexity: $O(len)$.
*   **Erase**: `s.erase(pos, len)`. Removes characters. Complexity: $O(N)$.

---

## 2. Searching in Text

### 2.1. Linear Searching (`find`)
The `s.find(sub)` method returns the index of the first occurrence of `sub` in `s`, or `string::npos` if not found.

```cpp
string text = "competitive programming";
size_t pos = text.find("pro");
if (pos != string::npos) {
    cout << "Found at: " << pos << endl;
}
```

### 2.2. Reverse Search
`s.rfind(sub)` searches from right to left.

### 2.3. Character Categories
Functions from `<cctype>` are useful:
*   `isalpha(c)`, `isdigit(c)`, `isspace(c)`.
*   `toupper(c)`, `tolower(c)`.

---

## 3. String Conversions

### 3.1. Numbers to Strings
`to_string(value)` works for `int`, `long long`, `double`, etc.

### 3.2. Strings to Numbers
*   `stoi(s)` -> `int`
*   `stoll(s)` -> `long long`
*   `stod(s)` -> `double`

---

## 4. Fundamental Algorithms

### 4.1. Palindrome Check
A string is a palindrome if it reads the same backwards.
```cpp
bool isPalindrome(const string& s) {
    for (int i = 0; i < s.size() / 2; i++) {
        if (s[i] != s[s.size() - 1 - i]) return false;
    }
    return true;
}
```

### 4.2. Anagram Check
Two strings are anagrams if they contain the same characters with the same frequencies.
1.  **Sorting Method**: Sort both strings and compare ($O(N \log N)$).
2.  **Frequency Array Method**: Use an array `cnt[256]` to count occurrences ($O(N)$).

---

## 5. StringStream for Parsing
The `stringstream` class from `<sstream>` allows you to treat a string like an input stream (like `cin`).

```cpp
string data = "Alice 20 5.5";
stringstream ss(data);
string name; int age; double score;
ss >> name >> age >> score;
```

---

## 6. Advanced Searching (KMP and Hashing)
For large inputs where multiple searches are needed, naive searching is too slow ($O(NM)$). Instead, use:
*   **KMP Algorithm**: $O(N+M)$ using a prefix function.
*   **Rolling Hash**: $O(N+M)$ using polynomial hashing.
(See Topic 36 for details).

## 7. Practice Problems
1.  **CSES Word Combinations**: String DP.
2.  **Codeforces 118A**: String Task.
3.  **UVa 10018**: Reverse and Add.
4.  **LeetCode 5**: Longest Palindromic Substring.

## 8. Conclusion
Mastering `std::string` is the first step in string processing. Pay close attention to index ranges and potential overflows during conversions.
