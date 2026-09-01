# ExpNo 7: Implement Alpha-Beta Pruning of Minimax Search Algorithm for a Simple TIC-TAC-TOE Game

| **Name**                       | Surya Prakash B  |
| ------------------------------ | - |
| **Register Number** | 212224230281  |

## Aim

To implement **Alpha-Beta Pruning of the Minimax Search Algorithm** for a simple Tic-Tac-Toe game using Python 3.

---

## Goals of Alpha-Beta Pruning

The main goals of Alpha-Beta Pruning are:

1. Improve the decision-making efficiency of the computer player.
2. Reduce the number of nodes evaluated in the game tree.
3. Select the optimal move using the Minimax algorithm.
4. Avoid evaluating branches that cannot affect the final decision.
5. Implement an intelligent Tic-Tac-Toe game using Python 3.

---

# Theory

## Minimax Algorithm

**Minimax** is a decision-making algorithm used in two-player games.

It recursively evaluates all possible moves and their possible outcomes to determine the best move.

In this Tic-Tac-Toe implementation:

```text
O → MAX player
X → MIN player
```

The evaluation scores are:

| Game Result | Score |
| ----------- | ----: |
| `O` wins    |  `+1` |
| Draw        |   `0` |
| `X` wins    |  `-1` |

The MAX player tries to maximize the score:

```text
MAX → +1
```

The MIN player tries to minimize the score:

```text
MIN → -1
```

---

# Alpha-Beta Pruning

**Alpha-Beta Pruning** is an optimization of the Minimax algorithm.

It produces the **same optimal decision as Minimax**, but avoids evaluating branches that cannot influence the final decision.

This makes the algorithm more efficient.

Alpha-Beta uses two values:

```text
Alpha (α)
Beta  (β)
```

### Alpha

`Alpha` represents the best value that the **MAX player** can guarantee so far.

Initially:

```text
α = -2
```

### Beta

`Beta` represents the best value that the **MIN player** can guarantee so far.

Initially:

```text
β = +2
```

Since this program only uses scores:

```text
-1, 0, +1
```

`-2` and `+2` are safe initial boundary values.

---

# How Alpha-Beta Pruning Works

Consider the following simplified tree:

```text
                    MAX
                  /     \
                MIN     MIN
               /  \     /  \
              3    5   2    ?
```

The first MIN node evaluates:

```text
MIN(3, 5) = 3
```

Therefore MAX currently knows:

```text
Alpha = 3
```

When the second MIN node finds a value of `2`:

```text
Beta = 2
```

Now:

```text
Alpha >= Beta
3 >= 2
```

The remaining branches under that MIN node do not need to be evaluated.

This is called **pruning**.

---

# Alpha-Beta Conditions

### At MAX Node

After obtaining a new maximum value:

```python
if maxv >= beta:
    return (maxv, px, py)
```

The remaining branches can be ignored.

Alpha is updated using:

```python
if maxv > alpha:
    alpha = maxv
```

---

### At MIN Node

After obtaining a new minimum value:

```python
if minv <= alpha:
    return (minv, qx, qy)
```

The remaining branches can be ignored.

Beta is updated using:

```python
if minv < beta:
    beta = minv
```

---

# Minimax vs Alpha-Beta Pruning

| Feature          | Minimax             | Alpha-Beta                  |
| ---------------- | ------------------- | --------------------------- |
| Decision quality | Optimal             | Optimal                     |
| Game tree        | Searches branches   | Prunes unnecessary branches |
| Alpha value      | No                  | Yes                         |
| Beta value       | No                  | Yes                         |
| Efficiency       | Lower               | Higher                      |
| Memory           | Similar             | Similar                     |
| Result           | Same optimal result | Same optimal result         |

The major advantage is:

```text
Alpha-Beta = Minimax + Pruning
```

---

# Implementation

The project implements a Tic-Tac-Toe game in which:

