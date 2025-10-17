<h1>ExpNo 6 : Implement Minimax Search Algorithm for a Simple TIC-TAC-TOE game</h1> 
<h3>Name: PREM PRASANTH J    </h3>
<h3>Register Number: 2305001028      </h3>
<H3>AIM:</H3>
<p>
    Implement Minimax Search Algorithm for a Simple TIC-TAC-TOE game
</p>

<H3>THEORY AND PROCEDURE:</H3>
To begin, let's start by defining what it means to play a perfect game of tic tac toe:

If I play perfectly, every time I play I will either win the game, or I will draw the game. Furthermore if I play against another perfect player, I will always draw the game.

How might we describe these situations quantitatively? Let's assign a score to the "end game conditions:"

I win, hurray! I get 10 points!
I lose, shit. I lose 10 points (because the other player gets 10 points)
I draw, whatever. I get zero points, nobody gets any points.
So now we have a situation where we can determine a possible score for any game end state.

Looking at a Brief Example
To apply this, let's take an example from near the end of a game, where it is my turn. I am X. My goal here, obviously, is to maximize my end game score.

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/498656fc-79ce-4234-a623-06568bad8dda)


If the top of this image represents the state of the game I see when it is my turn, then I have some choices to make, there are three places I can play, one of which clearly results in me wining and earning the 10 points. If I don't make that move, O could very easily win. And I don't want O to win, so my goal here, as the first player, should be to pick the maximum scoring move.

But What About O?
What do we know about O? Well we should assume that O is also playing to win this game, but relative to us, the first player, O wants obviously wants to chose the move that results in the worst score for us, it wants to pick a move that would minimize our ultimate score. Let's look at things from O's perspective, starting with the two other game states from above in which we don't immediately win:

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/029b1a70-e92e-46c0-9a32-d6aea98ecd9d)

The choice is clear, O would pick any of the moves that result in a score of -10.

Describing Minimax
The key to the Minimax algorithm is a back and forth between the two players, where the player whose "turn it is" desires to pick the move with the maximum score. In turn, the scores for each of the available moves are determined by the opposing player deciding which of its available moves has the minimum score. And the scores for the opposing players moves are again determined by the turn-taking player trying to maximize its score and so on all the way down the move tree to an end state.

A description for the algorithm, assuming X is the "turn taking player," would look something like:

If the game is over, return the score from X's perspective.
Otherwise get a list of new game states for every possible move
Create a scores list
For each of these states add the minimax result of that state to the scores list
If it's X's turn, return the maximum score from the scores list
If it's O's turn, return the minimum score from the scores list
You'll notice that this algorithm is recursive, it flips back and forth between the players until a final score is found.

Let's walk through the algorithm's execution with the full move tree, and show why, algorithmically, the instant winning move will be picked:

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/12b82542-54fb-47e7-8f76-b75fddc40f92)
<ul>
<li>It's X's turn in state 1. X generates the states 2, 3, and 4 and calls minimax on those states.</li>
<li>State 2 pushes the score of +10 to state 1's score list, because the game is in an end state.</li>
<li>State 3 and 4 are not in end states, so 3 generates states 5 and 6 and calls minimax on them, while state 4 generates states 7 and 8 and calls minimax on them.</li>
<li>State 5 pushes a score of -10 onto state 3's score list, while the same happens for state 7 which pushes a score of -10 onto state 4's score list.</li>
<li>State 6 and 8 generate the only available moves, which are end states, and so both of them add the score of +10 to the move lists of states 3 and 4.</li>
<li>Because it is O's turn in both state 3 and 4, O will seek to find the minimum score, and given the choice between -10 and +10, both states 3 and 4 will yield -10.</li>
<li>>Finally the score list for states 2, 3, and 4 are populated with +10, -10 and -10 respectively, and state 1 seeking to maximize the score will chose the winning move with score +10, state 2.</li
</ul>
##A Coded Version of Minimax Hopefully by now you have a rough sense of how th e minimax algorithm determines the best move to play. Let's examine my implementation of the algorithm to solidify the understanding:

Here is the function for scoring the game:

