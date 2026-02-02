# 🧠 Advanced Dynamic Programming (Advanced DP)

After mastering classical DP (Knapsack, LIS, LCS), the next level includes techniques for optimizing states and transitions. Advanced DP techniques allow you to solve tasks with more complex data structures (trees, graphs, bitmasks) and to optimize complexity from $O(N^2)$ or $O(N^3)$ to $O(N \log N)$ or even $O(N)$.

## 1. Tree DP

Tasks on trees are often solved with DFS, where for each vertex $u$ we calculate $dp[u]$ based on its children. The idea is to consider the tree recursively - first processing the subtrees, then combining the results.

### 1.1. Classical Tree DP
Example: Maximum Independent Set in a tree.

We define states:
- $dp[u][0]$: maximum number of selected vertices in the subtree of $u$, when $u$ **is not selected**.
- $dp[u][1]$: maximum number of selected vertices in the subtree of $u$, when $u$ **is selected**.

Transitions:
- If $u$ is not selected: $dp[u][0] = \sum_{v \in children(u)} \max(dp[v][0], dp[v][1])$.
- If $u$ is selected: $dp[u][1] = 1 + \sum_{v \in children(u)} dp[v][0]$.

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MAXN = 100005;
vector<int> adj[MAXN];
int dp[MAXN][2];

void dfs(int u, int parent) {
    dp[u][0] = 0; // u is not selected
    dp[u][1] = 1; // u is selected
    
    for (int v : adj[u]) {
        if (v == parent) continue;
        dfs(v, u);
        
        // If u is not selected, the child can be or not be
        dp[u][0] += max(dp[v][0], dp[v][1]);
        
        // If u is selected, the child cannot be selected
        dp[u][1] += dp[v][0];
    }
}

int main() {
    int n;
    cin >> n;
    
    for (int i = 0; i < n - 1; i++) {
        int u, v;
        cin >> u >> v;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }
    
    dfs(1, 0);
    cout << max(dp[1][0], dp[1][1]) << endl;
    
    return 0;
}
```

### 1.2. More Examples of Tree DP

**Tree Diameter:** For each vertex, we keep the longest path that passes through it.

```cpp
int maxPath[MAXN]; // the longest path passing through u
int maxDown[MAXN]; // the longest path down from u

void dfs(int u, int p) {
    maxDown[u] = 0;
    int max1 = 0, max2 = 0; // the two longest paths down
    
    for (int v : adj[u]) {
        if (v == p) continue;
        dfs(v, u);
        
        int curr = maxDown[v] + 1;
        if (curr > max1) {
            max2 = max1;
            max1 = curr;
        } else if (curr > max2) {
            max2 = curr;
        }
    }
    
    maxDown[u] = max1;
    maxPath[u] = max1 + max2; // diameter passing through u
}
```

### 1.3. Rerooting Technique (DP with all roots)
If we need to find the answer for *every* vertex if it were the root.

Approach (two DFSs):
1. **Bottom-up DFS:** We calculate DP for an arbitrary root (e.g., 1), processing the subtrees.
2. **Top-down DFS:** We transfer the result from the parent to the child, "moving" the root.

Example: For every vertex, find the sum of distances to all other vertices.

```cpp
const int MAXN = 200005;
vector<int> adj[MAXN];
long long subtree[MAXN]; // number of vertices in the subtree
long long down[MAXN];    // sum of distances downwards
long long ans[MAXN];     // answer for each vertex
int n;

// Bottom-up: calculating for root 1
void dfs1(int u, int p) {
    subtree[u] = 1;
    down[u] = 0;
    
    for (int v : adj[u]) {
        if (v == p) continue;
        dfs1(v, u);
        
        subtree[u] += subtree[v];
        down[u] += down[v] + subtree[v];
    }
}

