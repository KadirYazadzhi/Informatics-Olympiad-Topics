# #️⃣ Хеширане и Хеш Таблици: Пълно Ръководство

Хеширането е процес на преобразуване на данни с произволен размер (низове, обекти, големи числа) в число с фиксиран размер (хеш). В състезателното програмиране това е един от най-често използваните похвати за оптимизация на търсенето и сравнението на данни.

---

## 1. Хеш Таблици (Hash Tables)

Хеш таблицата е структура от данни, която имплементира асоциативен масив. Тя позволява вмъкване, изтриване и търсене средно за $O(1)$.

### 1.1. Колизии и справяне с тях
Колизия възниква, когато два различни ключа $k_1$ и $k_2$ дават един и същ хеш $h(k_1) = h(k_2)$.
1.  **Chaining (Метод на верижките)**: Всяка клетка от таблицата съдържа списък (`std::vector` или `std::list`). Всички елементи с един и същ хеш се добавят в списъка.
2.  **Open Addressing (Отворена адресация)**: Ако клетката е заета, търсим друга свободна клетка по определено правило (линейно пробване, квадратично пробване или двойно хеширане).

---

## 2. Проблемът със стандартните хеш функции в C++

Класовете `std::unordered_map` и `std::unordered_set` в C++ използват вградена хеш функция. За цели числа (`int`, `long long`) в GCC тази функция често е просто **идентитет** ($f(x) = x$).

Това прави структурата изключително уязвима! Ако някой знае размера на таблицата (обикновено просто число), той може да подаде числа като $size, 2 \cdot size, 3 \cdot size, \dots$, които всички ще попаднат в една и съща клетка. Това превръща $O(1)$ в $O(N)$ и води до $O(N^2)$ за цялата програма.

### 2.1. Решение: Custom Hash с Рандомизация

Винаги използвайте собствена хеш функция, която включва случаен елемент (seed), генериран при стартиране на програмата.

```cpp
#include <chrono>
#include <unordered_map>
using namespace std;

struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        // Изпитан алгоритъм за разбъркване на битовете
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

// Използване
unordered_map<long long, int, custom_hash> safe_map;
unordered_set<long long, custom_hash> safe_set;
```

### 2.2. gp_hash_table от GNU C++

GNU предоставя `__gnu_pbds::gp_hash_table`, която е по-бърза от `unordered_map`:
```cpp
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;

gp_hash_table<int, int, custom_hash> table;
```

---

## 3. Полиномно Хеширане на Низове (Rolling Hash)

Позволява сравнение на низове за $O(1)$ след $O(N)$ предварително изчисление.

Формула: $H(S) = (S[0] \cdot P^{n-1} + S[1] \cdot P^{n-2} + \dots + S[n-1] \cdot P^0) \pmod M$

