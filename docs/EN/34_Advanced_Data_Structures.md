# 🌳 Advanced Data Structures: A Complete Overview

In competitive programming, many tasks require processing a sequence of queries on arrays. While the naive approach takes $O(N)$ per query, specialized structures achieve $O(\log N)$ or even $O(1)$.

---

## 1. Binary Indexed Tree (BIT) / Fenwick Tree

BIT is an elegant structure for prefix sums that is implemented with extremely little code.

*   **Update ($O(\log N)$)**: Changing a value at a given index.
*   **Query ($O(\log N)$)**: Finding the sum in the interval $[0, i]$.

```cpp
int bit[MAXN];
int n;

void add(int idx, int val) {
    for (++idx; idx <= n; idx += idx & -idx)
        bit[idx] += val;
}

int sum(int idx) {
    int res = 0;
    for (++idx; idx > 0; idx -= idx & -idx)
        res += bit[idx];
    return res;
}

int range_sum(int l, int r) {
    return sum(r) - sum(l - 1);
}
```

### BIT for 2D Queries:

```cpp
int bit2d[MAXN][MAXN];

void update2D(int x, int y, int val) {
    for (int i = x; i <= n; i += i & -i) {
        for (int j = y; j <= n; j += j & -j) {
            bit2d[i][j] += val;
        }
    }
}

int query2D(int x, int y) {
    int res = 0;
    for (int i = x; i > 0; i -= i & -i) {
        for (int j = y; j > 0; j -= j & -j) {
            res += bit2d[i][j];
        }
    }
    return res;
}

// Sum of rectangle from (x1,y1) to (x2,y2)
int rectangleSum(int x1, int y1, int x2, int y2) {
    return query2D(x2, y2) - query2D(x1 - 1, y2) 
           - query2D(x2, y1 - 1) + query2D(x1 - 1, y1 - 1);
}
```

### BIT for finding the k-th element:

```cpp
// Finding the smallest index with prefix sum >= k
int kth_element(int k) {
    int idx = 0, sum_val = 0;
    int logn = __lg(n) + 1;
    
    for (int i = logn; i >= 0; i--) {
        int next_idx = idx + (1 << i);
        if (next_idx <= n && sum_val + bit[next_idx] < k) {
            idx = next_idx;
            sum_val += bit[idx];
        }
    }
    
    return idx + 1;
}
```

---

## 2. Segment Tree

The segment tree is the most versatile structure for interval queries. It can support any associative operation: sum, minimum, maximum, GCD, matrix multiplication.

### 2.1. Classical Segment Tree (Point Update)
```cpp
int tree[4 * MAXN];

void build(int a[], int v, int tl, int tr) {
    if (tl == tr) {
        tree[v] = a[tl];
    } else {
        int tm = (tl + tr) / 2;
        build(a, 2*v, tl, tm);
        build(a, 2*v+1, tm+1, tr);
        tree[v] = tree[2*v] + tree[2*v+1];
    }
}

int query(int v, int tl, int tr, int l, int r) {
    if (l > r) return 0;
    if (l == tl && r == tr) return tree[v];
    int tm = (tl + tr) / 2;
    return query(2*v, tl, tm, l, min(r, tm))
         + query(2*v+1, tm+1, tr, max(l, tm+1), r);
}

void update(int v, int tl, int tr, int pos, int new_val) {
    if (tl == tr) {
        tree[v] = new_val;
    } else {
        int tm = (tl + tr) / 2;
        if (pos <= tm)
            update(2*v, tl, tm, pos, new_val);
        else
            update(2*v+1, tm+1, tr, pos, new_val);
        tree[v] = tree[2*v] + tree[2*v+1];
    }
}
```

### 2.2. Lazy Propagation (Range Update)
Allows updating an entire interval (e.g., "add $X$ to all numbers from $L$ to $R$") in $O(\log N)$.
The idea is to postpone updating the children until we have to visit them.

