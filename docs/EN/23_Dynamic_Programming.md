# 🔄 Dynamic Programming: Advanced Techniques

## 🔢 Digit DP

Used for tasks of the type "How many numbers in the interval $[A, B]$ satisfy condition $X$?".
We solve the task for $[0, B]$ and subtract the result for $[0, A-1]$.

**State**: `dp(index, count, isLess, isStarted)`
- `index`: Which digit we are considering (from left to right).
- `count`: The parameter we are tracking (e.g., sum of digits, number of occurrences of the digit 7, etc.).
- `isLess`: Boolean flag. `true` if the number we are building is already strictly less than the prefix of $N$. If it is `true`, we can place any digits (0-9). If it is `false`, we are limited up to $N[index]$.
- `isStarted`: For handling leading zeros.

**Basic Template:**
```cpp
long long memo[20][200][2];
string S;

long long solve(int idx, int sum, bool isLess) {
    if (idx == S.size()) return sum; // Base case
    if (memo[idx][sum][isLess] != -1) return memo[idx][sum][isLess];

    long long ans = 0;
    int limit = isLess ? 9 : (S[idx] - '0');

    for (int digit = 0; digit <= limit; digit++) {
        bool nextIsLess = isLess || (digit < limit);
        ans += solve(idx + 1, sum + digit, nextIsLess);
    }
    return memo[idx][sum][isLess] = ans;
}
```

### Extended Example: Count of numbers with sum of digits divisible by K

```cpp
#include <iostream>
#include <cstring>
using namespace std;

string num;
int K;
long long dp[20][180][2]; // [position][sum % K][isLess]

long long digitDP(int pos, int sum, bool tight) {
    // Base case - we have reached the end of the number
    if (pos == num.size()) {
        return sum == 0 ? 1 : 0;
    }
    
    // Check for memoization
    if (dp[pos][sum][tight] != -1) {
        return dp[pos][sum][tight];
    }
    
    int limit = tight ? (num[pos] - '0') : 9;
    long long result = 0;
    
    for (int digit = 0; digit <= limit; digit++) {
        int newSum = (sum + digit) % K;
        bool newTight = tight && (digit == limit);
        result += digitDP(pos + 1, newSum, newTight);
    }
    
    return dp[pos][sum][tight] = result;
}

long long solve(string number) {
    num = number;
    memset(dp, -1, sizeof(dp));
    return digitDP(0, 0, true) - 1; // -1 to exclude 0
}

int main() {
    K = 3;
    cout << "Numbers up to 100 with sum of digits divisible by 3: " 
         << solve("100") << endl;
    return 0;
}
```

### Tricks in Digit DP:
1. **Handling leading zeros**: Add an `isStarted` flag that becomes `true` after the first non-zero digit.
2. **Interval $[A, B]$**: Calculate `solve(B) - solve(A-1)`.
3. **Memory optimization**: If we have many parameters, we can use a `map` instead of an array for memoization.

---


## 🌳 Tree DP

Tasks on trees are often solved with DP, where the state for node $u$ depends on its children.

**Example: Maximum Independent Set**
Return the maximum number of vertices that are not neighbors.
- `dp[u][0]`: Maximum for the subtree of $u$ if $u$ **is not** selected.
  - $\sum \max(dp[v][0], dp[v][1])$ for all children $v$.
- `dp[u][1]`: Maximum for the subtree if $u$ **is** selected.
  - $1 + \sum dp[v][0]$ for all children $v$ (children cannot be selected).

```cpp
#include <iostream>
#include <vector>
using namespace std;

const int MAXN = 100005;
vector<int> adj[MAXN];
int dp[MAXN][2]; // dp[u][0/1] = not selected/selected
bool visited[MAXN];

void dfs(int u, int parent) {
    visited[u] = true;
    dp[u][0] = 0; // we do not select node u
    dp[u][1] = 1; // we select node u
    
    for (int v : adj[u]) {
        if (v == parent) continue;
        dfs(v, u);
        
        // If u is not selected, we can either select or not select v
        dp[u][0] += max(dp[v][0], dp[v][1]);
        
        // If u is selected, we cannot select v
        dp[u][1] += dp[v][0];
    }
}

int main() {
    int n = 5;
    adj[1] = {2, 3};
    adj[2] = {1, 4, 5};
    adj[3] = {1};
    adj[4] = {2};
    adj[5] = {2};
    
    dfs(1, -1);
    cout << "Maximum Independent Set: " 
         << max(dp[1][0], dp[1][1]) << endl;
    return 0;
}
```

