---
layout: post
title: 718. Maximum Length of Repeated Subarray
---
### Question
Given two integer arrays `A` and `B`, return the maximum length of an subarray
that appears in both arrays.

 **Example 1:**

    
    
     **Input:**
    A: [1,2,3,2,1]
    B: [3,2,1,4,7]
    **Output:** 3
    **Explanation:** 
    The repeated subarray with maximum length is [3, 2, 1].
    



 **Note:**

  1. 1 <= len(A), len(B) <= 1000
  2. 0 <= A[i], B[i] < 100

### Solution 1
The code explains itself:

    
    
    class Solution {
        public int findLength(int[] A, int[] B) {
            if(A == null||B == null) return 0;
            int m = A.length;
            int n = B.length;
            int max = 0;
            //dp[i][j] is the length of longest common subarray ending with nums[i] and nums[j]
            int[][] dp = new int[m + 1][n + 1];
            for(int i = 0;i <= m;i++){
                for(int j = 0;j <= n;j++){
                    if(i == 0 || j == 0){
                        dp[i][j] = 0;
                    }
                    else{
                        if(A[i - 1] == B[j - 1]){
                            dp[i][j] = 1 + dp[i - 1][j - 1];
                            max = Math.max(max,dp[i][j]);
                        }
                    }
                }
            }
            return max;
        }
    }
    

Hope it helps!


### Solution 2
 **DP formula**

    
    
     /**
     * dp[i][j] = a[i] == b[j] ? 1 + dp[i + 1][j + 1] : 0;
     * dp[i][j] : max lenth of common subarray start at a[i] & b[j];
     */
    

**Java - DP matrix**

    
    
     class Solution {
        public int findLength(int[] a, int[] b) {
            int m = a.length, n = b.length;
            if (m == 0 || n == 0) return 0;
            int[][] dp = new int[m + 1][n + 1];
            int max = 0;
            for (int i = m - 1; i >= 0; i--)
                for (int j = n - 1; j >= 0; j--)
                    max = Math.max(max, dp[i][j] = a[i] == b[j] ? 1 + dp[i + 1][j + 1] : 0);
            return max;        
        }
    }
    

**Java - DP array**

    
    
     class Solution {
        public int findLength(int[] a, int[] b) {
            int m = a.length, n = b.length;
            if (m == 0 || n == 0) return 0;
            int[] dp = new int[n + 1];
            int max = 0;
            for (int i = m - 1; i >= 0; i--)
                for (int j = 0; j < n; j++)
                    max = Math.max(max, dp[j] = a[i] == b[j] ? 1 + dp[j + 1] : 0);
            return max;        
        }
    }
    

**C++ - DP array**

    
    
     class Solution {
    public:
        int findLength(vector<int>& a, vector<int>& b) {
            int m = a.size(), n = b.size();
            if (!m || !n) return 0;
            vector<int> dp(n + 1);
            int res = 0;
            for (int i = m - 1; i >= 0; i--) {
                for (int j = 0; j < n; j++) {
                    res = max(res, dp[j] = a[i] == b[j] ? 1 + dp[j + 1] : 0);
                }
            }
            return res;
        }
    };
    


### Solution 3
This is essentially the same question as longest common substring.

 _\- Yangshun_

    
    
    class Solution(object):
        def findLength(self, A, B):
            dp = [[ 0 for _ in range(len(B) + 1)] for _ in range(len(A) + 1)]
            for i in range(1, len(A) + 1):
                for j in range(1, len(B) + 1):
                    if A[i - 1] == B[j - 1]:
                        dp[i][j] = dp[i - 1][j - 1] + 1
            return max(max(row) for row in dp)
    



