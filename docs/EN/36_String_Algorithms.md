# 🧵 String Algorithms

Efficient text processing is a key area. Standard search (`std::string::find`) is $O(N \cdot M)$ in the worst case, which is slow. Specialized algorithms achieve $O(N + M)$. 

## 1. Polynomial Hashing (Rolling Hash)

The most flexible method. We transform strings into numbers such that comparison occurs in $O(1)$.
Formula for the hash of string $S$:
$H(S) = (S[0] \cdot P^0 + S[1] \cdot P^1 + \dots + S[n-1] \cdot P^{n-1}) \pmod M$

*   $P$: Base (e.g., 31, 53, or a random number > alphabet size).
*   $M$: A large prime number (e.g., $10^9 + 7$). 

### Rolling Hash Property
We can calculate the hash of any substring $S[i \dots j]$ in $O(1)$ if we have precalculated the prefixes:
$Hash(i, j) = (H[j] - H[i-1]) \cdot P^{-i} \pmod M$
Instead of division by $P^i$, we usually multiply the right side of the equation to avoid the modular inverse.

**Double Hash**: To avoid collisions (different strings with the same hash), we use two pairs $(P_1, M_1)$ and $(P_2, M_2)$. The probability of a collision becomes negligible.

### Full Implementation:

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class StringHash {
    const long long P = 31;
    const long long M = 1e9 + 7;
    vector<long long> hash_val, pow_p;
    int n;
    
public:
    StringHash(const string& s) {
        n = s.size();
        hash_val.resize(n + 1, 0);
        pow_p.resize(n + 1, 1);
        
        for (int i = 0; i < n; i++) {
            hash_val[i + 1] = (hash_val[i] + (s[i] - 'a' + 1) * pow_p[i]) % M;
            pow_p[i + 1] = (pow_p[i] * P) % M;
        }
    }
    
    // Hash of substring [l, r]
    long long getHash(int l, int r) {
        long long result = (hash_val[r + 1] - hash_val[l] + M) % M;
        return result;
    }
    
    // Comparing two substrings
    bool equals(int l1, int r1, int l2, int r2) {
        int len1 = r1 - l1 + 1;
        int len2 = r2 - l2 + 1;
        if (len1 != len2) return false;
        
        long long h1 = getHash(l1, r1);
        long long h2 = getHash(l2, r2);
        
        // Normalization for comparison
        if (l1 < l2) {
            h1 = (h1 * pow_p[l2 - l1]) % M;
        } else {
            h2 = (h2 * pow_p[l1 - l2]) % M;
        }
        
        return h1 == h2;
    }
};

