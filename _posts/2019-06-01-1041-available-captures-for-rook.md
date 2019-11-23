---
layout: post
title: 1041. Available Captures for Rook
---
### Question
On an 8 x 8 chessboard, there is one white rook.  There also may be empty
squares, white bishops, and black pawns.  These are given as characters 'R',
'.', 'B', and 'p' respectively. Uppercase characters represent white pieces,
and lowercase characters represent black pieces.

The rook moves as in the rules of Chess: it chooses one of four cardinal
directions (north, east, west, and south), then moves in that direction until
it chooses to stop, reaches the edge of the board, or captures an opposite
colored pawn by moving to the same square it occupies.  Also, rooks cannot
move into the same square as other friendly bishops.

Return the number of pawns the rook can capture in one move.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2019/02/20/1253_example_1_improved.PNG)

    
    
     **Input:** [[".",".",".",".",".",".",".","."],[".",".",".","p",".",".",".","."],[".",".",".","R",".",".",".","p"],[".",".",".",".",".",".",".","."],[".",".",".",".",".",".",".","."],[".",".",".","p",".",".",".","."],[".",".",".",".",".",".",".","."],[".",".",".",".",".",".",".","."]]
    **Output:** 3
    **Explanation:**
    In this example the rook is able to capture all the pawns.
    

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/02/19/1253_example_2_improved.PNG)

    
    
    **Input:** [[".",".",".",".",".",".",".","."],[".","p","p","p","p","p",".","."],[".","p","p","B","p","p",".","."],[".","p","B","R","B","p",".","."],[".","p","p","B","p","p",".","."],[".","p","p","p","p","p",".","."],[".",".",".",".",".",".",".","."],[".",".",".",".",".",".",".","."]]
    **Output:** 0
    **Explanation:**
    Bishops are blocking the rook to capture any pawn.
    

**Example 3:**

![](https://assets.leetcode.com/uploads/2019/02/20/1253_example_3_improved.PNG)

    
    
    **Input:** [[".",".",".",".",".",".",".","."],[".",".",".","p",".",".",".","."],[".",".",".","p",".",".",".","."],["p","p",".","R",".","p","B","."],[".",".",".",".",".",".",".","."],[".",".",".","B",".",".",".","."],[".",".",".","p",".",".",".","."],[".",".",".",".",".",".",".","."]]
    **Output:** 3
    **Explanation:**
    The rook can capture the pawns at positions b5, d6 and f5.
    



 **Note:**

  1. `board.length == board[i].length == 8`
  2. `board[i][j]` is either `'R'`, `'.'`, `'B'`, or `'p'`
  3. There is exactly one cell with `board[i][j] == 'R'`

### Solution 1
Search for the rock. From the rock, trace empty spaces in four directions.
Return 1 if we hit a pawn, zero otherwise.

Count and return captured pawns.

    
    
    int cap(vector<vector<char>>& b, int x, int y, int dx, int dy) {
      while (x >= 0 && x < b.size() && y >= 0 && y < b[x].size() && b[x][y] != 'B') {
        if (b[x][y] == 'p') return 1;
        x += dx, y += dy;
      }
      return 0;
    }
    int numRookCaptures(vector<vector<char>>& b) {
      for (auto i = 0; i < b.size(); ++i)
        for (auto j = 0; j < b[i].size(); ++j)
          if (b[i][j] == 'R') return cap(b,i,j,0,1)+cap(b,i,j,0,-1)+cap(b,i,j,1,0)+cap(b,i,j,-1,0);
      return 0;
    }
    

Java version:

    
    
    int cap(char[][] b, int x, int y, int dx, int dy) {
      while (x >= 0 && x < b.length && y >= 0 && y < b[x].length && b[x][y] != 'B') {
        if (b[x][y] == 'p') return 1;
        x += dx; y += dy;
      }
      return 0;
    }
    public int numRookCaptures(char[][] b) {
      for (int i = 0; i < b.length; ++i)
        for (int j = 0; j < b[i].length; ++j)
          if (b[i][j] == 'R') return cap(b,i,j,0,1)+cap(b,i,j,0,-1)+cap(b,i,j,1,0)+cap(b,i,j,-1,0);
      return 0;
    }
    

# Complexity Analysis

Runtime: O(n), where n is the total number of cells. In the worst case, we
need to check all cells to find the rock, and then check one row and one
column.  
Memory: O(1).


### Solution 2
 **Java**

    
    
         public int numRookCaptures(char[][] board) {
            int x0 = 0, y0 = 0, res = 0;
            for (int i = 0; i < 8; ++i)
                for (int j = 0; j < 8; ++j)
                    if (board[i][j] == 'R') {
                        x0 = i;
                        y0 = j;
                    }
    
            for (int[] d : new int[][] {{1, 0}, {0, 1}, { -1, 0}, {0, -1}}) {
                for (int x = x0 + d[0], y = y0 + d[1]; 0 <= x && x < 8 && 0 <= y && y < 8; x += d[0], y += d[1]) {
                    if (board[x][y] == 'p') res++;
                    if (board[x][y] != '.') break;
                }
            }
            return res;
        }
    

**Python:**

    
    
        def numRookCaptures(self, board):
             for i in range(8):
                for j in range(8):
                    if board[i][j] == 'R':
                        x0, y0 = i, j
            res = 0
            for i, j in [[1, 0], [0, 1], [-1, 0], [0, -1]]:
                x, y = x0 + i, y0 + j
                while 0 <= x < 8 and 0 <= y < 8:
                    if board[x][y] == 'p': res += 1
                    if board[x][y] != '.': break
                    x, y = x + i, y + j
            return res
    


### Solution 3
    
    
    const canCapture = (board, i, j) => {
        let captures = 0, dirs = [[1, 0], [-1, 0], [0, 1], [0, -1]];
        
        for (let dir of dirs) {
            let r = i + dir[0], c = j + dir[1];
                
            while (r >= 0 && r < 8 && c >= 0 && c < 8) {
                if (board[r][c] === 'p') { captures++; }
                if (board[r][c] !== '.') { break; }
                
                r += dir[0];
                c += dir[1];
            }
        }
        
        return captures;
    }
    
    const numRookCaptures = board => {
        for (let i = 0; i < 8; i++) {
            for (let j = 0; j < 8; j++) {
                if (board[i][j] === 'R') {
                    return canCapture(board, i, j);
                }
            }
        }
    };
    



