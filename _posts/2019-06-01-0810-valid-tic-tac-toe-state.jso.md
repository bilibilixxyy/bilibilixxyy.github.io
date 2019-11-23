---
layout: post
title: 810. Valid Tic-Tac-Toe State
---
### Question
A Tic-Tac-Toe board is given as a string array `board`. Return True if and
only if it is possible to reach this board position during the course of a
valid tic-tac-toe game.

The `board` is a 3 x 3 array, and consists of characters `" "`, `"X"`, and
`"O"`.  The " " character represents an empty square.

Here are the rules of Tic-Tac-Toe:

  * Players take turns placing characters into empty squares (" ").
  * The first player always places "X" characters, while the second player always places "O" characters.
  * "X" and "O" characters are always placed into empty squares, never filled ones.
  * The game ends when there are 3 of the same (non-empty) character filling any row, column, or diagonal.
  * The game also ends if all squares are non-empty.
  * No more moves can be played if the game is over.

    
    
     **Example 1:**
    **Input:** board = ["O   ", "   ", "   "]
    **Output:** false
    **Explanation:** The first player always plays "X".
    
    **Example 2:**
    **Input:** board = ["XOX", " X ", "   "]
    **Output:** false
    **Explanation:** Players take turns making moves.
    
    **Example 3:**
    **Input:** board = ["XXX", "   ", "OOO"]
    **Output:** false
    
    **Example 4:**
    **Input:** board = ["XOX", "O O", "XOX"]
    **Output:** true
    

**Note:**

  * `board` is a length-3 array of strings, where each string `board[i]` has length 3.
  * Each `board[i][j]` is a character in the set `{" ", "X", "O"}`.

### Solution 1
When `turns` is 1, X moved. When `turns` is 0, O moved. `rows` stores the
number of X or O in each row. `cols` stores the number of X or O in each
column. `diag` stores the number of X or O in diagonal. `antidiag` stores the
number of X or O in antidiagonal. When any of the value gets to 3, it means X
wins. When any of the value gets to -3, it means O wins.

When X wins, O cannot move anymore, so `turns` must be 1. When O wins, X
cannot move anymore, so `turns` must be 0. Finally, when we return, `turns`
must be either 0 or 1, and X and O cannot win at same time.

    
    
    class Solution {
        public boolean validTicTacToe(String[] board) {
            int turns = 0;
            boolean xwin = false; 
            boolean owin = false;
            int[] rows = new int[3];
            int[] cols = new int[3];
            int diag = 0;
            int antidiag = 0;
    				
            for (int i = 0; i < 3; i++) {
                for (int j = 0; j < 3; j++) {
                    if (board[i].charAt(j) == 'X') {
                        turns++; rows[i]++; cols[j]++;
                        if (i == j) diag++;
                        if (i + j == 2) antidiag++;
                    } else if (board[i].charAt(j) == 'O') {
                        turns--; rows[i]--; cols[j]--;
                        if (i == j) diag--;
                        if (i + j == 2) antidiag--;
                    }
                }
            }
    		
            xwin = rows[0] == 3 || rows[1] == 3 || rows[2] == 3 || 
                   cols[0] == 3 || cols[1] == 3 || cols[2] == 3 || 
                   diag == 3 || antidiag == 3;
            owin = rows[0] == -3 || rows[1] == -3 || rows[2] == -3 || 
                   cols[0] == -3 || cols[1] == -3 || cols[2] == -3 || 
                   diag == -3 || antidiag == -3;
            
            if (xwin && turns == 0 || owin && turns == 1) {
                return false;
            }
            return (turns == 0 || turns == 1) && (!xwin || !owin);
        }
    }
    


### Solution 2
To find the validity of a given board, we could first think about the cases
where the board is invalid

  1. Since X starts first, x_count >= o_count. So if o_count > x_count, we can return False
  2. Since the players take turns, we could also return False if x_count-o_count>1

After the corner cases, this is the algorithm used:

  1. If player O has a winning condition, also check the following:  
