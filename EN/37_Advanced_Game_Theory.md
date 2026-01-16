# 🎮 Game Theory: Expert Games

## 🌳 Green Hackenbush

Game on a graph. Edges are colored Green, Red, or Blue. There is "ground".
- **Rules**: Players cut edges. Anything not connected to ground disappears.
  - L (Left) cuts Blue.
  - R (Right) cuts Red.
  - Green can be cut by both.
- **Analysis**: This is a partizan game. Values are **Surreal Numbers**.
  - Each edge is assigned value (e.g., $+1$ for blue, $-1$ for red).
  - Green Hackenbush on trees is solved via XOR sums (colon principle).

---

## 📶 Staircase Nim

Coins on steps $0, 1, \dots, N$. Move: move any number of coins from step $i$ to $i-1$. Coins from step 0 leave the game.
- **Strategy**: Coins on even positions ($0, 2, 4\dots$) are useless (if opponent moves from 2 to 1, we move from 1 to 0).
- Game is equivalent to Nim with only piles at **odd** positions ($1, 3, 5\dots$).

---

## 🏁 Conclusion

Hackenbush is the gateway to surreal numbers, but in competitions, it rarely goes beyond "Hackenbush on Trees" (which is impartial). Staircase Nim is a frequent trick variation.