// Top-down: carrying the result to all possible roots
void dfs2(int u, int p) {
    for (int v : adj[u]) {
        if (v == p) continue;
        
        // Move the root from u to v
        // Remove contribution from the subtree of v
        // Add contribution from the rest of the tree
        ans[v] = ans[u] - subtree[v] + (n - subtree[v]);
        
        dfs2(v, u);
    }
}
```

## 2. Broken Profile DP

Used for covering $N \times M$ grids with shapes (dominoes, tiles), where $N$ is small (usually $N \le 13$).

The state is a bitmask representing the boundary ("profile") between the processed and unprocessed parts of the grid. We process the grid column by column (or row by row).

**Example:** Covering an $N \times M$ chessboard with $1 \times 2$ dominoes.

The bitmask shows which cells from the current column "stick out" into the next one (vertical dominoes).

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, m;
long long dp[1005][1 << 13]; // dp[column][mask]
bool valid[1 << 13][1 << 13]; // valid transitions

// Check if we can move from mask cur to next
void precompute(int pos, int cur, int next, int curMask, int nextMask) {
    if (pos == n) {
        valid[curMask][nextMask] = true;
        return;
    }
    
    if (cur & (1 << pos)) {
        // This cell is occupied by a previous column
        precompute(pos + 1, cur, next, curMask, nextMask);
    } else {
        // Placing a vertical domino
        precompute(pos + 1, cur | (1 << pos), next | (1 << pos), 
                   curMask, nextMask | (1 << pos));
        
        // Placing a horizontal domino (if there is space)
        if (pos + 1 < n && !(cur & (1 << (pos + 1)))) {
            precompute(pos + 2, cur | (1 << pos) | (1 << (pos + 1)), 
                       next, curMask, nextMask);
        }
    }
}

int main() {
    cin >> n >> m;
    
    // Precompute valid transitions
    for (int mask = 0; mask < (1 << n); mask++) {
        precompute(0, mask, 0, mask, 0);
    }
    
    dp[0][0] = 1; // Initial configuration
    
    for (int col = 0; col < m; col++) {
        for (int mask = 0; mask < (1 << n); mask++) {
            if (dp[col][mask] == 0) continue;
            
            for (int nextMask = 0; nextMask < (1 << n); nextMask++) {
                if (valid[mask][nextMask]) {
                    dp[col + 1][nextMask] += dp[col][mask];
                }
            }
        }
    }
    
    cout << dp[m][0] << endl; // All cells must be covered
    
    return 0;
}
```

Complexity: $O(N \cdot M \cdot 2^N \cdot 2^N)$ for precomputation, $O(M \cdot 2^{2N})$ for DP.

## 3. Bitmask DP

Used when we have a small set of elements ($N \le 20$) and need to store information about subsets.

**Example: Travelling Salesman Problem (TSP)**

State: $dp[mask][i]$ = the minimum cost to visit the vertices in $mask$ and finish at vertex $i$.

```cpp
const int MAXN = 20;
const int INF = 1e9;
int dist[MAXN][MAXN];
int dp[1 << MAXN][MAXN];
int n;

int tsp() {
    // Initialization
    for (int mask = 0; mask < (1 << n); mask++) {
        for (int i = 0; i < n; i++) {
            dp[mask][i] = INF;
        }
    }
    
    // Start from vertex 0
    dp[1][0] = 0;
    
    // For each mask (set of visited vertices)
    for (int mask = 1; mask < (1 << n); mask++) {
        for (int i = 0; i < n; i++) {
            if (!(mask & (1 << i))) continue; // i must be in the mask
            if (dp[mask][i] == INF) continue;
            
            // Try to add vertex j
            for (int j = 0; j < n; j++) {
                if (mask & (1 << j)) continue; // j is already visited
                
                int newMask = mask | (1 << j);
                dp[newMask][j] = min(dp[newMask][j], 
                                     dp[mask][i] + dist[i][j]);
            }
        }
    }
    
    // Find the minimum cost for visiting all vertices
    int fullMask = (1 << n) - 1;
    int result = INF;
    for (int i = 0; i < n; i++) {
        result = min(result, dp[fullMask][i] + dist[i][0]); // return to start
    }
    
    return result;
}
```

**Optimization: Subset Sum with bitwise operations**