### Another Example: Tree Diameter with DP

The diameter is the longest path between two vertices. We can find it with two DFS traversals or with DP:

```cpp
int diameter = 0;

int dfs(int u, int parent) {
    int max1 = 0, max2 = 0; // Two longest paths down
    
    for (int v : adj[u]) {
        if (v == parent) continue;
        int h = dfs(v, u);
        
        if (h > max1) {
            max2 = max1;
            max1 = h;
        } else if (h > max2) {
            max2 = h;
        }
    }
    
    diameter = max(diameter, max1 + max2);
    return max1 + 1;
}
```


---


## 📏 Hirschberg's Algorithm

To find **the actual** LCS (Longest Common Subsequence), and not just its length, standard DP requires $O(N \cdot M)$ memory. Hirschberg's algorithm allows for path reconstruction with $O(N \cdot M)$ time and only **$O(\min(N, M))$ memory**.
- It uses "Divide and Conquer" by finding the midpoint of the optimal path and solving recursively for the two halves.

### Detailed Explanation:

Standard LCS uses a table $dp[i][j]$ which takes $O(N \cdot M)$ memory. Hirschberg noticed that:
1. To find the length of the LCS, we only need two rows at a time.
2. We can divide the problem into two parts in the middle of the first string.
3. We solve the two subtasks recursively.

```cpp
// Finding the last row from the DP table
vector<int> computeLCS(string A, string B) {
    int m = A.size(), n = B.size();
    vector<int> prev(n + 1, 0), curr(n + 1, 0);
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (A[i-1] == B[j-1]) {
                curr[j] = prev[j-1] + 1;
            } else {
                curr[j] = max(prev[j], curr[j-1]);
            }
        }
        swap(prev, curr);
        fill(curr.begin(), curr.end(), 0);
    }
    return prev;
}

// Recursive Hirschberg's function
string hirschberg(string A, string B) {
    if (A.empty()) return "";
    if (A.size() == 1) {
        for (char c : B) {
            if (c == A[0]) return string(1, A[0]);
        }
        return "";
    }
    
    int mid = A.size() / 2;
    string A1 = A.substr(0, mid);
    string A2 = A.substr(mid);
    
    // Find the optimal division of B
    vector<int> L1 = computeLCS(A1, B);
    
    // Reverse strings for the second half
    reverse(A2.begin(), A2.end());
    reverse(B.begin(), B.end());
    vector<int> L2 = computeLCS(A2, B);
    reverse(B.begin(), B.end());
    
    // Find the best division point
    int k = 0, maxVal = L1[0] + L2[B.size()];
    for (int j = 0; j <= B.size(); j++) {
        if (L1[j] + L2[B.size() - j] > maxVal) {
            maxVal = L1[j] + L2[B.size() - j];
            k = j;
        }
    }
    
    // Solve the two parts recursively
    return hirschberg(A1, B.substr(0, k)) + 
           hirschberg(A.substr(mid), B.substr(k));
}
```

Complexity: $O(N \cdot M)$ time, $O(\min(N, M))$ memory.


---


## 🎒 Knapsack - Variations

1. **Unbounded Knapsack**: Each item can be used multiple times.
   - $dp[w] = \max(dp[w], dp[w - weight[i]] + value[i])$ (the loop is from bottom up).
2. **Limited Knapsack**: Each item has $C_i$ count.
   - Optimization: Decompose $C_i$ into powers of two $(1, 2, 4, \dots)$ and reduce the task to 0/1 Knapsack ($O(N \cdot W \cdot \log C)$).

### Detailed Implementation of Unbounded Knapsack:

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int unboundedKnapsack(int W, vector<int>& weight, vector<int>& value) {
    int n = weight.size();
    vector<int> dp(W + 1, 0);
    
    // For each weight from 1 to W
    for (int w = 1; w <= W; w++) {
        // Try each item
        for (int i = 0; i < n; i++) {
            if (weight[i] <= w) {
                dp[w] = max(dp[w], dp[w - weight[i]] + value[i]);
            }
        }
    }
    
    return dp[W];
}

