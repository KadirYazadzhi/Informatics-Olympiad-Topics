# 🔍 Sorting and String Manipulation: Advanced Techniques

In many problems, sorting and string processing go hand-in-hand. This topic covers the synergy between these two fundamental areas, focusing on lexicographical ordering, custom comparators, and efficient string sorting.

---

## 1. Lexicographical Sorting

Lexicographical order is the standard "dictionary" order. For strings $A$ and $B$:
1.  Compare $A[0]$ and $B[0]$. If $A[0] < B[0]$, then $A < B$.
2.  If equal, compare $A[1]$ and $B[1]$, and so on.
3.  If one string is a prefix of another, the shorter one is smaller.

In C++, `std::sort` on a `vector<string>` uses this order by default.

```cpp
vector<string> words = {"banana", "apple", "app"};
sort(words.begin(), words.end());
// Result: "app", "apple", "banana"
```

---

## 2. Custom Sorting for Strings

Sometimes we need a different order.

### 2.1. Sorting by Length
```cpp
sort(words.begin(), words.end(), [](const string& a, const string& b) {
    if (a.size() != b.size()) return a.size() < b.size();
    return a < b; // Lexicographical if lengths are equal
});
```

### 2.2. The "Smallest Concatenation" Problem
Given a list of strings, arrange them to form the smallest possible concatenated string.
*   **Wrong**: Sort lexicographically. (e.g., "b", "ba" -> "bba", but "bab" is smaller).
*   **Correct**: Compare $A+B$ with $B+A$.

```cpp
sort(words.begin(), words.end(), [](const string& a, const string& b) {
    return a + b < b + a;
});
```

---

## 3. Counting Sort for Strings

If we only have many strings of length 1 (characters), we use a frequency array ($O(N)$).
If we have strings of fixed length $L$, we can use **Radix Sort** to sort them in $O(N \cdot L)$.

---

## 4. Anagrams and Sorting

Two strings are anagrams if sorting their characters results in the same string.
**Task**: Group anagrams together.
1.  For each string, create a pair `{sorted_string, original_index}`.
2.  Sort the pairs.

```cpp
string s = "silent";
sort(s.begin(), s.end()); // "eilnst"
```

---

## 5. String-Specific Sorting Structures

### 5.1. Suffix Array
Sorting all suffixes of a string. This is a complex $O(N \log N)$ or $O(N \log^2 N)$ algorithm.
(See Topic 42 for detailed implementation).

### 5.2. Trie (Prefix Tree)
A Trie can be used to sort strings. An **In-order traversal** of a Trie visits strings in lexicographical order.

---

## 6. Stability in Sorting

Stability is important when sorting strings by multiple criteria (e.g., first by length, then alphabetically). Use `std::stable_sort`.

---

## 7. Practice Problems
1.  **Codeforces 637B**: Chat Order (Using map/set with custom logic).
2.  **UVa 10282**: Babelfish (Hashing vs Sorting).
3.  **CSES Grouping Anagrams**.
4.  **SPOJ smallest concatenation**.

## 8. Conclusion
Combining sorting with string logic is a common pattern. Whether it's finding the smallest rotation, grouping anagrams, or custom dictionary orders, the key is defining the correct comparator.