* `X` is the human player.
* `O` is the computer player.
* The computer uses Minimax with Alpha-Beta Pruning.
* Every possible move is recursively evaluated.
* Unnecessary branches are pruned.
* The computer selects the optimal move.

---

# Algorithm

1. Initialize an empty 3 × 3 Tic-Tac-Toe board.
2. Set player `X` as the first player.
3. Check whether the current game state is a terminal state.
4. If `X` wins, return `-1`.
5. If `O` wins, return `+1`.
6. If the board is full, return `0`.
7. For the MAX player `O`:

   * Generate every possible move.
   * Place `O` temporarily.
   * Call the MIN function recursively.
   * Select the maximum score.
   * Undo the move.
   * Update Alpha.
   * If `Alpha >= Beta`, prune the remaining branches.
8. For the MIN player `X`:

   * Generate every possible move.
   * Place `X` temporarily.
   * Call the MAX function recursively.
   * Select the minimum score.
   * Undo the move.
   * Update Beta.
   * If `Beta <= Alpha`, prune the remaining branches.
9. Continue until the game reaches a terminal state.
10. Select the optimal move.
11. Display the result.

---

# Python 3 Program

```python
import time


class Game:

    def __init__(self):
        self.initialize_game()


    # Initialize the game
    def initialize_game(self):

        self.current_state = [
            ['.', '.', '.'],
            ['.', '.', '.'],
            ['.', '.', '.']
        ]

        # X plays first
        self.player_turn = 'X'


    # Display the board
    def draw_board(self):

        for i in range(3):

            for j in range(3):
                print(
                    '{}|'.format(self.current_state[i][j]),
                    end=" "
                )

            print()

        print()


    # Check whether a move is valid
    def is_valid(self, px, py):

        if px < 0 or px > 2 or py < 0 or py > 2:
            return False

        elif self.current_state[px][py] != '.':
            return False

        return True


    # Check whether the game has ended
    def is_end(self):

        # Vertical win
        for i in range(3):

            if (
                self.current_state[0][i] != '.'
                and self.current_state[0][i]
                == self.current_state[1][i]
                == self.current_state[2][i]
            ):
                return self.current_state[0][i]


        # Horizontal win
        for i in range(3):

            if self.current_state[i] == ['X', 'X', 'X']:
                return 'X'

            elif self.current_state[i] == ['O', 'O', 'O']:
                return 'O'


        # Main diagonal
        if (
            self.current_state[0][0] != '.'
            and self.current_state[0][0]
            == self.current_state[1][1]
            == self.current_state[2][2]
        ):
            return self.current_state[0][0]


        # Secondary diagonal
        if (
            self.current_state[0][2] != '.'
            and self.current_state[0][2]
            == self.current_state[1][1]
            == self.current_state[2][0]
        ):
            return self.current_state[0][2]


        # Check whether board is full
        for i in range(3):

            for j in range(3):

                if self.current_state[i][j] == '.':
                    return None


        # Board full = draw
        return '.'


    # MAX player = O
    def max_alpha_beta(self, alpha, beta):

        maxv = -2

        px = None
        py = None

        result = self.is_end()


        # Terminal states
        if result == 'X':
            return (-1, 0, 0)

        elif result == 'O':
            return (1, 0, 0)

        elif result == '.':
            return (0, 0, 0)


        # Try all possible moves
        for i in range(3):

            for j in range(3):

                if self.current_state[i][j] == '.':

                    # Make O's move
                    self.current_state[i][j] = 'O'

                    # Call MIN
                    m, min_i, min_j = self.min_alpha_beta(
                        alpha,
                        beta
                    )

                    # Select maximum score
                    if m > maxv:

                        maxv = m
                        px = i
                        py = j

                    # Undo move
                    self.current_state[i][j] = '.'


                    # Alpha-Beta pruning
                    if maxv >= beta:
                        return (maxv, px, py)

                    # Update alpha
                    if maxv > alpha:
                        alpha = maxv


        return (maxv, px, py)


    # MIN player = X
    def min_alpha_beta(self, alpha, beta):

        minv = 2

        qx = None
        qy = None

        result = self.is_end()


        # Terminal states
        if result == 'X':
            return (-1, 0, 0)

        elif result == 'O':
            return (1, 0, 0)

        elif result == '.':
            return (0, 0, 0)


        # Try all possible moves
        for i in range(3):

            for j in range(3):

                if self.current_state[i][j] == '.':

                    # Make X's move
                    self.current_state[i][j] = 'X'

                    # Call MAX
                    m, max_i, max_j = self.max_alpha_beta(
                        alpha,
                        beta
                    )

                    # Select minimum score
                    if m < minv:

                        minv = m
                        qx = i
                        qy = j

                    # Undo move
                    self.current_state[i][j] = '.'


                    # Alpha-Beta pruning
                    if minv <= alpha:
                        return (minv, qx, qy)

                    # Update beta
                    if minv < beta:
                        beta = minv


        return (minv, qx, qy)


    # Play the game
    def play_alpha_beta(self):

        while True:

            self.draw_board()

            self.result = self.is_end()


            # Check whether game is over
            if self.result is not None:

                if self.result == 'X':
                    print('The winner is X!')

                elif self.result == 'O':
                    print('The winner is O!')

                elif self.result == '.':
                    print("It's a tie!")

                self.initialize_game()

                return


            # Human player's turn
            if self.player_turn == 'X':

                while True:

                    start = time.time()

                    m, qx, qy = self.min_alpha_beta(
                        -2,
                        2
                    )

                    end = time.time()


                    print(
                        'Evaluation time: {}s'.format(
                            round(end - start, 7)
                        )
                    )

                    print(
                        'Recommended move: X = {}, Y = {}'.format(
                            qx,
                            qy
                        )
                    )


                    # Get user input
                    px = int(
                        input('Insert the X coordinate: ')
                    )

                    py = int(
                        input('Insert the Y coordinate: ')
                    )


                    # Check move
                    if self.is_valid(px, py):

                        self.current_state[px][py] = 'X'

                        self.player_turn = 'O'

                        break

                    else:

                        print(
                            'The move is not valid! Try again.'
                        )


            # AI player's turn
            else:

                m, px, py = self.max_alpha_beta(
                    -2,
                    2
                )

                self.current_state[px][py] = 'O'

                self.player_turn = 'X'


# Main function
def main():

    game = Game()

    game.play_alpha_beta()


if __name__ == "__main__":
    main()
```

