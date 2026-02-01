# 📝 Strings and Text Searching

Strings are sequences of characters. In competitive programming, they are a source of some of the most difficult and interesting problems.

## 🏗️ std::string in C++
The `std::string` class automatically manages memory and provides powerful methods:
- `s.size()` / `s.length()`: Length.
- `s += t`: Concatenation.
- `s.find(sub)`: Substring search ($O(N \cdot M)$).
- `s.substr(pos, len)`: Substring extraction.

## 🔍 Substring Search Algorithms

### 1. Naive Search ($O(N \cdot M)$)
Check every possible starting position. Sufficient for small strings.

### 2. KMP (Knuth-Morris-Pratt) - Intro
Uses a prefix function to avoid redundant checks of already matched characters. Allows search in **linear time $O(N)$**. (Detailed in Topic 36).

## 🛠️ Useful Techniques
- **Comparison**: Strings are compared lexicographically. ` "apple" < "banana" `.
- **Stringstream**: Used for easy parsing of words from a sentence.
```cpp
stringstream ss(sentence);
string word;
while (ss >> word) { /* processing */ }
```

## ⚠️ Common Errors
- **`s.find()` returns `string::npos`**: Always check against this constant before using the result as an index.
- **Out of bounds**: Accessing `s[s.length()]` is invalid.

---

## 🏁 Conclusion
Strings require attention to characters and boundaries. For advanced problems, look into polynomial hashing and suffix structures.
