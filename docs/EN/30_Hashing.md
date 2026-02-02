# #️⃣ Hashing and Hash Tables: Complete Guide

Hashing is the process of converting data of arbitrary size (strings, objects, large numbers) into a fixed-size number (hash). In competitive programming, this is one of the most frequently used techniques for optimizing data search and comparison.

---

## 1. Hash Tables

A hash table is a data structure that implements an associative array. It allows for insertion, deletion, and searching in $O(1)$ on average.

### 1.1. Collisions and Handling Them
A collision occurs when two different keys $k_1$ and $k_2$ yield the same hash $h(k_1) = h(k_2)$.
1.  **Chaining**: Each cell of the table contains a list (`std::vector` or `std::list`). All elements with the same hash are added to the list.
2.  **Open Addressing**: If the cell is occupied, we search for another free cell according to a specific rule (linear probing, quadratic probing, or double hashing).

---

## 2. The Problem with Standard Hash Functions in C++

The `std::unordered_map` and `std::unordered_set` classes in C++ use a built-in hash function. For integers (`int`, `long long`) in GCC, this function is often simply the **identity** ($f(x) = x$).

This makes the structure extremely vulnerable! If someone knows the size of the table (usually a prime number), they can provide numbers like $size, 2 \cdot size, 3 \cdot size, \dots$, all of which will fall into the same cell. This turns $O(1)$ into $O(N)$ and leads to $O(N^2)$ for the entire program.

### 2.1. Solution: Custom Hash with Randomization

Always use your own hash function that includes a random element (seed) generated at the start of the program.

```cpp
#include <chrono>
#include <unordered_map>
using namespace std;

struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        // A proven algorithm for bit mixing
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }

    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

// Usage
unordered_map<long long, int, custom_hash> safe_map;
unordered_set<long long, custom_hash> safe_set;
```

### 2.2. gp_hash_table from GNU C++

GNU provides `__gnu_pbds::gp_hash_table`, which is faster than `unordered_map`:
```cpp
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;

gp_hash_table<int, int, custom_hash> table;
```

---

## 3. Polynomial String Hashing (Rolling Hash)

Allows for string comparison in $O(1)$ after $O(N)$ precalculation.

Formula: $H(S) = (S[0] \cdot P^{n-1} + S[1] \cdot P^{n-2} + \dots + S[n-1] \cdot P^0) \pmod M$

### 3.1. Prefix Hashes

```cpp
const int MOD = 1e9 + 7;
const int P = 31;

class StringHash {
    vector<long long> hash, pow;
    
public:
    StringHash(const string& s) {
        int n = s.size();
        hash.resize(n + 1);
        pow.resize(n + 1);
        pow[0] = 1;
        
        for (int i = 0; i < n; i++) {
            hash[i + 1] = (hash[i] * P + s[i]) % MOD;
            pow[i + 1] = (pow[i] * P) % MOD;
        }
    }
    
    // Hash of the substring [l, r)
    long long getHash(int l, int r) {
        long long result = (hash[r] - hash[l] * pow[r - l]) % MOD;
        if (result < 0) result += MOD;
        return result;
    }
};
```

### 3.2. Double Hashing

For maximum security against collisions:
```cpp
const int MOD1 = 1e9 + 7;
const int MOD2 = 1e9 + 9;
const int P1 = 31;
const int P2 = 37;

struct DoubleHash {
    long long hash1, hash2;
    
    DoubleHash(long long h1 = 0, long long h2 = 0) : hash1(h1), hash2(h2) {}
    
    bool operator==(const DoubleHash& other) const {
        return hash1 == other.hash1 && hash2 == other.hash2;
    }
};
```

### 3.3. Applications of Rolling Hash

**Longest Common Substring:**
```cpp
int longestCommonSubstring(string s1, string s2) {
    int left = 0, right = min(s1.size(), s2.size());
    
    while (left < right) {
        int mid = (left + right + 1) / 2;
        
        unordered_set<long long> hashes;
        StringHash hash1(s1);
        
        // Add all substrings of length mid from s1
        for (int i = 0; i <= (int)s1.size() - mid; i++) {
            hashes.insert(hash1.getHash(i, i + mid));
        }
        
        // Check if any substring from s2 is present
        StringHash hash2(s2);
        bool found = false;
        for (int i = 0; i <= (int)s2.size() - mid; i++) {
            if (hashes.count(hash2.getHash(i, i + mid))) {
                found = true;
                break;
            }
        }
        
        if (found) {
            left = mid;
        } else {
            right = mid - 1;
        }
    }
    
    return left;
}
```

**Finding a palindrome (Rabin-Karp):**
```cpp
bool isPalindrome(const string& s, int l, int r, 
                  StringHash& forwardHash, StringHash& reverseHash) {
    int n = s.size();
    return forwardHash.getHash(l, r) == 
           reverseHash.getHash(n - r, n - l);
}
```

