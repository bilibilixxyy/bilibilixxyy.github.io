---
layout: post
title: 576. Out of Boundary Paths
---
### Question
There is an **m** by **n** grid with a ball. Given the start coordinate
**(i,j)** of the ball, you can move the ball to **adjacent** cell or cross the
grid boundary in four directions (up, down, left, right). However, you can
**at most** move **N** times. Find out the number of paths to move the ball
out of grid boundary. The answer may be very large, return it after mod 10 9
\+ 7.



 **Example 1:**

    
    
     **Input:** m = 2, n = 2, N = 2, i = 0, j = 0
    **Output:** 6
    **Explanation:**
    ![](https://assets.leetcode.com/uploads/2018/10/13/out_of_boundary_paths_1.png)
    

**Example 2:**

    
    
    **Input:** m = 1, n = 3, N = 3, i = 0, j = 1
    **Output:** 12
    **Explanation:**
    ![](https://assets.leetcode.com/uploads/2018/10/12/out_of_boundary_paths_2.png)
    



 **Note:**

  1. Once you move the ball out of boundary, you cannot move it back.
  2. The length and height of the grid is in range [1,50].
  3. N is in range [0,50].

### Solution 1
`DP[i][j][k]` stands for how many possible ways to walk into cell `j,k` in
step `i`, `DP[i][j][k]` only depends on `DP[i - 1][j][k]`, so we can compress
3 dimensional dp array to 2 dimensional.

    
    
    public class Solution {
        public int findPaths(int m, int n, int N, int i, int j) {
            if (N <= 0) return 0;
            
            final int MOD = 1000000007;
            int[][] count = new int[m][n];
            count[i][j] = 1;
            int result = 0;
            
            int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
            
            for (int step = 0; step < N; step++) {
                int[][] temp = new int[m][n];
                for (int r = 0; r < m; r++) {
                    for (int c = 0; c < n; c++) {
                        for (int[] d : dirs) {
                            int nr = r + d[0];
                            int nc = c + d[1];
                            if (nr < 0 || nr >= m || nc < 0 || nc >= n) {
                                result = (result + count[r][c]) % MOD;
                            }
                            else {
                                temp[nr][nc] = (temp[nr][nc] + count[r][c]) % MOD;
                            }
                        }
                    }
                }
                count = temp;
            }
            
            return result;
        }
    }
    


### Solution 2
The number of paths for N moves is the sum of paths for N - 1 moves from the
adjacent cells. If an adjacent cell is out of the border, the number of paths
is 1.

    
    
    int findPaths(int m, int n, int N, int i, int j) {
      uint dp[51][50][50] = {};
      for (auto Ni = 1; Ni <= N; ++Ni)
        for (auto mi = 0; mi < m; ++mi)
          for (auto ni = 0; ni < n; ++ni)
            dp[Ni][mi][ni] = ((mi == 0 ? 1 : dp[Ni - 1][mi - 1][ni]) + (mi == m - 1? 1 : dp[Ni - 1][mi + 1][ni])
                + (ni == 0 ? 1 : dp[Ni - 1][mi][ni - 1]) + (ni == n - 1 ? 1 : dp[Ni - 1][mi][ni + 1])) % 1000000007;
      return dp[N][i][j];
    }
    

We can also reduce the memory usage by using two grids instead of N, as we
only need to look one step back. We can use N % 2 and (N + 1) % 2 to alternate
grids so we do not have to copy.

    
    
    int findPaths(int m, int n, int N, int i, int j) {
        unsigned int g[2][50][50] = {};
        while (N-- > 0)
            for (auto k = 0; k < m; ++k)
                for (auto l = 0, nc = (N + 1) % 2, np = N % 2; l < n; ++l)
                    g[nc][k][l] = ((k == 0 ? 1 : g[np][k - 1][l]) + (k == m - 1 ? 1 : g[np][k + 1][l])
                        + (l == 0 ? 1 : g[np][k][l - 1]) + (l == n - 1 ? 1 : g[np][k][l + 1])) % 1000000007;
        return g[1][i][j];
    }
    

As suggested by @mingthor, we can further decrease the memory usage (2 * m * n
>> m * (n + 1)) as we only looking one row up. We will store new values for
the current row in an array, and write these values back to the matrix as we
process cells in the next row. This approach, however, impacts the runtime as
we need extra copying for each step.

I experimented with different n and m (50 - 500), and N (5,000 - 50,000), and
the second solution is approximately 10% faster than this one.

    
    
    int findPaths(int m, int n, int N, int i, int j) {
        unsigned int g[50][50] = {}, r[50];
        while (N-- > 0)
            for (auto k = 0; k <= m; ++k)
                for (auto l = 0; l < n; ++l) {
                    auto tmp = r[l];
                    r[l] = (k == m ? 0 : ((k == 0 ? 1 : g[k - 1][l]) + (k == m - 1 ? 1 : g[k + 1][l])
                        + (l == 0 ? 1 : g[k][l - 1]) + (l == n - 1 ? 1 : g[k][l + 1])) % 1000000007);
                    if (k > 0) g[k - 1][l] = tmp;
                }
        return g[i][j];
    }
    


### Solution 3
    
    
    class Solution {
        private int[][] dirs = {{-1, 0}, {1, 0}, {0, 1}, {0, -1}};
        private int mod = 1000000000 + 7;
        
        public int findPaths(int m, int n, int N, int i, int j) {
            // m * n grid
            long[][][] memo = new long[m][n][N+1];
            for (int ii = 0; ii < m; ii++) {
                for (int jj = 0; jj < n; jj++) {
                    for (int kk = 0; kk < N+1; kk++) {
                        memo[ii][jj][kk] = -1;
                    }
                }
            }
            return (int) (dfs(m, n, N, i, j, memo) % mod);
        }
        
        public long dfs(int m, int n, int N, int i, int j, long[][][] memo) {
            //check if out of boundary, if out could not move back
            if (i < 0 || i >= m || j < 0 || j >= n) {
                return 1;
            }
            if (N == 0) return 0;
            if (memo[i][j][N] != -1) return memo[i][j][N];
            memo[i][j][N] = 0;
            for (int dir[] : dirs) {
                int x = dir[0] + i;
                int y = dir[1] + j;
                memo[i][j][N] = (memo[i][j][N] + dfs(m, n, N - 1, x, y, memo) % mod) % mod;
            }
            return memo[i][j][N];
            
        }
    }
    

Java DFS with memorization to store the result. I think both the time and
space complexity is O(mnN)