If we have a set of numbers and want to find all possible sums, we can use `bitset`:

```cpp
#include <bitset>

bitset<100005> dp;

void subsetSum(vector<int>& nums) {
    dp[0] = 1; // sum 0 is possible
    
    for (int x : nums) {
        dp |= (dp << x); // add x to all possible sums
    }
}
```

## 4. Digit DP

Used for counting numbers in an interval $[L, R]$ that satisfy a certain property.

**Approach:** Count numbers from $[0, R]$ minus numbers from $[0, L-1]$.

State: $dp[pos][tight][started][...]$ where:
- $pos$ = current position in the number
- $tight$ = whether we are still restricted by the upper bound
- $started$ = whether we have started placing non-zero digits
- additional parameters depending on the task

**Example:** Counting numbers in $[L, R]$ with a sum of digits divisible by $K$.

```cpp
#include <bits/stdc++.h>
using namespace std;

string num;
int K;
int dp[20][2][2][200]; // [pos][tight][started][sum % K]

// Recursive function for digit DP
int solve(int pos, int tight, int started, int sum) {
    // Base case: all digits processed
    if (pos == (int)num.size()) {
        return started && (sum % K == 0);
    }
    
    // Memoization
    if (dp[pos][tight][started][sum] != -1) {
        return dp[pos][tight][started][sum];
    }
    
    int limit = tight ? (num[pos] - '0') : 9;
    int result = 0;
    
    for (int digit = 0; digit <= limit; digit++) {
        int newTight = tight && (digit == limit);
        int newStarted = started || (digit > 0);
        int newSum = sum + digit;
        
        result += solve(pos + 1, newTight, newStarted, newSum);
    }
    
    return dp[pos][tight][started][sum] = result;
}

int countNumbers(long long x) {
    if (x < 0) return 0;
    num = to_string(x);
    memset(dp, -1, sizeof(dp));
    return solve(0, 1, 0, 0);
}

int main() {
    long long L, R;
    cin >> L >> R >> K;
    
    int answer = countNumbers(R) - countNumbers(L - 1);
    cout << answer << endl;
    
    return 0;
}
```

**Other applications of Digit DP:**
- Numbers without consecutive identical digits.
- Numbers with exactly K different digits.
- Numbers whose digits form an increasing/decreasing sequence.
- Numbers divisible by the sum of their digits.

## 5. Transition Optimizations

Many DP tasks have a formula of the form:
$dp[i] = \min_{j < i} \{ dp[j] + cost(j, i) \}$

Direct calculation is $O(N^2)$. The goal is to reduce it to $O(N \log N)$ or $O(N)$.

### 5.1. Convex Hull Trick (CHT)

Applicable when $cost(j, i)$ can be represented as a linear function: $cost(j, i) = b_j \cdot a_i + c_j$.

Then: $dp[i] = \min_{j < i} \{ dp[j] + b_j \cdot a_i + c_j \} = \min_{j < i} \{ (dp[j] + c_j) + b_j \cdot a_i \}$.

Each $j$ defines a line with slope $m_j = b_j$ and y-intercept $c_j = dp[j] + c_j$.

**Idea:** We maintain the lower convex hull of the lines. For each $a_i$, we find the line with the minimum value.

**Implementation with Deque (when slopes are monotonic):**

```cpp
struct Line {
    long long m, c; // y = m*x + c
    long long eval(long long x) { return m * x + c; }
};

deque<Line> hull;

// Check if the middle line is redundant
bool bad(Line l1, Line l2, Line l3) {
    // l2 is bad if l1 and l3 intersect before l1 and l2
    return (__int128)(l3.c - l1.c) * (l1.m - l2.m) <= 
           (__int128)(l2.c - l1.c) * (l1.m - l3.m);
}

void addLine(Line newLine) {
    // Remove bad lines from the end
    while (hull.size() >= 2 && 
           bad(hull[hull.size()-2], hull[hull.size()-1], newLine)) {
        hull.pop_back();
    }
    hull.push_back(newLine);
}

long long query(long long x) {
    // If x values are monotonic, we can remove from the beginning
    while (hull.size() >= 2 && hull[0].eval(x) >= hull[1].eval(x)) {
        hull.pop_front();
    }
    return hull[0].eval(x);
}
```