```cpp
int tree[4 * MAXN], lazy[4 * MAXN];

void push(int v, int tl, int tr) {
    if (lazy[v] != 0) {
        tree[v] += lazy[v] * (tr - tl + 1);
        if (tl != tr) {
            lazy[2*v] += lazy[v];
            lazy[2*v+1] += lazy[v];
        }
        lazy[v] = 0;
    }
}

void update_range(int v, int tl, int tr, int l, int r, int add) {
    push(v, tl, tr);
    if (l > r) return;
    if (l == tl && r == tr) {
        lazy[v] += add;
        push(v, tl, tr);
        return;
    }
    int tm = (tl + tr) / 2;
    update_range(2*v, tl, tm, l, min(r, tm), add);
    update_range(2*v+1, tm+1, tr, max(l, tm+1), r, add);
    push(2*v, tl, tm);
    push(2*v+1, tm+1, tr);
    tree[v] = tree[2*v] + tree[2*v+1];
}

int query_range(int v, int tl, int tr, int l, int r) {
    if (l > r) return 0;
    push(v, tl, tr);
    if (l == tl && r == tr) {
        return tree[v];
    }
    int tm = (tl + tr) / 2;
    return query_range(2*v, tl, tm, l, min(r, tm))
         + query_range(2*v+1, tm+1, tr, max(l, tm+1), r);
}
```

### 2.3. Persistent Segment Tree

Saves all versions of the tree after each modification. It is used for queries on historical data:

```cpp
struct Node {
    int val;
    Node *left, *right;
    
    Node() : val(0), left(nullptr), right(nullptr) {}
    Node(int v) : val(v), left(nullptr), right(nullptr) {}
    Node(Node* l, Node* r) : left(l), right(r) {
        val = 0;
        if (l) val += l->val;
        if (r) val += r->val;
    }
};

Node* build(int tl, int tr) {
    if (tl == tr) return new Node(0);
    int tm = (tl + tr) / 2;
    return new Node(build(tl, tm), build(tm + 1, tr));
}

Node* update(Node* node, int tl, int tr, int pos, int val) {
    if (tl == tr) return new Node(val);
    int tm = (tl + tr) / 2;
    if (pos <= tm) {
        return new Node(update(node->left, tl, tm, pos, val), node->right);
    } else {
        return new Node(node->left, update(node->right, tm + 1, tr, pos, val));
    }
}

int query(Node* node, int tl, int tr, int l, int r) {
    if (l > r) return 0;
    if (l == tl && r == tr) return node->val;
    int tm = (tl + tr) / 2;
    return query(node->left, tl, tm, l, min(r, tm))
         + query(node->right, tm + 1, tr, max(l, tm + 1), r);
}
```

---

## 3. Sparse Table

Sparse Table solves RMQ (Range Minimum Query) in $O(1)$ after $O(N \log N)$ preprocessing. It is applicable only to **static** arrays (no modifications).

*   **Idea**: Precalculate answers for all intervals with length $2^k$.
*   **Query**: Every interval $[L, R]$ can be covered by two overlapping intervals with length $2^k$, where $2^k$ is the largest power of 2 not exceeding the length of the interval.

```cpp
int st[MAXN][20];
int logs[MAXN];

void build(int n, int a[]) {
    logs[1] = 0;
    for (int i = 2; i <= n; i++) logs[i] = logs[i/2] + 1;
    for (int i = 0; i < n; i++) st[i][0] = a[i];
    for (int j = 1; j < 20; j++)
        for (int i = 0; i + (1 << j) <= n; i++)
            st[i][j] = min(st[i][j-1], st[i + (1 << (j-1))][j-1]);
}

int query(int l, int r) {
    int j = logs[r - l + 1];
    return min(st[l][j], st[r - (1 << j) + 1][j]);
}
```

### Sparse Table for GCD:

```cpp
int gcd_st[MAXN][20];

void build_gcd(int n, int a[]) {
    for (int i = 0; i < n; i++) gcd_st[i][0] = a[i];
    for (int j = 1; j < 20; j++)
        for (int i = 0; i + (1 << j) <= n; i++)
            gcd_st[i][j] = __gcd(gcd_st[i][j-1], gcd_st[i + (1 << (j-1))][j-1]);
}

int query_gcd(int l, int r) {
    int j = logs[r - l + 1];
    return __gcd(gcd_st[l][j], gcd_st[r - (1 << j) + 1][j]);
}
```