---

# Sample Input and Output

## Sample Game 1

![Sample Game 1](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/8d5e329a-9aff-41a6-bcf0-46efa10e1b92)

---

## Sample Game 2

![Sample Game 2](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/438b242d-54ba-443e-b040-a936e6ae3b55)

---

## Sample Game 3

![Sample Game 3](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/99a33390-fa11-4ade-a19f-e93bcd7aaec9)

---

## Sample Game 4

![Sample Game 4](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/440797bd-53cb-49c1-b18d-89776864c3e7)

---

## Sample Game 5

![Sample Game 5](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/81575a16-26b2-46f1-a8ac-27c9ed0a0fe5)

---

# Understanding the Program

## 1. Board Representation

The board is represented using a 3 × 3 list:

```python
self.current_state = [
    ['.', '.', '.'],
    ['.', '.', '.'],
    ['.', '.', '.']
]
```

Here:

```text
. → Empty
X → Human
O → Computer
```

---

## 2. Terminal State

The `is_end()` function checks:

* Vertical wins
* Horizontal wins
* Main diagonal
* Secondary diagonal
* Draw

The return values are:

```text
X → -1
O → +1
. → 0
None → Game continues
```

---

## 3. MAX Function

The computer plays as `O`.

Therefore, it tries to maximize the score.

```python
if m > maxv:
    maxv = m
```

The best possible result for `O` is:

```text
+1
```

---

## 4. MIN Function

The human player is `X`.

The Minimax algorithm assumes that `X` also plays optimally.

