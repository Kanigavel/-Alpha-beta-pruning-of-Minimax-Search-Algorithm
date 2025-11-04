<h1>ExpNo 7 : Implement Alpha-beta pruning of Minimax Search Algorithm for a Simple TIC-TAC-TOE game</h1> 
<h3>Name: Kanigavel M      </h3>
<h3>Register Number:212224240070          </h3>
<H3>Aim:</H3>
<p>
Implement Alpha-beta pruning of Minimax Search Algorithm for a Simple TIC-TAC-TOE game
</p>
<h1>GOALS of Alpha-Beta Pruning in MiniMax Search Algorithm</h1>

<h3>Improve the decision-making efficiency of the computer player by reducing the number of evaluated nodes in the game tree.</h3>
<h3>Tic-Tac-Toe game implementation incorporating the Alpha-Beta pruning and the Minimax algorithm with Python Code.</h3>
<h1>IMPLEMENTATION</h1>

The project involves developing a Tic-Tac-Toe game implementation incorporating the Alpha-Beta pruning with the Minimax algorithm. Using this algorithm, the computer player analyzes the game state, evaluates possible moves, and selects the optimal action based on the anticipated outcomes.

<h1>The Minimax algorithm</h1>

recursively evaluates all possible moves and their potential outcomes, creating a game tree.

<h1>Alpha-Beta pruning</h1>

Alpha–Beta (𝛼−𝛽) algorithm is actually an improved minimax using a heuristic. It stops evaluating a move when it makes sure that it’s worse than a previously examined move. Such moves need not to be evaluated further.

When added to a simple minimax algorithm, it gives the same output but cuts off certain branches that can’t possibly affect the final decision — dramatically improving the performance
<hr>
<h2>Sample Input and Output:</h2>

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/8d5e329a-9aff-41a6-bcf0-46efa10e1b92)
![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/438b242d-54ba-443e-b040-a936e6ae3b55)
![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/99a33390-fa11-4ade-a19f-e93bcd7aaec9)
![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/440797bd-53cb-49c1-b18d-89776864c3e7)
![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/81575a16-26b2-46f1-a8ac-27c9ed0a0fe5)

