# 🧱 Heaps: Mergeable Heaps

## 👈 Leftist Heap

Binary tree obeying heap property.
- **S-value (dist)**: Distance to nearest `null` descendant.
- **Leftist Property**: $dist(left) \ge dist(right)$. Always "heavy" on left.
- Path to right is short ($O(\log N)$).
- **Merge**: Merge right paths. If property violated, swap left and right child.
- All operations are $O(\log N)$.

## 📐 Skew Heap

Self-adjusting version of Leftist Heap (without storing $dist$).
- On every merge **always** swap left and right child.
- Amortized complexity $O(\log N)$. Very easy to implement.

```cpp
Node* merge(Node* a, Node* b) {
    if (!a || !b) return a ? a : b;
    if (a->val > b->val) swap(a, b);
    a->right = merge(a->right, b);
    swap(a->left, a->right);
    return a;
}
```

---

## 🏁 Conclusion

Skew Heap is the "Splay tree" of heaps – brilliantly simple and effective. Use it when you need merge.