# @player is the turn taking player
def score(game)
    if game.win?(@player)
        return 10
    elsif game.win?(@opponent)
        return -10
    else
        return 0
    end
end
Simple enough, return +10 if the current player wins the game, -10 if the other player wins and 0 for a draw. You will note that who the player is doesn't matter. X or O is irrelevant, only who's turn it happens to be.

And now the actual minimax algorithm; note that in this implementation a choice or move is simply a row / column address on the board, for example [0,2] is the top right square on a 3x3 board.

def minimax(game)
    return score(game) if game.over?
    scores = [] # an array of scores
    moves = []  # an array of moves

    # Populate the scores array, recursing as needed
    game.get_available_moves.each do |move|
        possible_game = game.get_new_state(move)
        scores.push minimax(possible_game)
        moves.push move
    end

    # Do the min or the max calculation
    if game.active_turn == @player
        # This is the max calculation
        max_score_index = scores.each_with_index.max[1]
        @choice = moves[max_score_index]
        return scores[max_score_index]
    else
        # This is the min calculation
        min_score_index = scores.each_with_index.min[1]
        @choice = moves[min_score_index]
        return scores[min_score_index]
    end
end
## PROGRAM:
```python
import math

def print_board(b):
    for row in b:
        print(" | ".join(row))
        print("-" * 5)

def check_winner(b):
    for i in range(3):
        if b[i][0] == b[i][1] == b[i][2] != " ": return b[i][0]
        if b[0][i] == b[1][i] == b[2][i] != " ": return b[0][i]
    if b[0][0] == b[1][1] == b[2][2] != " ": return b[0][0]
    if b[0][2] == b[1][1] == b[2][0] != " ": return b[0][2]
    return None

def minimax(b, is_max):
    winner = check_winner(b)
    if winner == "O": return 1
    if winner == "X": return -1
    if all(cell != " " for row in b for cell in row): return 0

    if is_max:
        best = -math.inf
        for i in range(3):
            for j in range(3):
                if b[i][j] == " ":
                    b[i][j] = "O"
                    best = max(best, minimax(b, False))
                    b[i][j] = " "
        return best
    else:
        best = math.inf
        for i in range(3):
            for j in range(3):
                if b[i][j] == " ":
                    b[i][j] = "X"
                    best = min(best, minimax(b, True))
                    b[i][j] = " "
        return best

def best_move(b):
    best_score = -math.inf
    move = None
    for i in range(3):
        for j in range(3):
            if b[i][j] == " ":
                b[i][j] = "O"
                score = minimax(b, False)
                b[i][j] = " "
                if score > best_score:
                    best_score = score
                    move = (i, j)
    return move


board = [[" "]*3 for _ in range(3)]
print("You are X | AI is O")

while True:
    print_board(board)
    winner = check_winner(board)
    if winner or all(c != " " for r in board for c in r):
        print("Winner:", winner if winner else "Draw!")
        break

    try:
        r = int(input("Enter row (0-2): "))
        c = int(input("Enter col (0-2): "))
        if 0 <= r <= 2 and 0 <= c <= 2 and board[r][c] == " ":
            board[r][c] = "X"
        else:
            print("Invalid move! Try again.")
            continue
    except:
        print("Enter valid numbers!")
        continue

    if not check_winner(board):
        move = best_move(board)
        if move:
            board[move[0]][move[1]] = "O"

```

<hr>
<h2>INPUT AND OUTPUT:</h2>

<img width="339" height="512" alt="image" src="https://github.com/user-attachments/assets/a80dc512-3317-45be-a513-74768f13b5a1" />
<img width="277" height="537" alt="image" src="https://github.com/user-attachments/assets/e23955ce-2b7e-41a4-8ff6-62a35b81836e" />
<img width="266" height="320" alt="image" src="https://github.com/user-attachments/assets/5f38939f-0280-4a81-9d02-1502bc39fe13" />

</hr>

<h2>RESULT:</h2>
<p>Thus,Implementation of  Minimax Search Algorithm for a Simple TIC-TAC-TOE game wasa done successfully.</p>
