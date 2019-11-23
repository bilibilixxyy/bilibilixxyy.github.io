---
layout: post
title: 967. Minimum Falling Path Sum
---
### Question
Given a **square** array of integers `A`, we want the **minimum** sum of a
_falling path_ through `A`.

A falling path starts at any element in the first row, and chooses one element
from each row.  The next row's choice must be in a column that is different
from the previous row's column by at most one.



 **Example 1:**

    
    
     **Input:** [[1,2,3],[4,5,6],[7,8,9]]
    **Output:** 12
    **Explanation:**
    The possible falling paths are:
    

  * `[1,4,7], [1,4,8], [1,5,7], [1,5,8], [1,5,9]`
  * `[2,4,7], [2,4,8], [2,5,7], [2,5,8], [2,5,9], [2,6,8], [2,6,9]`
  * `[3,5,7], [3,5,8], [3,5,9], [3,6,8], [3,6,9]`

The falling path with the smallest sum is `[1,4,7]`, so the answer is `12`.



 **Note:**

  1. `1 <= A.length == A[0].length <= 100`
  2. `-100 <= A[i][j] <= 100`

### Solution 1
The minimum path to get to element `A[i][j]` is the minimum of `A[i - 1][j -
1]`, `A[i - 1][j]` and `A[i - 1][j + 1]`.  
Starting from row 1, we add the minumum path to each element. The smallest
number in the last row is the miminum path sum.  
Example:  
[1, 2, 3]  
[4, 5, 6] => [5, 6, 8]  
[7, 8, 9] => [7, 8, 9] => [12, 13, 15]

    
    
    int minFallingPathSum(vector<vector<int>>& A) {
      for (auto i = 1; i < A.size(); ++i)
        for (auto j = 0; j < A.size(); ++j)
          A[i][j] += min({ A[i-1][j], A[i-1][max(0,j-1)], A[i-1][min((int)A.size()-1,j+1)] });
      return *min_element(begin(A[A.size() - 1]), end(A[A.size() - 1]));
    }
    

Java version:

    
    
    public int minFallingPathSum(int[][] A) {
      for (int i = 1; i < A.length; ++i)
        for (int j = 0; j < A.length; ++j)
          A[i][j] += Math.min(A[i - 1][j], Math.min(A[i - 1][Math.max(0, j - 1)], A[i - 1][Math.min(A.length - 1, j + 1)]));
      return Arrays.stream(A[A.length - 1]).min().getAsInt();
    }        
    


### Solution 2
The solution is definitely not optimal, nor is the code as "elegant", but the
idea is straght forward, hope it helps :)

Given matrix like the left part in the graph below, we first initialize a 2D
DP matrix, and then iterate the original matrix row by row. For each element
in DP matrix, we sum up the **corresponding element from original matrix**
with the **minimum neighbors from previous row in DP matrix**.  
In order to save the future hassles dealing with index boundries while
iterating the matrix, I added the **two extra columns** and assigned them as
`Integer.MAX_VALUE`.

The idea is illustrated as following:  
![image](https://assets.leetcode.com/users/yfgu0618/image_1540698728.png)

Following is the code:

    
    
    public static int minFallingPathSum(int[][] A) {
      int rows = A.length;
      int cols = A[0].length;
      // DP matrix has 2 extra columns
      int[][] dp = new int[rows][cols + 2];
      // Fill the first row of DP matrix
      for (int i = 1; i <= cols; i++) {
        dp[0][i] = A[0][i - 1];
      }
      // Fill Integer.MAX_VALUE into first and last column of DP matrix
      for (int i = 0; i < rows; i++) {
        dp[i][0] = Integer.MAX_VALUE;
        dp[i][cols + 1] = Integer.MAX_VALUE;
      }
      // Building the DP matrix
      for (int i = 1; i < rows; i++) {
        for (int j = 1; j <= cols; j++) {
          // Find the minimum neighbor from previous row in DP matrix
          int minNeighbor = Math.min(dp[i - 1][j - 1], dp[i - 1][j]);
          minNeighbor = Math.min(minNeighbor, dp[i - 1][j + 1]);
          dp[i][j] = A[i][j - 1] + minNeighbor;
        }
      }
      // The minimum path sum is minimum of the last row in DP matrix
      int min = Integer.MAX_VALUE;
      for (int i = 1; i <= cols; i++) {
        min = Math.min(min, dp[rows - 1][i]);
      }
      return min;
    }
    


### Solution 3
    
    
    def minFallingPathSum(self, A):
        """
        :type A: List[List[int]]
        :rtype: int
        """
        dp = A[0]
        for row in A[1:]:
            dp = [value + min([dp[c], dp[max(c - 1, 0)], dp[min(len(A) - 1, c + 1)]]) for c, value in enumerate(row)]
        return min(dp)



