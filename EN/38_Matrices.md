# 📊 Matrices: Theorems and Applications

## 🌳 Matrix Tree Theorem (Kirchhoff's Theorem)

The number of spanning trees in a graph $G$ is equal to any cofactor of its Laplacian matrix.
- **Laplacian Matrix ($L$)**: $L_{i,i} = \deg(i)$, $L_{i,j} = -1$ (if edge $i-j$ exists), else 0.
- Delete $k$-th row and $k$-th column.
- Determinant of resulting matrix gives tree count.
- Determinant is calculated via Gaussian elimination in $O(N^3)$.

---

## 🏁 Gaussian Elimination Modulo 2 (XOR Systems)

For systems where addition is XOR (`^`) and multiplication is AND (`&`).
- Use `std::bitset` for matrix rows.
- Operations become very fast ($N^3 / 64$).
- **Application**: "Lights Out" game, finding subset with given XOR.

---

## 🏁 Conclusion

Matrix Tree Theorem transforms the hard combinatorial problem of counting trees into a standard linear algebra problem.
