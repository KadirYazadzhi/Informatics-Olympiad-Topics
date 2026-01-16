# 🧮 Linear Algebra: Bases and Rank

## 🎹 Matrix Rank

Rank is the number of linearly independent rows (or columns).
- It is the size of the largest non-zero minor.
- Found after Gaussian elimination (number of non-zero rows).

## ⊕ XOR Basis (Again) as Gauss

Linear Basis (Topic 22) is actually Gaussian elimination on a matrix of bits.
- Each row is a number.
- Goal is to get diagonal (or row echelon) matrix.
- If we can fully zero out a number via XOR with basis, it is linearly dependent (can be represented by others).

---

## 🏁 Conclusion

In competitions, "Linear Algebra" almost always means either matrix multiplication for DP or Gauss/XOR basis. Truly complex things (eigenvalues and vectors) are extremely rare.
