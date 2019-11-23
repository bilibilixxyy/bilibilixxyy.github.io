---
layout: post
title: 221. Maximal Square
---
### Question
Given a 2D binary matrix filled with 0's and 1's, find the largest square
containing only 1's and return its area.

 **Example:**

    
    
     **Input:**
    1 0 1 0 0
    1 0  1 1 1
    1 1 1 1 1
    1 0 0 1 0
    
    **Output:** 4
    

### Solution 1
To appy DP, we define the state as the maximal **size** (square = size * size)
of the square that can be formed till point `(i, j)`, denoted as `dp[i][j]`.

For the topmost row (`i = 0`) and the leftmost column (`j = 0`), we have
`dp[i][j] = matrix[i][j] - '0'`, meaning that it can at most form a square of
size 1 when the matrix has a `'1'` in that cell.

When `i > 0` and `j > 0`, if `matrix[i][j] = '0'`, then `dp[i][j] = 0` since
no square will be able to contain the `'0'` at that cell. If `matrix[i][j] =
'1'`, we will have `dp[i][j] = min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1]) + 1`,
which means that the square will be limited by its left, upper and upper-left
neighbors.

    
    
    class Solution {
    public:
        int maximalSquare(vector<vector<char>>& matrix) {
            if (matrix.empty()) {
                return 0;
            }
            int m = matrix.size(), n = matrix[0].size(), sz = 0;
            vector<vector<int>> dp(m, vector<int>(n, 0));
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    if (!i || !j || matrix[i][j] == '0') {
                        dp[i][j] = matrix[i][j] - '0';
                    } else {
                        dp[i][j] = min(dp[i - 1][j - 1], min(dp[i - 1][j], dp[i][j - 1])) + 1;
                    }
                    sz = max(dp[i][j], sz);
                }
            }
            return sz * sz;
        }
    };
    

In the above code, it uses `O(mn)` space. Actually each time when we update
`dp[i][j]`, we only need `dp[i-1][j-1]`, `dp[i-1][j]` (the previous row) and
`dp[i][j-1]` (the current row). So we may just keep two rows.

    
    
    class Solution {
    public:
        int maximalSquare(vector<vector<char>>& matrix) {
            if (matrix.empty()) {
                return 0;
            }
            int m = matrix.size(), n = matrix[0].size(), sz = 0;
            vector<int> pre(n, 0), cur(n, 0);
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    if (!i || !j || matrix[i][j] == '0') {
                        cur[j] = matrix[i][j] - '0';
                    } else {
                        cur[j] = min(pre[j - 1], min(pre[j], cur[j - 1])) + 1;
                    }
                    sz = max(cur[j], sz);
                }
                fill(pre.begin(), pre.end(), 0);
                swap(pre, cur);
            }
            return sz * sz;
        }
    };
    

Furthermore, we may only use just one `vector` (thanks to @stellari for
sharing the idea).

    
    
    class Solution {
    public:
        int maximalSquare(vector<vector<char>>& matrix) {
            if (matrix.empty()) {
                return 0;
            }
            int m = matrix.size(), n = matrix[0].size(), sz = 0, pre;
            vector<int> cur(n, 0);
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    int temp = cur[j];
                    if (!i || !j || matrix[i][j] == '0') {
                        cur[j] = matrix[i][j] - '0';
                    } else {
                        cur[j] = min(pre, min(cur[j], cur[j - 1])) + 1;
                    }
                    sz = max(cur[j], sz);
                    pre = temp;
                }
            }
            return sz * sz;
        }
    };
    


### Solution 2
    
    
    public int maximalSquare(char[][] a) {
        if(a.length == 0) return 0;
        int m = a.length, n = a[0].length, result = 0;
        int[][] b = new int[m+1][n+1];
        for (int i = 1 ; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if(a[i-1][j-1] == '1') {
                    b[i][j] = Math.min(Math.min(b[i][j-1] , b[i-1][j-1]), b[i-1][j]) + 1;
                    result = Math.max(b[i][j], result); // update result
                }
            }
        }
        return result*result;
    }


### Solution 3
    
    
    public int maximalSquare(char[][] a) {
      if (a == null || a.length == 0 || a[0].length == 0)
        return 0;
          
      int max = 0, n = a.length, m = a[0].length;
      
      // dp(i, j) represents the length of the square 
      // whose lower-right corner is located at (i, j)
      // dp(i, j) = min{ dp(i-1, j-1), dp(i-1, j), dp(i, j-1) }
      int[][] dp = new int[n + 1][m + 1];
      
      for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
          if (a[i - 1][j - 1] == '1') {
            dp[i][j] = Math.min(dp[i - 1][j - 1], Math.min(dp[i - 1][j], dp[i][j - 1])) + 1;
            max = Math.max(max, dp[i][j]);
          }
        }
      }
      
      // return the area
      return max * max;
    }



