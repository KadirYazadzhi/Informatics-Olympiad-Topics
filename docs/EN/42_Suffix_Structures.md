# 🔤 Suffix Structures: Expert Level

Suffix structures are specialized tools for efficient string processing. They allow for fast answers to queries such as "whether string $P$ is contained in $S$", "how many times it occurs", "find the longest common substring", and others.

The main structures are:
1.  **Suffix Tree**.
2.  **Suffix Array**.
3.  **Suffix Automaton (SAM)**.

In competitive programming, **Suffix Array** (due to low memory and simplicity) and **Suffix Automaton** (due to its power and linear complexity) are most frequently used.

---

## 1. Suffix Array

### 1.1. Definition
Let $S$ be a string of length $n$. The suffix array `SA` contains the indices of all suffixes of $S$, sorted lexicographically.
Example: $S = "aba"$
Suffixes:
0: "aba"
1: "ba"
2: "a"

Sorted suffixes:
1. "a" (index 2)
2. "aba" (index 0)
3. "ba" (index 1)
$\implies SA = [2, 0, 1]$.

### 1.2. Construction ($O(N \log N)$)
The simplest way is sorting with `std::sort` ($O(N^2 \log N)$), but it is too slow. The efficient approach uses sorting by equivalence classes of powers of 2.

Idea:
1.  Sort substrings of length $2^0 = 1$ (simply the characters).
2.  Use the result to sort substrings of length $2^1 = 2$. A substring of length $2L$ consists of two substrings of length $L$: `pair(class[i], class[i+L])`.
3.  Repeat until $2^k \ge n$.

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

    // Initial phase (k=0)
    for (int i = 0; i < n; i++) cnt[s[i]]++;
    for (int i = 1; i < alphabet; i++) cnt[i] += cnt[i-1];
    for (int i = 0; i < n; i++) p[--cnt[s[i]]] = i;
    
    c[p[0]] = 0;
    int classes = 1;
    for (int i = 1; i < n; i++) {
        if (s[p[i]] != s[p[i-1]]) classes++;
        c[p[i]] = classes - 1;
    }

    // Steps (1, 2, 4, ...)
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
    s += "$"; // Add terminating character (smallest)
    vector<int> sorted_shifts = sort_cyclic_shifts(s);
    sorted_shifts.erase(sorted_shifts.begin()); // Remove '$'
    return sorted_shifts;
}
```

### 1.3. LCP Array (Longest Common Prefix)
`LCP[i]` is the length of the longest common prefix between suffixes `SA[i]` and `SA[i-1]`.
This array allows for solving many tasks. It can be built in $O(N)$ using Kasai's algorithm.

Applications of SA + LCP:
1.  **Number of distinct substrings**: $\frac{n(n+1)}{2} - \sum LCP[i]$.
2.  **Longest repeated substring**: $\max(LCP)$.

---

## 2. Suffix Automaton (SAM)

### 2.1. Idea
SAM is a directed acyclic graph (DAG) that compresses all substrings of a given string.
*   **States**: Each node represents a set of substrings that occur at the same positions in $S$ (the so-called `endpos` equivalence).
*   **Edges**: Transitions upon adding a character.
*   **Suffix Links**: References to the state corresponding to the longest suffix that is in a different equivalence class.

### 2.2. Properties
*   Number of nodes: at most $2N - 1$.
*   Number of edges: at most $3N - 4$.
*   Construction: $O(N)$ (online algorithm).

### 2.3. Implementation
The structure of a node contains:
*   `len`: length of the longest substring in this state.
*   `link`: suffix link.
*   `next`: map or array for transitions.

```cpp
#include <map>

struct State {
    int len, link;
    std::map<char, int> next; // Or array next[26]
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
    // Follow suffix links until there is no transition with 'c'
    while (p != -1 && st[p].next.find(c) == st[p].next.end()) {
        st[p].next[c] = cur;
        p = st[p].link;
    }
    
    if (p == -1) {
        st[cur].link = 0;
    } else {
        int q = st[p].next[c];
        if (st[p].len + 1 == st[q].len) {
            // Easy case: just link
            st[cur].link = q;
        } else {
            // Cloning
            int clone = sz++;
            st[clone].len = st[p].len + 1;
            st[clone].next = st[q].next; // Copy transitions
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

### 2.4. Applications of SAM

1.  **Substring occurrence check**: We start from the root and follow the edges. If we cannot make a transition, the string does not occur. Time: $O(|P|)$.
2.  **Number of distinct substrings**: Each node $u$ (other than the root) corresponds to $len(u) - len(link(u))$ unique substrings. Sum this difference for all nodes.
3.  **Lexicographically Minimal String Rotation**: We build a SAM for the string $S+S$ and walk $N$ steps along the lexicographically smallest edges.
4.  **Number of occurrences**: For each node, we can calculate how many times its substrings occur by "pushing" the counters from children to parents in the suffix link tree.

## 3. Comparison

| Characteristic | Suffix Array + LCP | Suffix Automaton |
| :--- | :--- | :--- |
| **Memory** | $O(N)$ (small constant, int arrays) | $O(N \Sigma)$ or $O(N)$ with map (larger constant) |
| **Construction Time** | $O(N \log N)$ or $O(N)$ | $O(N)$ |
| **Implementation Complexity** | Medium (somewhat confusing) | Short, but abstract |
| **Flexibility** | Good for static tasks | Excellent, supports dynamic adding |

For competitions, **Suffix Array** is usually preferred if memory is limited (e.g., 256MB for $N=5 \cdot 10^5$), while **SAM** is more convenient for tasks with many different strings or complex dependencies.