a) If player X also has a winning condition, return False  
b) If x_count != o_count , return False (Since player O always plays second,
it has to meet this condition always)

  2. If player X has a winning condition, check the following:  
a) If x_count != o_count + 1, return False (Since player X plays the first
move, if player X wins, the player X's count would be 1 more than player O)

    
    
    class Solution(object):
        def check_win_positions(self, board, player):
    	"""
    	Check if the given player has a win position.
    	Return True if there is a win position. Else return False.
    	"""
            #Check the rows
            for i in range(len(board)):
                if board[i][0] == board[i][1] == board[i][2] == player:
                    return True                        
    
            #Check the columns
            for i in range(len(board)):
                if board[0][i] == board[1][i] == board[2][i] == player:
                    return True 
    										
            #Check the diagonals
            if board[0][0] == board[1][1] == board[2][2]  == player or \
                   board[0][2] == board[1][1] == board[2][0] == player:
                return True
    						
            return False
            
        def validTicTacToe(self, board):
            """
            :type board: List[str]
            :rtype: bool
            """
            
            x_count, o_count = 0, 0
            for i in range(len(board)):
                for j in range(len(board[0])):
                    if board[i][j] == "X":
                        x_count += 1
                    elif  board[i][j] == "O":
                        o_count += 1
    										
            if o_count > x_count or x_count-o_count>1:
                return False
            
            if self.check_win_positions(board, 'O'):
                if self.check_win_positions(board, 'X'):
                    return False
                return o_count == x_count
            
            if self.check_win_positions(board, 'X') and x_count!=o_count+1:
                return False
    
            return True
    


### Solution 3
    
    
    def validTicTacToe(self, board):
        b = '|'.join(board)
        x, o = (any(p*3 in b[s::d] for s in range(9) for d in (1, 3, 4, 5)) for p in 'XO')
        m = b.count('X') - b.count('O')
        return m == (not o) if m else not x
    

  

### Explanation and stuff

I first join a board like `"XOX", "O O", "XOX"` into a single string like
`"XOX|O O|XOX"`. Then I can easily get rows/columns/diagonals by one-
dimensional slicing. For example the main diagonal (with digits for clarity):

    
    
    >>> "123|456|789"[::5]
    '159'
    

In general index delta `d=1` goes to the right, `d=4` goes down, `d=5` goes
down-right and `d=3` goes down-left. And I try that from every needed start
index `s` and a few more. The slices aren't exactly the
rows/columns/diagonals, but they include them and they don't include anything
they shouldn't (thanks to the `|` row separator).

So to find out whether player `'X'` has a winning line, I just check whether
`"XXX"` is in any of those slices.

Then I also determine the move difference `m`, how many more moves player
`'X'` made. Again it is helpful to have the board in a single string that I
can just ask for the counts.

If they players made the same number of moves, then I just need to check that
`'X'` has no winning line. Otherwise `'X`' must have made exactly one more
move and `'O'` must not have a winning line. That's what my last line checks.
You could also do

    
    
        return (m == 0 and not x) or (m == 1 and not o)
    

but I just had some fun making it smaller. I also considered negative the
meaning of `x` and `o` so they tell whether the players _don't_ have winning
lines:

    
    
     def validTicTacToe(self, board):
        b = '|'.join(board)
        x, o = (not any(p*3 in b[s::d] for s in range(9) for d in (1, 3, 4, 5)) for p in 'XO')
        m = b.count('X') - b.count('O')
        return m == o if m else x
    

That saves a bit overall but made the already longest line longer, plus I just
don't like the negative view.

Golf-ish version just for fun (that's for Python 3, for Python 2 the deltas
need to be (1,7,8,9)):

    
    
    def validTicTacToe(self, b):
        b=str(b);x,o=(not any(p*3in
        b[s::d]for s in range(99)for
        d in(1,6,7,8))for p in'XO')
        c=b.count;m=c('X')-c('O')
        return m==o if m else x
    