### Note on non-idempotent operations:

Sparse Table works only for operations that are **idempotent** (i.e., $f(x, x) = x$). Examples: min, max, gcd, lcm.
For sum or XOR, a different approach or a segment tree must be used.

---

## 4. Sqrt Decomposition

If $N$ is around $10^5$, we can divide the array into blocks of size $\sqrt{N}$.
*   **Update**: $O(1)$ or $O(\sqrt{N})$.
*   **Query**: $O(\sqrt{N})$.
*   **Mo's Algorithm**: Allows solving complex offline queries (e.g., count of distinct elements) in $O((N+Q)\sqrt{N})$.

### 4.1. Basic Sqrt Decomposition:

```cpp
const int MAXN = 100005;
const int SQRTN = 320; // sqrt(MAXN)

int a[MAXN], block[SQRTN];
int n, block_size;

void build() {
    block_size = (int)sqrt(n) + 1;
    for (int i = 0; i < n; i++) {
        block[i / block_size] += a[i];
    }
}

void update(int idx, int val) {
    int block_idx = idx / block_size;
    block[block_idx] -= a[idx];
    a[idx] = val;
    block[block_idx] += a[idx];
}

int query(int l, int r) {
    int sum_val = 0;
    int left_block = l / block_size;
    int right_block = r / block_size;
    
    if (left_block == right_block) {
        for (int i = l; i <= r; i++)
            sum_val += a[i];
    } else {
        for (int i = l; i < (left_block + 1) * block_size; i++)
            sum_val += a[i];
        for (int i = left_block + 1; i < right_block; i++)
            sum_val += block[i];
        for (int i = right_block * block_size; i <= r; i++)
            sum_val += a[i];
    }
    
    return sum_val;
}
```

### 4.2. Mo's Algorithm:

```cpp
struct Query {
    int l, r, idx;
};

int block_size;

bool mo_cmp(Query a, Query b) {
    if (a.l / block_size != b.l / block_size)
        return a.l / block_size < b.l / block_size;
    return (a.l / block_size & 1) ? (a.r < b.r) : (a.r > b.r);
}

void mo_algorithm(vector<Query>& queries, int a[], int n) {
    block_size = sqrt(n);
    sort(queries.begin(), queries.end(), mo_cmp);
    
    vector<int> answers(queries.size());
    int current_l = 0, current_r = -1;
    int current_answer = 0;
    
    for (auto q : queries) {
        // Expand/shrink window
        while (current_l > q.l) {
            current_l--;
            // Add a[current_l]
        }
        while (current_r < q.r) {
            current_r++;
            // Add a[current_r]
        }
        while (current_l < q.l) {
            // Remove a[current_l]
            current_l++;
        }
        while (current_r > q.r) {
            // Remove a[current_r]
            current_r--;
        }
        
        answers[q.idx] = current_answer;
    }
}
```

### 4.3. Mo's Application - Number of distinct elements:

```cpp
int freq[MAXN];
int distinct_count = 0;

void add(int x) {
    if (freq[x] == 0) distinct_count++;
    freq[x]++;
}

void remove(int x) {
    freq[x]--;
    if (freq[x] == 0) distinct_count--;
}

void solve_mo(vector<Query>& queries, int a[], int n) {
    block_size = sqrt(n);
    sort(queries.begin(), queries.end(), mo_cmp);
    
    vector<int> answers(queries.size());
    int cl = 0, cr = -1;
    
    for (auto q : queries) {
        while (cl > q.l) add(a[--cl]);
        while (cr < q.r) add(a[++cr]);
        while (cl < q.l) remove(a[cl++]);
        while (cr > q.r) remove(a[cr--]);
        
        answers[q.idx] = distinct_count;
    }
    
    for (int ans : answers) {
        cout << ans << "\n";
    }
}
```