**Example task:** We have arrays $a[]$ and $b[]$. We must calculate:
$dp[i] = \min_{j < i} \{ dp[j] + b[j] \cdot a[i] \}$.

```cpp
const int MAXN = 100005;
long long a[MAXN], b[MAXN], dp[MAXN];
int n;

void solve() {
    dp[0] = 0;
    addLine({b[0], dp[0]});
    
    for (int i = 1; i < n; i++) {
        dp[i] = query(a[i]);
        addLine({b[i], dp[i]});
    }
}
```

Complexity: $O(N)$ if slopes and $x$ values are monotonic.

### 5.2. Divide and Conquer Optimization

Applicable when the optimal transition point is monotonic: $opt[i] \le opt[i+1]$.

Condition (Quadrangle Inequality): $cost(a, c) + cost(b, d) \le cost(a, d) + cost(b, c)$ for $a \le b \le c \le d$.

**Example:** $dp[i][k]$ = minimum cost to divide the first $i$ elements into $k$ groups.

```cpp
const int MAXN = 5005;
const long long INF = 1e18;
long long dp_table[MAXN][MAXN];
long long cost_table[MAXN][MAXN]; // precomputed cost

void compute(int k, int l, int r, int optL, int optR) {
    if (l > r) return;
    
    int mid = (l + r) / 2;
    long long best = INF;
    int bestPos = -1;
    
    // Searching for the optimal position in a limited interval
    for (int j = optL; j <= min(mid, optR); j++) {
        long long curr = dp_table[j][k-1] + cost_table[j+1][mid];
        if (curr < best) {
            best = curr;
            bestPos = j;
        }
    }
    
    dp_table[mid][k] = best;
    
    // Recursively for the left and right halves
    compute(k, l, mid - 1, optL, bestPos);
    compute(k, mid + 1, r, bestPos, optR);
}

void solve() {
    // Initialization
    for (int i = 0; i < n; i++) {
        dp_table[i][0] = INF;
        dp_table[i][1] = cost_table[0][i];
    }
    
    // For each k
    for (int k = 2; k <= maxK; k++) {
        compute(k, 0, n - 1, 0, n - 1);
    }
}
```

Complexity: $O(K \cdot N \log N)$ instead of $O(K \cdot N^2)$.

### 5.3. Knuth Optimization

Applicable for interval DP tasks with the quadrangle inequality.

Conditions:
1. $opt[i][j-1] \le opt[i][j] \le opt[i+1][j]$ (monotonicity of the optimum).
2. $cost(i, j)$ satisfies the quadrangle inequality.

**Example:** Optimal Binary Search Tree (Optimal BST).

```cpp
const int MAXN = 1005;
long long dp_interval[MAXN][MAXN];  // dp[i][j] = min cost for interval [i, j]
int opt_pos[MAXN][MAXN];            // opt[i][j] = optimal root position
long long cost_interval[MAXN][MAXN]; // cost for interval

void solve() {
    // Base case
    for (int i = 0; i < n; i++) {
        dp_interval[i][i] = cost_interval[i][i];
        opt_pos[i][i] = i;
    }
    
    // By length of the interval
    for (int len = 2; len <= n; len++) {
        for (int i = 0; i + len - 1 < n; i++) {
            int j = i + len - 1;
            dp_interval[i][j] = INF;
            
            // Restricting the search with Knuth Optimization
            for (int k = opt_pos[i][j-1]; k <= opt_pos[i+1][j]; k++) {
                long long curr = dp_interval[i][k-1] + dp_interval[k+1][j] + cost_interval[i][j];
                if (curr < dp_interval[i][j]) {
                    dp_interval[i][j] = curr;
                    opt_pos[i][j] = k;
                }
            }
        }
    }
}
```

Complexity: $O(N^2)$ instead of $O(N^3)$.

