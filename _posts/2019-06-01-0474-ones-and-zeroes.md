---
layout: post
title: 474. Ones and Zeroes
---
### Question
In the computer world, use restricted resource you have to generate maximum
benefit is what we always want to pursue.

For now, suppose you are a dominator of **m** `0s` and **n** `1s`
respectively. On the other hand, there is an array with strings consisting of
only `0s` and `1s`.

Now your task is to find the maximum number of strings that you can form with
given **m** `0s` and **n** `1s`. Each `0` and `1` can be used at most
**once**.

 **Note:**

  1. The given numbers of `0s` and `1s` will both not exceed `100`
  2. The size of given string array won't exceed `600`.



 **Example 1:**

    
    
     **Input:** Array = {"10", "0001", "111001", "1", "0"}, m = 5, n = 3
    **Output:** 4
    
    **Explanation:** This are totally 4 strings can be formed by the using of 5 0s and 3 1s, which are “10,”0001”,”1”,”0”
    



**Example 2:**

    
    
    **Input:** Array = {"10", "0", "1"}, m = 1, n = 1
    **Output:** 2
    
    **Explanation:** You could form "10", but then you'd have nothing left. Better form "0" and "1".
    

### Solution 1
    
    
    int findMaxForm(vector<string>& strs, int m, int n) {
      vector<vector<int>> memo(m+1, vector<int>(n+1, 0));
      int numZeroes, numOnes;
    
      for (auto &s : strs) {
        numZeroes = numOnes = 0;
        // count number of zeroes and ones in current string
        for (auto c : s) {
          if (c == '0')
    	numZeroes++;
          else if (c == '1')
    	numOnes++;
        }
    
        // memo[i][j] = the max number of strings that can be formed with i 0's and j 1's
        // from the first few strings up to the current string s
        // Catch: have to go from bottom right to top left
        // Why? If a cell in the memo is updated(because s is selected),
        // we should be adding 1 to memo[i][j] from the previous iteration (when we were not considering s)
        // If we go from top left to bottom right, we would be using results from this iteration => overcounting
        for (int i = m; i >= numZeroes; i--) {
    	for (int j = n; j >= numOnes; j--) {
              memo[i][j] = max(memo[i][j], memo[i - numZeroes][j - numOnes] + 1);
    	}
        }
      }
      return memo[m][n];
    }
    


### Solution 2
This problem is a typical 0-1 knapsack problem, we need to pick several
strings in provided strings to get the maximum number of strings using limited
number 0 and 1. We can create a three dimensional array, in which dp[i][j][k]
means the maximum number of strings we can get from the first i argument strs
using limited j number of '0's and k number of '1's.

For dp[i][j][k], we can get it by fetching the current string i or discarding
the current string, which would result in dp[i][j][k] =
dp[i-1][j-numOfZero(strs[i])][i-numOfOnes(strs[i])] and dp[i][j][k] =
dp[i-1][j][k]; We only need to treat the larger one in it as the largest
number for dp[i][j][k].

talking is cheap:

    
    
    public int findMaxForm(String[] strs, int m, int n) {
        int l = strs.length;
        int[][][] dp = new int[l+1][m+1][n+1];
        
        for (int i = 0; i < l+1; i++) {
            int[] nums = new int[]{0,0};
            if (i > 0) {
                nums = calculate(strs[i-1]);
            }
            for (int j = 0; j < m+1; j++) {
                for (int k = 0; k < n+1; k++) {
                    if (i == 0) {
                        dp[i][j][k] = 0;
                    } else if (j>=nums[0] && k>=nums[1]) {
                        dp[i][j][k] = Math.max(dp[i-1][j][k], dp[i-1][j-nums[0]][k-nums[1]]+1);
                    } else {
                        dp[i][j][k] = dp[i-1][j][k];
                    }
                }
            }
        }
        
        return dp[l][m][n];
    }
    
    private int[] calculate(String str) {
        int[] res = new int[2];
        Arrays.fill(res, 0);
        
        for (char ch : str.toCharArray()) {
            if (ch == '0') {
                res[0]++;
            } else if (ch == '1') {
                res[1]++;
            }
        }
        
        return res;
    }
    

By the way, 0-1 knapsack we cannot decrease the time complexity, but we can
decrease the space complexity from i _j_ k to j*k

    
    
    public int findMaxForm(String[] strs, int m, int n) {
        int l = strs.length;
        int[][] dp = new int[m+1][n+1];
        
        for (int i = 0; i < m+1; i++) {
            Arrays.fill(dp[i], 0);
        }
        
        int[] nums = new int[]{0,0};
        for (String str : strs) {
            nums = calculate(str);
            for (int j = m; j >= nums[0]; j--) {
                for (int k = n; k >= nums[1]; k--) {
                    if (j>=nums[0] && k>=nums[1]) {
                        dp[j][k] = Math.max(dp[j][k], dp[j-nums[0]][k-nums[1]]+1);
                    } else {
                        dp[j][k] = dp[j][k];
                    }
                }
            }
        }
        
        return dp[m][n];
    }
    
    private int[] calculate(String str) {
        int[] res = new int[2];
        Arrays.fill(res, 0);
        
        for (char ch : str.toCharArray()) {
            if (ch == '0') {
                res[0]++;
            } else if (ch == '1') {
                res[1]++;
            }
        }
        
        return res;
    }
    


### Solution 3
Based on the description...



