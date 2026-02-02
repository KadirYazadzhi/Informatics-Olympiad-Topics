# 🌳 Напреднали Структури от Данни: Пълен Преглед

В състезателното програмиране много задачи изискват обработка на поредица от заявки върху масиви. Докато наивният подход отнема $O(N)$ на заявка, специализираните структури постигат $O(\log N)$ или дори $O(1)$.

---

## 1. Дърво на Фенуик (Binary Indexed Tree - BIT)

BIT е елегантна структура за префиксни суми, която се имплементира с изключително малко код.

*   **Update ($O(\log N)$)**: Промяна на стойност на даден индекс.
*   **Query ($O(\log N)$)**: Намиране на сумата в интервала $[0, i]$.

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

### BIT за 2D заявки:

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

// Сума на правоъгълник от (x1,y1) до (x2,y2)
int rectangleSum(int x1, int y1, int x2, int y2) {
    return query2D(x2, y2) - query2D(x1 - 1, y2) 
           - query2D(x2, y1 - 1) + query2D(x1 - 1, y1 - 1);
}
```

### BIT за намиране на k-тия елемент:

```cpp
// Намиране на най-малък индекс с префиксна сума >= k
int kth_element(int k) {
    int idx = 0, sum = 0;
    int logn = __lg(n) + 1;
    
    for (int i = logn; i >= 0; i--) {
        int next_idx = idx + (1 << i);
        if (next_idx <= n && sum + bit[next_idx] < k) {
            idx = next_idx;
            sum += bit[idx];
        }
    }
    
    return idx + 1;
}
```

---

## 2. Сегментно Дърво (Segment Tree)

Сегментното дърво е най-универсалната структура за интервални заявки. То може да поддържа всяка асоциативна операция: сума, минимум, максимум, НОД, умножение на матрици.

### 2.1. Класическо Сегментно Дърво (Point Update)
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
Позволява обновяване на цял интервал (напр. "добави $X$ към всички числа от $L$ до $R$") за $O(\log N)$.
Идеята е да отложим обновяването на децата, докато не се наложи да ги посетим.

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

Запазва всички версии на дървото след всяка промяна. Използва се за заявки върху исторически данни:

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

## 3. Разредена Таблица (Sparse Table)

Sparse Table решава RMQ (Range Minimum Query) за $O(1)$ след $O(N \log N)$ препроцесинг. Приложима е само за **статични** масиви (без промени).

*   **Идея**: Предварително пресмятаме отговорите за всички интервали с дължина $2^k$.
*   **Заявка**: Всеки интервал $[L, R]$ може да бъде покрит от два застъпващи се интервала с дължина $2^k$, където $2^k$ е най-голямата степен на 2, ненадвишаваща дължината на интервала.

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

### Sparse Table за НОД:

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

### Забележка за не-идемпотентни операции:

Sparse Table работи само за операции, които са **идемпотентни** (т.е. $f(x, x) = x$). Примери: min, max, gcd, lcm.
За сума или XOR трябва да се използва различен подход или сегментно дърво.

---

## 4. Sqrt Decomposition

Ако $N$ е около $10^5$, можем да разделим масива на блокове с размер $\sqrt{N}$.
*   **Update**: $O(1)$ или $O(\sqrt{N})$.
*   **Query**: $O(\sqrt{N})$.
*   **Алгоритъм на Мо (Mo's Algorithm)**: Позволява решаване на сложни офлайн заявки (напр. брой различни елементи) за $O((N+Q)\sqrt{N})$.

### 4.1. Базово Sqrt Decomposition:

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
    int sum = 0;
    int left_block = l / block_size;
    int right_block = r / block_size;
    
    if (left_block == right_block) {
        for (int i = l; i <= r; i++)
            sum += a[i];
    } else {
        for (int i = l; i < (left_block + 1) * block_size; i++)
            sum += a[i];
        for (int i = left_block + 1; i < right_block; i++)
            sum += block[i];
        for (int i = right_block * block_size; i <= r; i++)
            sum += a[i];
    }
    
    return sum;
}
```

### 4.2. Алгоритъм на Мо:

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
        // Разширяваме/свиваме прозореца
        while (current_l > q.l) {
            current_l--;
            // Добавяме a[current_l]
        }
        while (current_r < q.r) {
            current_r++;
            // Добавяме a[current_r]
        }
        while (current_l < q.l) {
            // Премахваме a[current_l]
            current_l++;
        }
        while (current_r > q.r) {
            // Премахваме a[current_r]
            current_r--;
        }
        
        answers[q.idx] = current_answer;
    }
}
```

### 4.3. Приложение на Мо - Брой различни елементи:

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

## 5. Допълнителни структури

### 5.1. Union-Find (Disjoint Set Union - DSU)

Ефективна структура за работа с непресичащи се множества:

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

### 5.2. Trie (Префиксно дърво)

За ефективно съхранение и търсене на низове:

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

Ефективно представяне на всички суфикси на низ:

```cpp
vector<int> buildSuffixArray(string s) {
    int n = s.size();
    vector<int> sa(n), rank(n), tmp(n);
    
    // Начално сортиране по първи символ
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

## 6. Задачи

1.  **CSES Dynamic Range Minimum Queries**: Сегментно дърво.
2.  **CSES Static Range Sum Queries**: Префиксни суми.
3.  **Codeforces 86D**: Powerful array (Mo's algorithm).
4.  **SPOJ GSS1**: Can you answer these queries I (SegTree).
5.  **CSES Range Update Queries**: Lazy propagation.
6.  **CSES Distinct Values Queries**: Mo's algorithm.
7.  **Codeforces 633F**: Range XOR Queries (Persistent segment tree).

## 7. Сравнителна таблица

| Структура | Build | Update | Query | Space | Приложение |
|-----------|-------|---------|-------|-------|------------|
| **Prefix Sum** | $O(N)$ | - | $O(1)$ | $O(N)$ | Статични суми |
| **BIT** | $O(N \log N)$ | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Префиксни суми |
| **Segment Tree** | $O(N)$ | $O(\log N)$ | $O(\log N)$ | $O(N)$ | Универсални заявки |
| **Sparse Table** | $O(N \log N)$ | - | $O(1)$ | $O(N \log N)$ | Статични RMQ |
| **Sqrt Decomp** | $O(N)$ | $O(\sqrt{N})$ | $O(\sqrt{N})$ | $O(N)$ | Офлайн заявки |

## 🏁 Заключение

Започнете с BIT за суми. Ако ви трябва Min/Max, научете Sparse Table. За сложни интервални операции, Сегментното дърво е незаменимо. Алгоритъмът на Мо е мощен инструмент за офлайн заявки с комплексни условия. Овладейте тези структури чрез практика с различни задачи.