## 6. SOS DP (Sum Over Subsets)

For calculating $F[mask] = \sum_{sub \subseteq mask} A[sub]$ for all masks.

**Naive approach:** For each mask, iterate through all its subsets.
```cpp
for (int mask = 0; mask < (1 << n); mask++) {
    for (int sub = mask; ; sub = (sub - 1) & mask) {
        F[mask] += A[sub];
        if (sub == 0) break;
    }
}
```
Complexity: $O(3^N)$ - because there are $\binom{N}{k}$ masks with $k$ ones, each of which has $2^k$ submasks.

**SOS DP approach:** We process the bits one by one.

Idea: $dp[mask][i]$ = sum of all submasks of `mask` that differ only in the first $i$ bits.

```cpp
// Initialization
for (int mask = 0; mask < (1 << n); mask++) {
    dp[mask][0] = A[mask];
}

// Iterating over bits
for (int i = 0; i < n; i++) {
    for (int mask = 0; mask < (1 << n); mask++) {
        if (mask & (1 << i)) {
            // If the i-th bit is 1, we add the submasks with 0 at this position
            dp[mask][i+1] = dp[mask][i] + dp[mask ^ (1 << i)][i];
        } else {
            // If the i-th bit is 0, we add nothing new
            dp[mask][i+1] = dp[mask][i];
        }
    }
}

// Optimized version without the second dimension
for (int i = 0; i < n; i++) {
    for (int mask = 0; mask < (1 << n); mask++) {
        if (mask & (1 << i)) {
            dp[mask] += dp[mask ^ (1 << i)];
        }
    }
}
```

Complexity: $O(N \cdot 2^N)$.

**Applications:**
- Counting subsets with certain properties.
- Convolution of two arrays over bitmasks.
- Tasks with unions and intersections of sets.

**Example task:** We have $N$ integers. For each number, we want to know how many numbers from the set are its submasks (have only bits that are set in the current number).

```cpp
int freq_table[1 << 20];

void countSubsets(vector<int>& nums) {
    // freq_table[mask] = how many times the mask occurs
    for (int x : nums) {
        freq_table[x]++;
    }
    
    // SOS DP
    for (int i = 0; i < 20; i++) {
        for (int mask = 0; mask < (1 << 20); mask++) {
            if (mask & (1 << i)) {
                freq_table[mask] += freq_table[mask ^ (1 << i)];
            }
        }
    }
    
    // freq_table[mask] now contains the number of submasks
}
```

## 7. Practical Tips and Common Errors

### 7.1. Memory Optimization

When the DP array is multi-dimensional, we can often reduce the dimensionality:
- If $dp[i]$ depends only on $dp[i-1]$, use two arrays or a rolling array.
- For interval DP, process by interval length.

```cpp
// Instead of dp[n][k], we use only two rows
long long curr_row[MAXK], prev_row[MAXK];

for (int i = 0; i < n; i++) {
    for (int j = 0; j < k; j++) {
        curr_row[j] = prev_row[j] + transition_val[i][j];
    }
    swap(curr_row, prev_row);
}
```

### 7.2. Choosing Data Structures

For optimization of DP transitions:
- **Segment Tree / Fenwick Tree**: For range queries (min, max, sum).
- **Monotonic Deque**: For sliding window minimum/maximum.
- **Set/Map**: For dynamically maintaining candidates.
- **Li Chao Tree**: For Convex Hull Trick with arbitrary slopes.

### 7.3. Common Errors

1. **Incorrect initialization**: Ensure that the starting states are correct.
2. **Overflow**: Use `long long` where necessary.
3. **Incorrect iteration order**: In some optimizations, the order is critical.
4. **Forgetting edge cases**: Empty sets, single elements, etc.

```cpp
// Example: Correct initialization for minimization
const long long INF = 1e18;
for (int i = 0; i < n; i++) {
    dp[i] = INF; // Not 1e9, because there might be overflow!
}
dp[0] = 0; // Base case
```

## 8. Practice Tasks