Therefore, `X` tries to minimize the score.

```python
if m < minv:
    minv = m
```

The best result for `X` is:

```text
-1
```

from the perspective of `O`.

---

# Understanding Alpha and Beta

The most important part of Alpha-Beta Pruning is understanding these two values.

### Alpha

```text
α = Best score MAX can guarantee
```

It starts at:

```text
α = -2
```

and increases as MAX finds better moves.

### Beta

```text
β = Best score MIN can guarantee
```

It starts at:

```text
β = +2
```

and decreases as MIN finds better moves.

---

# Pruning Condition

The central condition is:

```text
α >= β
```

When this happens, the remaining branches do not need to be evaluated.

At MAX:

```python
if maxv >= beta:
    return (maxv, px, py)
```

At MIN:

```python
if minv <= alpha:
    return (minv, qx, qy)
```

This is what makes Alpha-Beta faster than ordinary Minimax.

---

# Example of Alpha-Beta Pruning

Consider:

```text
                    MAX
                   /   \
                  /     \
                MIN     MIN
               /  \     /  \
              3    5   2    ?
```

First:

```text
MIN(3, 5) = 3
```

So:

```text
Alpha = 3
```

The second MIN node gets:

```text
Beta = 2
```

Now:

```text
Alpha >= Beta

3 >= 2
```

Therefore, the `?` branch is unnecessary.

It is **pruned**.

---

# Advantages

* Produces the same optimal result as Minimax.
* Reduces the number of nodes that need to be evaluated.
* Improves game-playing performance.
* Allows deeper searches in larger game trees.
* Especially effective when good moves are examined first.

---

# Limitations

* Still has exponential worst-case complexity.
* Efficiency depends on move ordering.
* More complex to implement than basic Minimax.
* Tic-Tac-Toe is small enough that ordinary Minimax is already practical.

---

# Complexity Analysis

Let:

* `b` = branching factor
* `d` = depth of the game tree

### Minimax

Worst-case time complexity:

```text
O(b^d)
```

### Alpha-Beta Pruning

Worst-case:

```text
O(b^d)
```

With excellent move ordering, the best case can approach:

```text
O(b^(d/2))
```

Therefore, Alpha-Beta Pruning can potentially allow the algorithm to search approximately twice as deep for the same amount of work.

### Space Complexity

The recursive search requires approximately:

```text
O(d)
```

stack space, excluding storage used for the board and other program structures.

---

# Minimax vs Alpha-Beta: Main Difference

```text
Minimax:

Generate
   ↓
Evaluate
   ↓
Explore all branches
   ↓
Choose best move
```

Alpha-Beta:

```text
Generate
   ↓
Evaluate
   ↓
Check Alpha/Beta
   ↓
Prune unnecessary branches
   ↓
Choose best move
```

Therefore:

```text
Alpha-Beta Pruning
        =
Minimax + Intelligent Pruning
```

---

# Key Concepts

| Concept            | Meaning                                                   |
| ------------------ | --------------------------------------------------------- |
| **Minimax**        | Algorithm for choosing optimal moves in a two-player game |
| **Alpha (α)**      | Best value currently guaranteed for MAX                   |
| **Beta (β)**       | Best value currently guaranteed for MIN                   |
| **Pruning**        | Skipping branches that cannot affect the final decision   |
| **MAX**            | Player attempting to maximize the score                   |
| **MIN**            | Player attempting to minimize the score                   |
| **Game Tree**      | All possible future game states                           |
| **Terminal State** | Win, loss, or draw                                        |
| **Backtracking**   | Undoing a simulated move                                  |
| **Optimal Move**   | Move producing the best guaranteed outcome                |

---

# Result

Thus, the **Alpha-Beta Pruning technique was successfully implemented along with the Minimax Search Algorithm** for a simple Tic-Tac-Toe game using Python 3. The algorithm efficiently eliminates unnecessary branches of the game tree while preserving the optimal decision of the Minimax algorithm.