int main() {
    int W = 8;
    vector<int> weight = {1, 3, 4, 5};
    vector<int> value = {10, 40, 50, 70};
    
    cout << "Maximum value: " 
         << unboundedKnapsack(W, weight, value) << endl;
    return 0;
}
```

### Limited Knapsack with Binary Decomposition:

```cpp
int limitedKnapsack(int W, vector<int>& weight, 
                    vector<int>& value, vector<int>& count) {
    int n = weight.size();
    vector<pair<int,int>> items; // {weight, value}
    
    // Decompose each item into powers of 2
    for (int i = 0; i < n; i++) {
        int c = count[i];
        int k = 1;
        while (k <= c) {
            items.push_back({weight[i] * k, value[i] * k});
            c -= k;
            k *= 2;
        }
        if (c > 0) {
            items.push_back({weight[i] * c, value[i] * c});
        }
    }
    
    // Standard 0/1 Knapsack
    vector<int> dp(W + 1, 0);
    for (auto [w, v] : items) {
        for (int i = W; i >= w; i--) {
            dp[i] = max(dp[i], dp[i - w] + v);
        }
    }
    
    return dp[W];
}
```

### Knapsack with Exact Sum:

Sometimes it is required to find ways to fill the knapsack with a specific weight:

```cpp
int exactKnapsack(int target, vector<int>& coins) {
    vector<int> dp(target + 1, 0);
    dp[0] = 1; // One way to make 0
    
    for (int coin : coins) {
        for (int i = coin; i <= target; i++) {
            dp[i] += dp[i - coin];
        }
    }
    
    return dp[target];
}
```


---


## 🕸️ Broken Profile DP - Details

Used for filling $N \times M$ grids (where $N$ is small).
We build the grid cell by cell. The profile is the boundary between the processed and unprocessed cells.
- If moving row by row, the profile is the state of the current row (bitmask).
- If moving cell by cell, the profile includes $N$ bits (parts of the current and next row).

### Classical Example: Tiling with Dominoes

Task: In how many ways can we cover an $N \times M$ grid with $1 \times 2$ dominoes?

```cpp
#include <iostream>
#include <cstring>
using namespace std;

const int MAXN = 11;
const int MAXM = 1001;
long long dp[MAXM][1 << MAXN];
int n, m;

// Generates all valid transitions from a mask to the next mask
void gen(int pos, int cur_mask, int next_mask, int col, long long val) {
    if (pos == n) {
        dp[col + 1][next_mask] += val;
        return;
    }
    
    if (cur_mask & (1 << pos)) {
        // Already filled - continue
        gen(pos + 1, cur_mask, next_mask, col, val);
    } else {
        // Option 1: Vertical domino
        gen(pos + 1, cur_mask | (1 << pos), next_mask | (1 << pos), col, val);
        
        // Option 2: Horizontal domino (if there is space)
        if (pos + 1 < n && !(cur_mask & (1 << (pos + 1)))) {
            gen(pos + 2, cur_mask | (1 << pos) | (1 << (pos + 1)), 
                next_mask, col, val);
        }
    }
}

long long countTilings(int N, int M) {
    n = N; m = M;
    memset(dp, 0, sizeof(dp));
    dp[0][0] = 1;
    
    for (int col = 0; col < m; col++) {
        for (int mask = 0; mask < (1 << n); mask++) {
            if (dp[col][mask] > 0) {
                gen(0, mask, 0, col, dp[col][mask]);
            }
        }
    }
    
    return dp[m][0];
}

int main() {
    cout << "Ways to tile 3x4: " 
         << countTilings(3, 4) << endl;
    return 0;
}
```

Complexity: $O(M \cdot 2^N \cdot N)$ where $N \leq 10$.

---

## 🎯 Practice Tasks

1. **CSES: Dice Combinations** - Basic DP.
2. **CSES: Counting Towers** - Profile DP.
3. **Codeforces 1073E** - Digit DP with a limit on distinct digits.
4. **CSES: Tree Distances II** - Tree DP for sum of distances.
5. **AtCoder DP Contest** - A collection of 26 DP tasks.

---

## 💡 Tips and Good Practices

1. **Define the state clearly**: Write down what each parameter means.
2. **Check the boundaries**: What are the base cases?
3. **Memoization vs. Tabulation**: Memoization (top-down) is more intuitive, but tabulation (bottom-up) is often faster.
4. **Memory optimization**: If `dp[i]` depends only on `dp[i-1]`, use two arrays instead of a whole matrix.
5. **Watch out for overflow**: With large numbers, use `long long` or modular arithmetic.

---

## 🏁 Conclusion

Dynamic programming is an art. The most important step is to define a state that contains **enough** information for future decisions but is **minimal** in size. Always check for overlapping subproblems! Practice regularly with different types of tasks to develop an intuition for when and how to apply DP.