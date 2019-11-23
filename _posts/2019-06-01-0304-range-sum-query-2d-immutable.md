---
layout: post
title: 304. Range Sum Query 2D - Immutable
---
### Question
Given a 2D matrix _matrix_ , find the sum of the elements inside the rectangle
defined by its upper left corner ( _row_ 1, _col_ 1) and lower right corner (
_row_ 2, _col_ 2).

![Range Sum Query 2D](/static/images/courses/range_sum_query_2d.png)  
The above rectangle (with the red border) is defined by (row1, col1) = **(2,
1)** and (row2, col2) = **(4, 3)** , which contains sum = **8**.

 **Example:**  

    
    
    Given matrix = [
      [3, 0, 1, 4, 2],
      [5, 6, 3, 2, 1],
      [1, 2, 0, 1, 5],
      [4, 1, 0, 1, 7],
      [1, 0, 3, 0, 5]
    ]
    
    sumRegion(2, 1, 4, 3) -> 8
    sumRegion(1, 1, 2, 2) -> 11
    sumRegion(1, 2, 2, 4) -> 12
    

**Note:**  

  1. You may assume that the matrix does not change.
  2. There are many calls to _sumRegion_ function.
  3. You may assume that _row_ 1 ≤ _row_ 2 and _col_ 1 ≤ _col_ 2.

### Solution 1
Construct a 2D array `sums[row+1][col+1]`

( **notice** : we add additional blank row `sums[0][col+1]={0}` and blank
column `sums[row+1][0]={0}` to remove the edge case checking), so, we can have
the following definition

`sums[i+1][j+1]` represents the sum of area from `matrix[0][0]` to
`matrix[i][j]`

To calculate sums, the ideas as below

    
    
    +-----+-+-------+     +--------+-----+     +-----+---------+     +-----+--------+
    |     | |       |     |        |     |     |     |         |     |     |        |
    |     | |       |     |        |     |     |     |         |     |     |        |
    +-----+-+       |     +--------+     |     |     |         |     +-----+        |
    |     | |       |  =  |              |  +  |     |         |  -  |              |
    +-----+-+       |     |              |     +-----+         |     |              |
    |               |     |              |     |               |     |              |
    |               |     |              |     |               |     |              |
    +---------------+     +--------------+     +---------------+     +--------------+
    
       sums[i][j]      =    sums[i-1][j]    +     sums[i][j-1]    -   sums[i-1][j-1]   +  
    
                            matrix[i-1][j-1]
    

So, we use the same idea to find the specific area's sum.

    
    
    +---------------+   +--------------+   +---------------+   +--------------+   +--------------+
    |               |   |         |    |   |   |           |   |         |    |   |   |          |
    |   (r1,c1)     |   |         |    |   |   |           |   |         |    |   |   |          |
    |   +------+    |   |         |    |   |   |           |   +---------+    |   +---+          |
    |   |      |    | = |         |    | - |   |           | - |      (r1,c2) | + |   (r1,c1)    |
    |   |      |    |   |         |    |   |   |           |   |              |   |              |
    |   +------+    |   +---------+    |   +---+           |   |              |   |              |
    |        (r2,c2)|   |       (r2,c2)|   |   (r2,c1)     |   |              |   |              |
    +---------------+   +--------------+   +---------------+   +--------------+   +--------------+
    

And we can have the following code

    
    
    class NumMatrix {
    private:
        int row, col;
        vector<vector<int>> sums;
    public:
        NumMatrix(vector<vector<int>> &matrix) {
            row = matrix.size();
            col = row>0 ? matrix[0].size() : 0;
            sums = vector<vector<int>>(row+1, vector<int>(col+1, 0));
            for(int i=1; i<=row; i++) {
                for(int j=1; j<=col; j++) {
                    sums[i][j] = matrix[i-1][j-1] + 
                                 sums[i-1][j] + sums[i][j-1] - sums[i-1][j-1] ;
                }
            }
        }
    
        int sumRegion(int row1, int col1, int row2, int col2) {
            return sums[row2+1][col2+1] - sums[row2+1][col1] - sums[row1][col2+1] + sums[row1][col1];
        }
    };


### Solution 2
    
    
    private int[][] dp;
    
    public NumMatrix(int[][] matrix) {
        if(   matrix           == null
           || matrix.length    == 0
           || matrix[0].length == 0   ){
            return;   
        }
        
        int m = matrix.length;
        int n = matrix[0].length;
        
        dp = new int[m + 1][n + 1];
        for(int i = 1; i <= m; i++){
            for(int j = 1; j <= n; j++){
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1] -dp[i - 1][j - 1] + matrix[i - 1][j - 1] ;
            }
        }
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        int iMin = Math.min(row1, row2);
        int iMax = Math.max(row1, row2);
        
        int jMin = Math.min(col1, col2);
        int jMax = Math.max(col1, col2);
        
        return dp[iMax + 1][jMax + 1] - dp[iMax + 1][jMin] - dp[iMin][jMax + 1] + dp[iMin][jMin];    
    }


### Solution 3
The idea is simple, just precompute sums for all matrices with (0, 0) as top
left corner and (i, j) as bottom right corner. There are O(n^2) of these
matrices, so we store them in a 2D table. In order to make code simpler, I add
an extra column and row, filled with 0.

    
    
    class NumMatrix(object):
          def __init__(self, matrix):
              if matrix is None or not matrix:
                  return
              n, m = len(matrix), len(matrix[0])
              self.sums = [ [0 for j in xrange(m+1)] for i in xrange(n+1) ]
              for i in xrange(1, n+1):
                  for j in xrange(1, m+1):
                      self.sums[i][j] = matrix[i-1][j-1] + self.sums[i][j-1] + self.sums[i-1][j] - self.sums[i-1][j-1]
        
    
          def sumRegion(self, row1, col1, row2, col2):
              row1, col1, row2, col2 = row1+1, col1+1, row2+1, col2+1
              return self.sums[row2][col2] - self.sums[row2][col1-1] - self.sums[row1-1][col2] + self.sums[row1-1][col1-1]