### 3.1. Префиксни хешове

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
    
    // Хеш на подниза [l, r)
    long long getHash(int l, int r) {
        long long result = (hash[r] - hash[l] * pow[r - l]) % MOD;
        if (result < 0) result += MOD;
        return result;
    }
};
```

### 3.2. Двойно хеширане

За максимална сигурност срещу колизии:
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

### 3.3. Приложения на Rolling Hash

**Най-дълъг общ подниз:**
```cpp
int longestCommonSubstring(string s1, string s2) {
    int left = 0, right = min(s1.size(), s2.size());
    
    while (left < right) {
        int mid = (left + right + 1) / 2;
        
        unordered_set<long long> hashes;
        StringHash hash1(s1);
        
        // Добавяме всички подсиренци с дължина mid от s1
        for (int i = 0; i <= s1.size() - mid; i++) {
            hashes.insert(hash1.getHash(i, i + mid));
        }
        
        // Проверяваме дали някой подсиренец от s2 го има
        StringHash hash2(s2);
        bool found = false;
        for (int i = 0; i <= s2.size() - mid; i++) {
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

**Намиране на палиндром (Rabin-Karp):**
```cpp
bool isPalindrome(const string& s, int l, int r, 
                  StringHash& forwardHash, StringHash& reverseHash) {
    int n = s.size();
    return forwardHash.getHash(l, r) == 
           reverseHash.getHash(n - r, n - l);
}
```

---

## 4. Хеширане на 2D структури

### 4.1. Матрици

```cpp
long long hashMatrix(vector<vector<int>>& matrix) {
    long long hash = 0;
    long long pow = 1;
    const long long P = 31;
    const long long MOD = 1e9 + 7;
    
    for (auto& row : matrix) {
        for (int val : row) {
            hash = (hash + val * pow) % MOD;
            pow = (pow * P) % MOD;
        }
    }
    
    return hash;
}
```

### 4.2. Точки в равнината

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

## 5. Zobrist Hashing (Хеширане на множества)

Използва се в игри (шах) или задачи за съвпадение на множества.

```cpp
class ZobristHash {
    vector<uint64_t> keys;
    
public:
    ZobristHash(int n) : keys(n) {
        mt19937_64 rng(chrono::steady_clock::now().time_since_epoch().count());
        for (int i = 0; i < n; i++) {
            keys[i] = rng();
        }
    }
    
    uint64_t hash(const vector<int>& elements) {
        uint64_t h = 0;
        for (int x : elements) {
            h ^= keys[x];
        }
        return h;
    }
    
    // Добавяне/премахване на елемент
    uint64_t toggle(uint64_t currentHash, int element) {
        return currentHash ^ keys[element];
    }
};
```

### Приложение в шах
```cpp
class ChessBoard {
    uint64_t boardHash;
    ZobristHash zobrist;
    
public:
    ChessBoard() : zobrist(64 * 12) { // 64 позиции, 12 фигури
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

За статични множества (не се променят след създаването):

```cpp
class PerfectHash {
    vector<vector<int>> table;
    int n;
    
public:
    void build(const vector<int>& keys) {
        n = keys.size();
        table.resize(n);
        
        // Използваме универсално хеширане
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

Вероятностна структура за проверка на принадлежност:

```cpp
class BloomFilter {
    vector<bool> bits;
    int k; // Брой хеш функции
    
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

За разпределени системи:

```cpp
class ConsistentHash {
    map<int, string> ring;
    hash<string> hasher;
    
public:
    void addServer(const string& server) {
        int hash = hasher(server) % 1000000;
        ring[hash] = server;
    }
    
    string getServer(const string& key) {
        int hash = hasher(key) % 1000000;
        auto it = ring.lower_bound(hash);
        
        if (it == ring.end()) {
            return ring.begin()->second;
        }
        return it->second;
    }
};
```

---

## 9. Практически съвети

1. **Винаги рандомизирайте**: Използвайте custom hash с time-based seed
2. **Двойно хеширане**: За критични приложения използвайте 2 или повече хеш функции
3. **Избор на модул**: Използвайте прости числа за MOD (1e9+7, 1e9+9)
4. **База P**: Изберете P > размера на азбуката (обикновено 31, 53, или 263)
5. **Колизии**: Помнете че хешовете винаги имат шанс за колизия

## 10. Задачи за упражнение

1. **Codeforces 4C**: Registration System
2. **CSES String Matching**: Rolling Hash
3. **Codeforces 1200E**: Compress Words (Suffix/Prefix matching)
4. **SPOJ ADASTRNG**: Substring operations
5. **Codeforces 1418C**: Complex state hashing

## 🏁 Заключение

Хеширането е мощен инструмент, който превръща сложни сравнения в прости числови операции. От низове до графи, хешовете намират приложение навсякъде в състезателното програмиране. Винаги внимавайте с колизиите и използвайте рандомизация за защита срещу anti-hash тестове. Практикувайте с различни задачи и експериментирайте с различни хеш функции! 🚀