### 8.1. Tree DP
1. **CSES Tree Matching**: Maximum matching in a tree.
2. **CSES Tree Distances I**: Maximum distance from each vertex.
3. **CSES Tree Distances II**: Sum of distances (Rerooting).
4. **Codeforces 1092F**: Tree with Maximum Cost.

### 8.2. Bitmask DP
1. **CSES Hamiltonian Flights**: Counting Hamiltonian paths.
2. **CSES Elevator Rides**: Optimal distribution with a constraint.
3. **Codeforces 453B**: Little Pony and Harmony Chest.
4. **AtCoder DP Contest - O**: Matching.

### 8.3. Digit DP
1. **SPOJ GONE**: Numbers with a certain sum of digits.
2. **Codeforces 628D**: Magic Numbers.
3. **LightOJ 1068**: Investigating Palindromes (with digit DP).

### 8.4. Optimizations
1. **Codeforces 319C**: Kalila and Dimna (Convex Hull Trick).
2. **APIO 2010**: Commando (CHT).
3. **IOI 2014**: Holiday (Divide and Conquer).
4. **CEOI 2004**: Two Sawmills (Knuth Optimization).

### 8.5. SOS DP
1. **Codeforces 165E**: Compatible Numbers.
2. **Codeforces 449D**: Jzzhu and Numbers.
3. **CSES Sum of Four Values**: (with bitmasks).

## 9. Additional Techniques

### 9.1. DP with Matrix Exponentiation

For DP with linear transitions, when $N$ is very large, we can use matrix multiplication.

**Example:** Fibonacci with matrices in $O(\log N)$:

```cpp
typedef vector<vector<long long>> Matrix;

Matrix multiply(Matrix& A, Matrix& B) {
    int n = A.size();
    Matrix C(n, vector<long long>(n, 0));
    
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            for (int k = 0; k < n; k++) {
                C[i][j] += A[i][k] * B[k][j];
            }
        }
    }
    return C;
}

Matrix matPow(Matrix A, long long p) {
    int n = A.size();
    Matrix result(n, vector<long long>(n, 0));
    
    // Identity matrix
    for (int i = 0; i < n; i++) result[i][i] = 1;
    
    while (p > 0) {
        if (p & 1) result = multiply(result, A);
        A = multiply(A, A);
        p >>= 1;
    }
    
    return result;
}
```

### 9.2. Monotone Queue Optimization

For optimization of DP of the type $dp[i] = \min_{i-k \le j < i} dp[j] + cost[j]$.

```cpp
deque<int> dq; // stores indices

for (int i = 0; i < n; i++) {
    // Remove elements outside the window
    while (!dq.empty() && dq.front() < i - k) {
        dq.pop_front();
    }
    
    // Find the minimum in the window
    if (!dq.empty()) {
        dp[i] = dp[dq.front()] + cost_val[i];
    }
    
    // Add the current element
    while (!dq.empty() && dp[dq.back()] >= dp[i]) {
        dq.pop_back();
    }
    dq.push_back(i);
}
```

## 🏁 Conclusion

Advanced DP is the art of turning an "impossible" $O(N^2)$ or $O(N^3)$ solution into $O(N \log N)$ or $O(N)$ through mathematical observations and data structures.

**Key steps in solving Advanced DP tasks:**

1. **Recognizing the type**: Tree DP, Bitmask DP, Digit DP, Profile DP?
2. **Formulating the states**: What must we remember to calculate the answer?
3. **Finding the transitions**: How do we combine smaller subtasks?
4. **Optimization**: Is there monotonicity? Can we use CHT, D&C, Knuth?
5. **Implementation**: Watch out for overflow, correct initialization, edge cases.

Practice regularly and analyze the solutions of tasks from competitions. Over time, you will develop an intuition for when which technique is applicable.

**Resources for additional learning:**
- CSES Problem Set (Advanced DP section).
- Codeforces EDU section.
- AtCoder DP Contest.
- CP-Algorithms website.
- IOI/CEOI/APIO archives with tasks.

Good luck with advanced dynamic programming! 🚀