---

## 4. Hashing of 2D Structures

### 4.1. Matrices

```cpp
long long hashMatrix(vector<vector<int>>& matrix) {
    long long hash_val = 0;
    long long p_pow = 1;
    const long long P = 31;
    const long long MOD = 1e9 + 7;
    
    for (auto& row : matrix) {
        for (int val : row) {
            hash_val = (hash_val + val * p_pow) % MOD;
            p_pow = (p_pow * P) % MOD;
        }
    }
    
    return hash_val;
}
```

### 4.2. Points in the Plane

```cpp
struct Point {
    int x, y;
    
    bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
};

struct PointHash {
    size_t operator()(const Point& p) const {
        return hash<long long>{}(((long long)p.x << 32) | p.y);
    }
};

unordered_set<Point, PointHash> points;
```

---

## 5. Zobrist Hashing (Set Hashing)

Used in games (chess) or tasks for set matching.

```cpp
class ZobristHash {
public:
    vector<uint64_t> keys;
    
    ZobristHash(int n) : keys(n) {
        mt19937_64 rng(chrono::steady_clock::now().time_since_epoch().count());
        for (int i = 0; i < n; i++) {
            keys[i] = rng();
        }
    }
    
    uint64_t hashSet(const vector<int>& elements) {
        uint64_t h = 0;
        for (int x : elements) {
            h ^= keys[x];
        }
        return h;
    }
    
    // Adding/removing an element
    uint64_t toggle(uint64_t currentHash, int element) {
        return currentHash ^ keys[element];
    }
};
```

### Application in Chess
```cpp
class ChessBoard {
    uint64_t boardHash;
    ZobristHash zobrist;
    
public:
    ChessBoard() : zobrist(64 * 12) { // 64 positions, 12 figures
        boardHash = 0;
    }
    
    void movePiece(int from, int to, int piece) {
        boardHash ^= zobrist.keys[from * 12 + piece];
        boardHash ^= zobrist.keys[to * 12 + piece];
    }
};
```

---

## 6. Perfect Hashing

For static sets (they do not change after creation):

```cpp
class PerfectHash {
    vector<vector<int>> table;
    int n;
    
public:
    void build(const vector<int>& keys) {
        n = keys.size();
        table.resize(n);
        
        // Using universal hashing
        for (int key : keys) {
            int bucket = key % n;
            table[bucket].push_back(key);
        }
    }
    
    bool contains(int key) {
        int bucket = key % n;
        for (int x : table[bucket]) {
            if (x == key) return true;
        }
        return false;
    }
};
```

---

## 7. Bloom Filter

A probabilistic structure for checking membership:

```cpp
class BloomFilter {
    vector<bool> bits;
    int k; // Number of hash functions
    
    size_t hash1(int x) { return x % bits.size(); }
    size_t hash2(int x) { return (x * 31) % bits.size(); }
    size_t hash3(int x) { return (x * 37) % bits.size(); }
    
public:
    BloomFilter(int size, int numHashes) : bits(size), k(numHashes) {}
    
    void add(int x) {
        bits[hash1(x)] = true;
        bits[hash2(x)] = true;
        bits[hash3(x)] = true;
    }
    
    bool mightContain(int x) {
        return bits[hash1(x)] && bits[hash2(x)] && bits[hash3(x)];
    }
};
```

---

## 8. Consistent Hashing

For distributed systems:

```cpp
class ConsistentHash {
    map<int, string> ring;
    hash<string> hasher;
    
public:
    void addServer(const string& server) {
        int h = hasher(server) % 1000000;
        ring[h] = server;
    }
    
    string getServer(const string& key) {
        int h = hasher(key) % 1000000;
        auto it = ring.lower_bound(h);
        
        if (it == ring.end()) {
            return ring.begin()->second;
        }
        return it->second;
    }
};
```

---

## 9. Practical Tips

1. **Always randomize**: Use a custom hash with a time-based seed.
2. **Double hashing**: For critical applications, use 2 or more hash functions.
3. **Choosing a modulus**: Use prime numbers for MOD (1e9+7, 1e9+9).
4. **Base P**: Choose P > alphabet size (usually 31, 53, or 263).
5. **Collisions**: Remember that hashes always have a chance of collision.

## 10. Practice Tasks

1. **Codeforces 4C**: Registration System.
2. **CSES String Matching**: Rolling Hash.
3. **Codeforces 1200E**: Compress Words (Suffix/Prefix matching).
4. **SPOJ ADASTRNG**: Substring operations.
5. **Codeforces 1418C**: Complex state hashing.

## 🏁 Conclusion

Hashing is a powerful tool that turns complex comparisons into simple numerical operations. From strings to graphs, hashes find application everywhere in competitive programming. Always watch out for collisions and use randomization for protection against anti-hash tests. Practice with various tasks and experiment with different hash functions! 🚀