# 📂 Advanced Data Structures: Expert Level

## 🌳 Implicit Treap (Rope)

Instead of using element value as key for BST, use its **index** in the array.
Since indices change on insertion, we don't store them explicitly. Instead, store `size` (subtree size).
- Root index is `size(left)`.
- Index of element in right subtree is `size(left) + 1 + index_in_right`.

**Applications**:
- Insert/delete element at arbitrary position in array in $O(\log N)$.
- Reverse range in $O(\log N)$ with lazy propagation.
- Cyclic shift (Cut & Paste) of subarray.
- This is the structure behind `std::rope` (but better to write your own).

---

## 📜 Persistent Segment Tree

Allows access to previous versions of the structure.
- When updating a node, instead of changing its value, create a **copy** of the node and modify the copy. Parent must also be copied to point to the new copy.
- This creates $O(\log N)$ new nodes per operation.

**Application: $K$-th number in range $[L, R]$**
1. Build persistent tree where version $i$ contains numbers from $A[1 \dots i]$.
2. Count of numbers with value in range $[val\_min, val\_max]$ in interval $[L, R]$ equals `count(root[R]) - count(root[L-1])`.
3. Descend the tree (like binary search) to find the $K$-th. Complexity $O(\log N)$.

---

## 🌲 Link-Cut Tree

Dynamic structure for maintaining a forest of trees.
- Operations: `link(u, v)` (connect two trees), `cut(u, v)` (split), `path_query(u, v)`.
- Uses **Splay Trees** to represent paths (Heavy-Light Decomposition concept, but dynamic).
- Complexity: $O(\log N)$ amortized.

---

## 🏁 Conclusion

Implicit Treap is the most powerful structure for arrays. It can replace segment tree and do things it can't (insert, reverse). Persistence is key for problems with "history" or 2D problems reduced to 1D + time.