int main() {
    string s = "abracadabra";
    StringHash sh(s);
    
    // Check whether "abra" occurs at positions 0 and 7
    cout << "Substring [0,3] == [7,10]: " 
         << sh.equals(0, 3, 7, 10) << endl;
    return 0;
}
```

### Hashing Applications:

1.  **Substring search** in $O(N)$.
2.  **Finding the longest common substring** of two strings.
3.  **Checking for palindrome** of every substring.
4.  **Rabin-Karp algorithm** for multiple patterns.

## 2. Knuth-Morris-Pratt Algorithm (KMP)

Uses the **Prefix Function** ($\pi$).
$\pi[i]$ is the length of the longest proper prefix of the substring $S[0 \dots i]$ that is also its suffix.

Example: $S = \text{"ababa"}$
*   $\pi[0]$ ("a") = 0
*   $\pi[1]$ ("ab") = 0
*   $\pi[2]$ ("aba") = 1 ("a")
*   $\pi[3]$ ("abab") = 2 ("ab")
*   $\pi[4]$ ("ababa") = 3 ("aba")

### Applications of $\pi$:
1.  **Substring Search**: Searching for $P$ in $T$. We make a string $S = P + \# + T$. All positions where $\pi[i] == |P|$ are occurrences.
2.  **Minimum Period**: The length of the smallest period of a string with length $N$ is $N - \pi[N-1]$ (if $N$ is divisible by this number).

```cpp
vector<int> prefix_function(string s) {
    int n = s.length();
    vector<int> pi(n);
    for (int i = 1; i < n; i++) {
        int j = pi[i-1];
        while (j > 0 && s[i] != s[j])
            j = pi[j-1];
        if (s[i] == s[j])
            j++;
        pi[i] = j;
    }
    return pi;
}
```

### Pattern Search with KMP:

```cpp
vector<int> kmp_search(string text, string pattern) {
    string combined = pattern + "#" + text;
    vector<int> pi = prefix_function(combined);
    vector<int> occurrences;
    
    int m = pattern.size();
    for (int i = m + 1; i < (int)combined.size(); i++) {
        if (pi[i] == m) {
            // Found a match at position i - 2*m
            occurrences.push_back(i - 2 * m);
        }
    }
    
    return occurrences;
}
```

### Finding the Period:

```cpp
int minimalPeriod(string s) {
    int n = s.size();
    vector<int> pi = prefix_function(s);
    int period_len = n - pi[n - 1];
    
    if (n % period_len == 0) {
        return period_len;
    }
    return n; // The entire string is the period
}
```

## 3. Z-Algorithm

Calculates the **Z-function**.
$Z[i]$ is the length of the longest common prefix between string $S$ and the suffix of $S$ starting at $i$.
*   $Z[0]$ is usually not defined (or is $N$).
*   Analogous to KMP, allows for pattern search in linear time.

### Z-Algorithm Implementation:

```cpp
vector<int> z_function(string s) {
    int n = s.size();
    vector<int> z(n);
    z[0] = n;
    
    int l = 0, r = 0;
    for (int i = 1; i < n; i++) {
        if (i <= r) {
            z[i] = min(r - i + 1, z[i - l]);
        }
        while (i + z[i] < n && s[z[i]] == s[i + z[i]]) {
            z[i]++;
        }
        if (i + z[i] - 1 > r) {
            l = i;
            r = i + z[i] - 1;
        }
    }
    
    return z;
}
```

### Pattern Search with Z-Algorithm:

```cpp
vector<int> z_search(string text, string pattern) {
    string combined = pattern + "$" + text;
    vector<int> z = z_function(combined);
    vector<int> occurrences;
    
    int m = pattern.size();
    for (int i = m + 1; i < (int)combined.size(); i++) {
        if (z[i] == m) {
            occurrences.push_back(i - m - 1);
        }
    }
    
    return occurrences;
}
```

Complexity: $O(N)$ for building the Z-array.

## 4. Manacher's Algorithm

Finds the longest palindrome at each position of the string in $O(N)$.
*   Considers separately palindromes with odd length (center in a character) and even length (center between characters).

### Implementation:

```cpp
string preprocess(string s) {
    // Add '#' between characters for a unified approach
    string result = "#";
    for (char c : s) {
        result += c;
        result += '#';
    }
    return result;
}

vector<int> manacher(string s) {
    string t = preprocess(s);
    int n = t.size();
    vector<int> p(n, 0); // p[i] = radius of the palindrome centered at i
    
    int center = 0, right = 0;
    for (int i = 0; i < n; i++) {
        int mirror = 2 * center - i;
        
        if (i < right) {
            p[i] = min(right - i, p[mirror]);
        }
        
        // Attempt expansion
        while (i + p[i] + 1 < n && i - p[i] - 1 >= 0 &&
               t[i + p[i] + 1] == t[i - p[i] - 1]) {
            p[i]++;
        }
        
        // Update the center
        if (i + p[i] > right) {
            center = i;
            right = i + p[i];
        }
    }
    
    return p;
}

