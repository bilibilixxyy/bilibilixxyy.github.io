---
layout: post
title: 945. Snakes and Ladders
---
### Question
On an N x N `board`, the numbers from `1` to `N*N` are written
_boustrophedonically_   **starting from the bottom  left of the board**, and
alternating direction each row.  For example, for a 6 x 6 board, the numbers
are written as follows:

    
    
    ![](https://assets.leetcode.com/uploads/2018/09/23/snakes.png)
    

You start on square `1` of the board (which is always in the last row and
first column).  Each move, starting from square `x`, consists of the
following:

  * You choose a destination square `S` with number `x+1`, `x+2`, `x+3`, `x+4`, `x+5`, or `x+6`, provided this number is `<= N*N`. 
    * (This choice simulates the result of a standard 6-sided die roll: ie., there are always **at most 6 destinations, regardless of the size of the board**.)
  * If `S` has a snake or ladder, you move to the destination of that snake or ladder.  Otherwise, you move to `S`.

A board square on row `r` and column `c` has a "snake or ladder" if
`board[r][c] != -1`.  The destination of that snake or ladder is
`board[r][c]`.

Note that you only take a snake or ladder at most once per move: if the
destination to a snake or ladder is the start of another snake or ladder, you
do **not** continue moving.   (For example, if the board is `[[4,-1],[-1,3]]`,
and on the first move your destination square is `2`, then you finish your
first move at `3`, because you do **not** continue moving to `4`.)

Return the least number of moves required to reach square N*N.  If it is not
possible, return `-1`.

**Example 1:**

    
    
    **Input:** [
    [-1,-1,-1,-1,-1,-1],
    [-1,-1,-1,-1,-1,-1],
    [-1,-1,-1,-1,-1,-1],
    [-1,35,-1,-1,13,-1],
    [-1,-1,-1,-1,-1,-1],
    [-1,15,-1,-1,-1,-1]]
    **Output:** 4
    **Explanation:**
    At the beginning, you start at square 1 [at row 5, column 0].
    You decide to move to square 2, and must take the ladder to square 15.
    You then decide to move to square 17 (row 3, column 5), and must take the snake to square 13.
    You then decide to move to square 14, and must take the ladder to square 35.
    You then decide to move to square 36, ending the game.
    It can be shown that you need at least 4 moves to reach the N*N-th square, so the answer is 4.
    

**Note:**

  1. `2 <= board.length = board[0].length <= 20`
  2. `board[i][j]` is between `1` and `N*N` or is equal to `-1`.
  3. The board square with number `1` has no snake or ladder.
  4. The board square with number `N*N` has no snake or ladder.

### Solution 1
    
    
     class Solution {
        public int snakesAndLadders(int[][] board) {
            int n = board.length;
            int[] arr = new int[n * n];
            int i = n - 1, j = 0, index = 0, inc = 1;
            while (index < n * n) {
                arr[index++] = board[i][j];
                if (inc == 1 && j == n - 1) {
                    inc = -1;
                    i--;
                } else if (inc == -1 && j == 0) {
                    inc = 1;
                    i--;
                } else {
                    j += inc;
                }
            }
            boolean[] visited = new boolean[n * n];
            Queue<Integer> q = new LinkedList<>();
            int start = arr[0] > -1 ? arr[0] - 1 : 0;
            q.offer(start);
            visited[start] = true;
            int step = 0;
            while (!q.isEmpty()) {
                int size = q.size();
                while (size-- > 0) {
                    int cur = q.poll();
                    if (cur == n * n - 1) {
                        return step;
                    }
                    for (int next = cur + 1; next <= Math.min(cur + 6, n * n - 1); next++) {
                        int dest = arr[next] > -1 ? arr[next] - 1 : next;
                        if (!visited[dest]) {
                            visited[dest] = true;
                            q.offer(dest);
                        }
                    }
                }
                step++;
            }
            return -1;
        }
    }
    


### Solution 2
I drew this diagram.  
Hope it help understand the problem.  
![](https://assets.leetcode.com/users/lee215/image_1537671763.png)

 **Python:**

    
    
        def snakesAndLadders(self, board):
            n = len(board)
            need = { 1: 0}
            bfs = [1]
            for x in bfs:
                for i in range(x + 1, x + 7):
                    a, b = (i - 1) / n, (i - 1) % n
                    nxt = board[~a][b if a % 2 == 0 else ~b]
                    if nxt > 0: i = nxt
                    if i == n * n: return need[x] + 1
                    if i not in need:
                        need[i] = need[x] + 1
                        bfs.append(i)
            return -1
    


### Solution 3
    
    
        public int snakesAndLadders(int[][] board) {
            int n = board.length;
            Queue<Integer> queue = new LinkedList<>();
            queue.offer(1);
            boolean[] visited = new boolean[n * n + 1];
            for (int move = 0; !queue.isEmpty(); move++) {
                for (int size = queue.size(); size > 0; size--) {
                    int num = queue.poll();
                    if (visited[num]) continue;
                    visited[num] = true;
                    if (num == n * n) return move;
                    for (int i = 1; i <= 6 && num + i <= n * n; i++) {
                        int next = num + i;
                        int value = getBoardValue(board, next);
                        if (value > 0) next = value;
                        if (!visited[next]) queue.offer(next);
                    }
                }
            }
            return -1;
        }
    
        private int getBoardValue(int[][] board, int num) {
            int n = board.length;
            int r = (num - 1) / n;
            int x = n - 1 - r;
            int y = r % 2 == 0 ? num - 1 - r * n : n + r * n - num;
            return board[x][y];
        }
    



