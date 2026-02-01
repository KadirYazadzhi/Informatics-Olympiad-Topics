# 🔤 Suffix Structures: Expert Level

Suffix structures are specialized tools for efficient string processing. They allow answering queries like "is $P$ a substring of $S$", "how many times does it appear", "find the longest common substring", etc. quickly.

The main structures are:
1.  **Suffix Tree**.
2.  **Suffix Array**.
3.  **Suffix Automaton (SAM)**.

In competitive programming, **Suffix Array** (due to low memory) and **Suffix Automaton** (due to power and linear time) are most used.

---

## 1. Suffix Array

### 1.1. Definition
Let $S$ be a string of length $n$. The Suffix Array `SA` contains the indices of all suffixes of $S$, sorted lexicographically.
Example: $S = "aba"$
Suffixes:
0: "aba"
1: "ba"
2: "a"

Sorted: "a", "aba", "ba" $\implies SA = [2, 0, 1]$.

### 1.2. Construction ($O(N \log N)$)
Using cyclic shifts sorting ($O(N \log N)$) is standard.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>

using namespace std;

vector<int> sort_cyclic_shifts(string const& s) {
    int n = s.size();
    const int alphabet = 256;
    vector<int> p(n), c(n), cnt(max(alphabet, n), 0);

    for (int i = 0; i < n; i++) cnt[s[i]]++;
    for (int i = 1; i < alphabet; i++) cnt[i] += cnt[i-1];
    for (int i = 0; i < n; i++) p[--cnt[s[i]]] = i;
    
    c[p[0]] = 0;
    int classes = 1;
    for (int i = 1; i < n; i++) {
        if (s[p[i]] != s[p[i-1]]) classes++;
        c[p[i]] = classes - 1;
    }

    vector<int> pn(n), cn(n);
    for (int h = 0; (1 << h) < n; ++h) {
        for (int i = 0; i < n; i++) {
            pn[i] = p[i] - (1 << h);
            if (pn[i] < 0) pn[i] += n;
        }
        fill(cnt.begin(), cnt.begin() + classes, 0);
        for (int i = 0; i < n; i++) cnt[c[pn[i]]]++;
        for (int i = 1; i < classes; i++) cnt[i] += cnt[i-1];
        for (int i = n-1; i >= 0; i--) p[--cnt[c[pn[i]]]] = pn[i];
        
        cn[p[0]] = 0;
        classes = 1;
        for (int i = 1; i < n; i++) {
            pair<int, int> cur = {c[p[i]], c[(p[i] + (1 << h)) % n]};
            pair<int, int> prev = {c[p[i-1]], c[(p[i-1] + (1 << h)) % n]};
            if (cur != prev) ++classes;
            cn[p[i]] = classes - 1;
        }
        c.swap(cn);
    }
    return p;
}

vector<int> suffix_array_construction(string s) {
    s += "$";
    vector<int> sorted_shifts = sort_cyclic_shifts(s);
    sorted_shifts.erase(sorted_shifts.begin());
    return sorted_shifts;
}
```

### 1.3. LCP Array (Longest Common Prefix)
`LCP[i]` is the length of the longest common prefix between `SA[i]` and `SA[i-1]`.
It allows solving:
1.  **Number of distinct substrings**: $\frac{n(n+1)}{2} - \sum LCP[i]$.
2.  **Longest repeating substring**: $\max(LCP)$.

---

## 2. Suffix Automaton (SAM)

### 2.1. Idea
A DAG that compresses all substrings.
*   **States**: Equivalence classes of substrings (`endpos`).
*   **Suffix Links**: Point to the longest suffix in a different class.

### 2.2. Implementation ($O(N)$)

```cpp
#include <map>

struct State {
    int len, link;
    std::map<char, int> next;
};

const int MAXLEN = 100000;
State st[MAXLEN * 2];
int sz, last;

void sam_init() {
    st[0].len = 0;
    st[0].link = -1;
    st[0].next.clear();
    sz = 1;
    last = 0;
}

void sam_extend(char c) {
    int cur = sz++;
    st[cur].len = st[last].len + 1;
    st[cur].next.clear();
    int p = last;
    while (p != -1 && st[p].next.find(c) == st[p].next.end()) {
        st[p].next[c] = cur;
        p = st[p].link;
    }
    if (p == -1) {
        st[cur].link = 0;
    } else {
        int q = st[p].next[c];
        if (st[p].len + 1 == st[q].len) {
            st[cur].link = q;
        } else {
            int clone = sz++;
            st[clone].len = st[p].len + 1;
            st[clone].next = st[q].next;
            st[clone].link = st[q].link;
            while (p != -1 && st[p].next[c] == q) {
                st[p].next[c] = clone;
                p = st[p].link;
            }
            st[q].link = st[cur].link = clone;
        }
    }
    last = cur;
}
```

### 2.3. Applications
1.  **Check occurrence**: Follow edges from root. $O(|P|)$.
2.  **Distinct substrings**: Sum of $(len(u) - len(link(u)))$.
3.  **K-th Lexicographical Substring**: Count paths in DAG.

---

## 3. Comparison

| Feature | Suffix Array + LCP | Suffix Automaton |
| :--- | :--- | :--- |
| **Memory** | $O(N)$ (small constant) | $O(N \Sigma)$ or map |
| **Construction** | $O(N \log N)$ | $O(N)$ |
| **Complexity** | Moderate | Abstract logic |
| **Flexibility** | Good for static | Excellent for dynamic |

For contests, use **Suffix Array** if memory is tight ($N=5 \cdot 10^5$), otherwise **SAM** is more powerful.