---

## 5. Additional Structures

### 5.1. Disjoint Set Union (DSU)

An efficient structure for working with non-overlapping sets:

```cpp
int parent[MAXN], rank_arr[MAXN];

void make_set(int v) {
    parent[v] = v;
    rank_arr[v] = 0;
}

int find_set(int v) {
    if (v == parent[v])
        return v;
    return parent[v] = find_set(parent[v]); // path compression
}

void union_sets(int a, int b) {
    a = find_set(a);
    b = find_set(b);
    if (a != b) {
        if (rank_arr[a] < rank_arr[b])
            swap(a, b);
        parent[b] = a;
        if (rank_arr[a] == rank_arr[b])
            rank_arr[a]++;
    }
}
```

### 5.2. Trie (Prefix Tree)

For efficient string storage and searching:

```cpp
struct TrieNode {
    TrieNode* children[26];
    bool isEndOfWord;
    
    TrieNode() {
        isEndOfWord = false;
        for (int i = 0; i < 26; i++)
            children[i] = nullptr;
    }
};

class Trie {
    TrieNode* root;
    
public:
    Trie() { root = new TrieNode(); }
    
    void insert(string word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx])
                node->children[idx] = new TrieNode();
            node = node->children[idx];
        }
        node->isEndOfWord = true;
    }
    
    bool search(string word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx])
                return false;
            node = node->children[idx];
        }
        return node->isEndOfWord;
    }
};
```

### 5.3. Suffix Array

An efficient representation of all suffixes of a string:

```cpp
vector<int> buildSuffixArray(string s) {
    int n = s.size();
    vector<int> sa(n), rank(n), tmp(n);
    
    // Initial sorting by first character
    for (int i = 0; i < n; i++) {
        sa[i] = i;
        rank[i] = s[i];
    }
    
    for (int k = 1; k < n; k *= 2) {
        auto cmp = [&](int i, int j) {
            if (rank[i] != rank[j]) return rank[i] < rank[j];
            int ri = (i + k < n) ? rank[i + k] : -1;
            int rj = (j + k < n) ? rank[j + k] : -1;
            return ri < rj;
        };
        
        sort(sa.begin(), sa.end(), cmp);
        
        tmp[sa[0]] = 0;
        for (int i = 1; i < n; i++) {
            tmp[sa[i]] = tmp[sa[i-1]] + (cmp(sa[i-1], sa[i]) ? 1 : 0);
        }
        
        rank = tmp;
    }
    
    return sa;
}
```

## 6. Tasks

1.  **CSES Dynamic Range Minimum Queries**: Segment Tree.
2.  **CSES Static Range Sum Queries**: Prefix sums.
3.  **Codeforces 86D**: Powerful array (Mo's algorithm).
4.  **SPOJ GSS1**: Can you answer these queries I (SegTree).
5.  **CSES Range Update Queries**: Lazy propagation.
6.  **CSES Distinct Values Queries**: Mo's algorithm.
7.  **Codeforces 633F**: Range XOR Queries (Persistent segment tree).

## 7. Comparison Table

| Structure | Build | Update | Query | Space | Application |
|-----------|-------|---------|-------|-------|------------|
| **Prefix Sum** | $O(N)$ | - | $O(1)$ | $O(N)$ | Static sums |
| **BIT** | $O(N \log N)$ | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Prefix sums |
| **Segment Tree** | $O(N)$ | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Universal queries |
| **Sparse Table** | $O(N \log N)$ | - | $O(1)$ | $O(N \log N)$ | Static RMQ |
| **Sqrt Decomp** | $O(N)$ | $O(\sqrt{N})$ | $O(\sqrt{N})$ | $O(N)$ | Offline queries |

## 🏁 Conclusion

Start with BIT for sums. If you need Min/Max, learn Sparse Table. For complex interval operations, the Segment Tree is indispensable. Mo's algorithm is a powerful tool for offline queries with complex conditions. Master these structures through practice with various tasks.