<h3> PROGRAM </h3>

          # tic_tac_toe_alpha_beta.py
          # Simple Tic-Tac-Toe with Minimax + Alpha-Beta pruning
          # Human = 'X', Computer = 'O' by default
      
      import math
      from typing import List, Optional, Tuple
      
      # ---------- Board utilities ----------
      def create_board() -> List[str]:
          # 9-element list for 3x3 board, indices 0..8
          return [' ' for _ in range(9)]
      
      def print_board(board: List[str]) -> None:
          print()
          for row in range(3):
              r = board[row*3:(row+1)*3]
              print(' ' + ' | '.join(r))
              if row < 2:
                  print("---+---+---")
          print()
      
      def available_moves(board: List[str]) -> List[int]:
          return [i for i, v in enumerate(board) if v == ' ']
      
      def is_full(board: List[str]) -> bool:
          return all(cell != ' ' for cell in board)
      
      def winner(board: List[str]) -> Optional[str]:
          win_lines = [
              (0,1,2),(3,4,5),(6,7,8),  # rows
              (0,3,6),(1,4,7),(2,5,8),  # cols
              (0,4,8),(2,4,6)           # diagonals
          ]
          for a,b,c in win_lines:
              if board[a] == board[b] == board[c] and board[a] != ' ':
                  return board[a]
          return None
      
      # ---------- Evaluation function ----------
      def evaluate(board: List[str], computer: str, human: str) -> int:
          w = winner(board)
          if w == computer:
              return +10
          elif w == human:
              return -10
          else:
              return 0
      
      # ---------- Minimax with Alpha-Beta pruning ----------
      def minimax(board: List[str], depth: int, is_maximizing: bool,
                  alpha: int, beta: int, computer: str, human: str) -> Tuple[int, Optional[int]]:
          """
          Returns (score, move_index). move_index is the best move for the current player.
          """
          score = evaluate(board, computer, human)
          if score != 0 or is_full(board) or depth == 0:
              return score, None
      
          best_move = None
      
          if is_maximizing:
              max_eval = -math.inf
              for move in available_moves(board):
                  board[move] = computer
                  eval_score, _ = minimax(board, depth-1, False, alpha, beta, computer, human)
                  board[move] = ' '
                  if eval_score > max_eval:
                      max_eval = eval_score
                      best_move = move
                  alpha = max(alpha, eval_score)
                  if beta <= alpha:
                      break  # beta cut-off
              return max_eval, best_move
          else:
              min_eval = math.inf
              for move in available_moves(board):
                  board[move] = human
                  eval_score, _ = minimax(board, depth-1, True, alpha, beta, computer, human)
                  board[move] = ' '
                  if eval_score < min_eval:
                      min_eval = eval_score
                      best_move = move
                  beta = min(beta, eval_score)
                  if beta <= alpha:
                      break  # alpha cut-off
              return min_eval, best_move
      
      # ---------- AI move selection ----------
      def best_move_for_computer(board: List[str], computer: str, human: str) -> int:
          # set depth to remaining cells to search to terminal states (small search space)
          depth = len(available_moves(board))
          score, move = minimax(board, depth, True, -math.inf, math.inf, computer, human)
          # move can be None if board is terminal; handle gracefully
          return move if move is not None else -1
      
      # ---------- Game loop (console) ----------
      def play_game(human_starts: bool = True):
          board = create_board()
          human = 'X'
          computer = 'O'
          current_player = 'human' if human_starts else 'computer'
      
          print("Tic-Tac-Toe: You are 'X'. Computer is 'O'.")
          print_board(board)
      
          while True:
              w = winner(board)
              if w or is_full(board):
                  break
      
              if current_player == 'human':
                  # Human's turn
                  while True:
                      try:
                          user_input = input("Enter your move (1-9) or 'q' to quit: ").strip()
                          if user_input.lower() == 'q':
                              print("Game aborted by user.")
                              return
                          pos = int(user_input) - 1
                          if pos not in range(9):
                              print("Invalid position. Choose from 1 to 9.")
                              continue
                          if board[pos] != ' ':
                              print("Cell already taken. Choose another.")
                              continue
                          board[pos] = human
                          break
                      except ValueError:
                          print("Please enter a number from 1 to 9.")
                  print_board(board)
                  current_player = 'computer'
              else:
                  # Computer's turn
                  print("Computer is thinking...")
                  move = best_move_for_computer(board, computer, human)
                  if move == -1:
                      # no valid move
                      break
                  board[move] = computer
                  print(f"Computer placed '{computer}' in position {move+1}")
                  print_board(board)
                  current_player = 'human'
      
          # final result
          w = winner(board)
          if w == human:
              print("Congratulations! You (X) win!")
          elif w == computer:
              print("Computer (O) wins. Better luck next time!")
          else:
              print("It's a draw!")
      
      # ---------- If run as script ----------
      if __name__ == "__main__":
          # Example: human starts. Prompt user if they want to start or let computer start.
          start = input("Do you want to start first? (y/n) [y]: ").strip().lower()
          human_starts = (start != 'n')
          play_game(human_starts=human_starts)

          
<h3>OUTPUT</h3>

<img width="634" height="518" alt="image" src="https://github.com/user-attachments/assets/331ca596-4228-464d-a573-37cd682ce454" />
<img width="741" height="718" alt="image" src="https://github.com/user-attachments/assets/3f67e183-6b8f-4ebf-826b-6d966cc002df" />
<img width="740" height="538" alt="image" src="https://github.com/user-attachments/assets/37f0abbb-f30f-4d5b-9b5f-e28051eafc49" />

<h3> Result </h3>
Thus, the program to implement Alpha–Beta Pruning of the Minimax Search Algorithm for a simple Tic-Tac-Toe game was successfully executed.