string longestPalindrome(string s) {
    vector<int> p = manacher(s);
    int maxLen = 0, centerIndex = 0;
    
    for (int i = 0; i < (int)p.size(); i++) {
        if (p[i] > maxLen) {
            maxLen = p[i];
            centerIndex = i;
        }
    }
    
    // Convert back to original index
    int start = (centerIndex - maxLen) / 2;
    return s.substr(start, maxLen);
}
```

### Application: Counting All Palindromes

```cpp
long long countAllPalindromes(string s) {
    vector<int> p = manacher(s);
    long long count_val = 0;
    
    for (int radius : p) {
        // Each radius gives (radius + 1) / 2 palindromes
        count_val += (radius + 1) / 2;
    }
    
    return count_val;
}
```

Complexity: $O(N)$ time and $O(N)$ memory.

## 5. Comparison

| Algorithm | Complexity | Advantages | Disadvantages |
| :--- | :--- | :--- | :--- |
| **Hashing** | $O(N)$ | Easy code, flexible (palindromes, comparisons) | Probability of collision (though small) |
| **KMP** | $O(N)$ | Deterministic, useful for periodicity | Slightly more complex logic |
| **Z-Algo** | $O(N)$ | More intuitive for some tasks than KMP | Less frequently used |
| **Manacher** | $O(N)$ | Fastest for palindromes | Specific to a single task |
| **Aho-Corasick**| $O(N)$ | Searching for **multiple** patterns simultaneously | Based on Trie (Topic 26) |

## 6. Aho-Corasick - Multiple Pattern Search

The Aho-Corasick algorithm extends KMP for searching for multiple patterns simultaneously. It uses a Trie structure with failure links.

```cpp
struct AhoCorasick {
    static const int K = 26; // Alphabet size
    
    struct Vertex {
        int next[K];
        bool leaf = false;
        int p = -1; // parent
        char pch; // character from parent
        int link = -1; // failure link
        int go_to[K]; // precomputed transitions
        
        Vertex(int p = -1, char ch = '$') : p(p), pch(ch) {
            fill(begin(next), end(next), -1);
            fill(begin(go_to), end(go_to), -1);
        }
    };
    
    vector<Vertex> trie;
    
    AhoCorasick() {
        trie.emplace_back();
    }
    
    void add_string(string const& s) {
        int v = 0;
        for (char ch : s) {
            int c = ch - 'a';
            if (trie[v].next[c] == -1) {
                trie[v].next[c] = trie.size();
                trie.emplace_back(v, ch);
            }
            v = trie[v].next[c];
        }
        trie[v].leaf = true;
    }
    
    int get_link(int v) {
        if (trie[v].link == -1) {
            if (v == 0 || trie[v].p == 0)
                trie[v].link = 0;
            else
                trie[v].link = go(get_link(trie[v].p), trie[v].pch);
        }
        return trie[v].link;
    }
    
    int go(int v, char ch) {
        int c = ch - 'a';
        if (trie[v].go_to[c] == -1) {
            if (trie[v].next[c] != -1)
                trie[v].go_to[c] = trie[v].next[c];
            else
                trie[v].go_to[c] = v == 0 ? 0 : go(get_link(v), ch);
        }
        return trie[v].go_to[c];
    }
    
    int search(string text) {
        int v = 0;
        int count_matches = 0;
        for (char ch : text) {
            v = go(v, ch);
            int temp = v;
            while (temp != 0) {
                if (trie[temp].leaf) count_matches++;
                temp = get_link(temp);
            }
        }
        return count_matches;
    }
};
```

## 7. Practice Tasks

1.  **CSES String Matching**: Basic task for KMP/Z-algorithm.
2.  **Codeforces 1200E**: Compress Words (Hashing/KMP).
3.  **SPOJ NHAY**: Searching for a needle in a haystack.
4.  **CSES Finding Patterns**: Aho-Corasick or suffix arrays.
5.  **CSES Palindrome Queries**: Manacher with modifications.
6.  **Codeforces 126B**: Password (KMP for prefix-suffix).

## 8. Additional Techniques

### Suffix Array
A sorted array of all suffixes of the string. Built in $O(N \log N)$ with radix sort or $O(N \log^2 N)$ with binary lifting.

### Suffix Tree
A compressed trie of all suffixes. Can be built in linear time with Ukkonen's algorithm, but is complex to implement.

### Longest Common Prefix (LCP)
The LCP array contains the lengths of the longest common prefixes between adjacent suffixes in the suffix array. Used for many substring tasks.

---

## 🏁 Conclusion

String algorithms are a basic tool in competitive programming. Start with hashing for quick prototypes, learn KMP for deterministic solutions, and master Aho-Corasick for multiple pattern search. Each of these algorithms has its application and strengths.