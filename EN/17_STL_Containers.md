# 📦 STL Containers: Data Structures

Standard Template Library (STL) provides ready-made, optimized data structures.

## 📂 Main Categories

### 1. Sequence Containers
- `std::vector`: Dynamic array. Fast index access ($O(1)$).
- `std::deque`: Double-ended queue. Fast add/remove at both ends.
- `std::list`: Doubly linked list. Fast insert anywhere, but slow access.

### 2. Associative Containers (Sorted)
- `std::set`: Set of unique elements. Implemented via Red-Black Tree.
- `std::map`: Key-value pairs.
- Operations: Search, insert, delete in **$O(\log N)$**.

### 3. Unordered Containers (Hash-based)
- `std::unordered_set` / `std::unordered_map`.
- Operations: Average **$O(1)$**.

## 🛠️ Container Choice
- Need fast index access? $\to$ `vector`.
- Need frequent add/remove from both ends? $\to$ `deque`.
- Search is critical? $\to$ `unordered_map`.

---

## 🏁 Conclusion
Correct container choice often determines if a program fits the time limit. Knowing the complexity of every operation is mandatory.
