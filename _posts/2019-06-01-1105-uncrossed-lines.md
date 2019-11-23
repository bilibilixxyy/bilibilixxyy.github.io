---
layout: post
title: 1105. Uncrossed Lines
---
### Question
We write the integers of `A` and `B` (in the order they are given) on two
separate horizontal lines.

Now, we may draw _connecting lines_ : a straight line connecting two numbers
`A[i]` and `B[j]` such that:

  * `A[i] == B[j]`;
  * The line we draw does not intersect any other connecting (non-horizontal) line.

Note that a connecting lines cannot intersect even at the endpoints: each
number can only belong to one connecting line.

Return the maximum number of connecting lines we can draw in this way.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2019/04/26/142.png)

    
    
     **Input:** A = [1,4,2], B = [1,2,4]
    **Output:** 2
    **Explanation:** We can draw 2 uncrossed lines as in the diagram.
    We cannot draw 3 uncrossed lines, because the line from A[1]=4 to B[2]=4 will intersect the line from A[2]=2 to B[1]=2.
    

**Example 2:**

    
    
    **Input:** A = [2,5,1,2,5], B = [10,5,2,1,5,2]
    **Output:** 3
    

**Example 3:**

    
    
    **Input:** A = [1,3,7,1,7,5], B = [1,9,2,5,1]
    **Output:** 2



 **Note:**

  1. `1 <= A.length <= 500`
  2. `1 <= B.length <= 500`
  3. ` 1 <= A[i], B[i] <= 2000`

### Solution 1
## Solution 1: Straight Forward 2D DP

Time `O(N^2)`, Space `O(N^2)`

 **Java:**

    
    
        public int maxUncrossedLines(int[] A, int[] B) {
            int m = A. length, n = B.length, dp[][] = new int[m + 1][n + 1];
            for (int i = 1; i <= m; ++i)
                for (int j = 1; j <= n; ++j)
                    if (A[i - 1] == B[j - 1])
                        dp[i][j] = 1 + dp[i - 1][j - 1];
                    else
                        dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
            return dp[m][n];
        }
    

**C++:**

    
    
        int maxUncrossedLines(vector<int>& A, vector<int>& B) {
            int m = A. size(), n = B.size(), dp[m+1][n+1];
            memset(dp, 0, sizeof(dp));
            for (int i = 1; i <= m; ++i)
                for (int j = 1; j <= n; ++j)
                    dp[i][j] = A[i - 1] == B[j - 1] ? dp[i - 1][j - 1] + 1 : max(dp[i][j - 1], dp[i - 1][j]);
            return dp[m][n];
        }
    

**Python:**

    
    
        def maxUncrossedLines(self, A, B):
            dp, m, n = collections.defaultdict(int), len(A), len(B)
             for i in xrange(m):
                for j in xrange(n):
                    dp[i, j] = max(dp[i - 1, j - 1] + (A[i] == B[j]), dp[i - 1, j], dp[i, j - 1])
            return dp[m - 1, n - 1]
    

## Solution 2: 1D DP

Time `O(N^2)`, Space `O(N)`  
**Python:**

    
    
        def maxUncrossedLines(self, A, B):
             m, n = len(A), len(B)
            dp = [0] * (n + 1)
            for i in xrange(m):
                for j in range(n)[::-1]:
                    if A[i] == B[j]: dp[j + 1] = dp[j] + 1
                for j in range(n):
                    dp[j + 1] = max(dp[j + 1], dp[j])
            return dp[n]
    


### Solution 2
For instance, shouldn't input like : `[1, 2, 4], [1, 4, 4]` give an output of
3 instead of 2? It's not really a line cross if they just meet at the tip. Was
trying to come up with a solution with this in mind and now only noticed that
I was wrong all the while lol.


### Solution 3
 **Idea**  
if `A[i]` and `B[j]` are same, increment `count` and advance `i` and `j` and
work on the remaning arrays.  
Otherwise, we advance `i` and check the count on the remaining arrays, in
another case we advance `j` and check, choose the max from the two sub-
problems.

 **Solution1 (TLE)**

    
    
     public int maxUncrossedLines(int[] A, int[] B) {
            return helper(A, 0, B, 0);
        }
        private int helper(int[] A, int i, int[] B, int j){
            if(i == A.length || j == B.length) return 0;
            int count = 0;
            if(A[i] == B[j]) 
                count = 1+helper(A, i+1, B, j+1);
            else
            	count += Math.max(helper(A, i+1, B, j), helper(A, i, B, j+1));
            return count;
        }
    

**Solution2** same idea w/ memo

    
    
    class Solution {
        Integer[][] dp = null;
        public int maxUncrossedLines(int[] A, int[] B) {
            dp = new Integer[A. length][B.length];
            return helper(A, 0, B, 0);
        }
        private int helper(int[] A, int i, int[] B, int j){
            if(i == A.length || j == B.length) return 0;
            if(dp[i][j] != null) return dp[i][j];
            if(A[i] == B[j]) 
                dp[i][j] = 1+helper(A, i+1, B, j+1);
            else
            	dp[i][j] = Math.max(helper(A, i+1, B, j), helper(A, i, B, j+1));
            return dp[i][j];
        }
    }
    



