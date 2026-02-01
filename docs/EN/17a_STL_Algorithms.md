# ⚙️ Algorithms in STL

The `<algorithm>` library contains over 100 functions for working with containers.

## 🔢 Sorting and Searching
- `std::sort(begin, end)`: $O(N \log N)$.
- `std::stable_sort(begin, end)`: Preserves order of equals.
- `std::binary_search(begin, end, val)`: Returns `bool`.
- `std::lower_bound`: First element $\ge$ val.
- `std::upper_bound`: First element $>$ val.

## 🛠️ Data Manipulation
- `std::reverse(begin, end)`: Reverses order.
- `std::unique(begin, end)`: Removes adjacent duplicates (requires sorted array).
- `std::next_permutation`: Generates next arrangement.

## 📊 Mathematical Algorithms
- `std::max_element` / `std::min_element`: Finds extremas.
- `std::accumulate(begin, end, init)`: Sums elements.

---

## 🏁 Conclusion
Don't reinvent the wheel! STL algorithms are tested and often faster than hand-written versions. Use them to reduce errors in your code.
