---
layout: post
title: 1121. Partition Array for Maximum Sum
---
### Question
Given an integer array `A`, you partition the array into (contiguous)
subarrays of length at most `K`.  After partitioning, each subarray has their
values changed to become the maximum value of that subarray.

Return the largest sum of the given array after partitioning.



 **Example 1:**

    
    
     **Input:** A = [1,15,7,9,2,5,10], K = 3
    **Output:** 84
    **Explanation** : A becomes [15,15,15,9,10,10,10]



 **Note:**

  1. `1 <= K <= A.length <= 500`
  2. `0 <= A[i] <= 10^6`

### Solution 1
##  **Explanation**

`dp[i]` record the maximum sum we can get considering `A[0] ~ A[i]`  
To get `dp[i]`, we will try to change `k` last numbers separately to the
maximum of them,  
where `k = 1` to `k = K`.

##  **Complexity**

Time `O(NK)`, Space `O(N)`

  

 **Java:**

    
    
         public int maxSumAfterPartitioning(int[] A, int K) {
            int N = A.length, dp[] = new int[N];
            for (int i = 0; i < N; ++i) {
                int curMax = 0;
                for (int k = 1; k <= K && i - k + 1 >= 0; ++k) {
                    curMax = Math.max(curMax, A[i - k + 1]);
                    dp[i] = Math.max(dp[i], (i >= k ? dp[i - k] : 0) + curMax * k);
                }
            }
            return dp[N - 1];
        }
    

**C++:**

    
    
         int maxSumAfterPartitioning(vector<int>& A, int K) {
            int N = A.size();
            vector<int> dp(N);
            for (int i = 0; i < N; ++i) {
                int curMax = 0;
                for (int k = 1; k <= K && i - k + 1 >= 0; ++k) {
                    curMax = max(curMax, A[i - k + 1]);
                    dp[i] = max(dp[i], (i >= k ? dp[i - k] : 0) + curMax * k);
                }
            }
            return dp[N - 1];
        }
    

**Python:**

    
    
        def maxSumAfterPartitioning(self, A, K):
            N = len(A)
            dp = [ 0] * (N + 1)
            for i in xrange(N):
                curMax = 0
                for k in xrange(1, min(K, i + 1) + 1):
                    curMax = max(curMax, A[i - k + 1])
                    dp[i] = max(dp[i], dp[i - k] + curMax * k)
            return dp[N - 1]
    


### Solution 2
Code is same as others, but I found a way to visualize the pattern. Alfter
all, the goal is to derive the rule.

    
    
    class Solution {
        // Let k be 2
        // Focus on "growth" of the pattern
        // Define A' to be a partition over A that gives max sum
        
        // #0
        // A = {1}
        // A'= {1} => 1
        
        // #1
        // A = {1, 2}
        // A'= {1}{2} => 1 + 2 => 3 X
        // A'= {1, 2} => {2, 2} => 4 AC
            
        // #2
        // A = {1, 2, 9}
        // A'= {1, 2}{9} => {2, 2}{9} => 4 + 9 => 13 X
        // A'= {1}{2, 9} => {1}{9, 9} => 1 + 18 => 19 AC
        
        // #3
        // A = {1, 2, 9, 30}
        // A'= {1}{2, 9}{30} => {1}{9, 9}{30} => 19 + 30 => 49 X
        // A'= {1, 2}{9, 30} => {2, 2}{30, 30} => 4 + 60 => 64 AC
        
        // Now, label each instance. Use F1() to represent how A is partitioned and use F2() to represent
        // the AC value of that partition. F2() is the dp relation we are looking for.
        
        // #4
        // A = {1, 2, 9, 30, 5}
        // A'= F1(#3){5} => F2(#3) + 5 => 69 X
        // A'= F1(#2){30, 5} => F2(#2) + 30 + 30 => 79 AC
        // => F2(#4) = 79
        
        public int maxSumAfterPartitioning(int[] A, int K) {
            int N = A.length, dp[] = new int[N];
            for (int i = 0; i < N; ++i) {
                int curMax = 0;
                for (int k = 1; k <= K && i - k + 1 >= 0; ++k) {
                    curMax = Math.max(curMax, A[i - k + 1]);
                    dp[i] = Math.max(dp[i], (i >= k ? dp[i - k] : 0) + curMax * k);
                }
            }
            return dp[N - 1];
        }
    }
    


### Solution 3
The dynamic programming solution works here because the problem has an optimal
substructure and overlapping subproblems as in the following example:

Let `A = [9, 10, 2, 5]` and `K = 3`

Let `S[n1, n2, ..., ni]` be the solution to subarray `[n1, n2, ..., ni]`.  
The following are base cases to initialize the memo array:

    
    
    S[9] = 9 (i.e., memo[0] = 9)
    S[9, 10] = 20 (i.e., memo[1] = 20)
    S[9, 10, 2] = 30 (i.e., memo[2] = 30)
    

Here we do the real work, where you need to "loop" through a K-sized window
before the new value to be considered, including the new value, which in this
case the new value is 5:

    
    
    S[9, 10, 2, 5] = max(S[9] + S[10, 2, 5], S[9, 10] + S[2, 5], S[9, 10, 2] + S[5]) = 39
    

The window we "looped" through above is [10, 2, 5].

From the formula above, we see that the overlapping subproblem is in using the
solutions from previous solutions stored in the memo, e.g., `S[9]`, `S[9,
10]`, and `S[9, 10, 2]`. The optimal substructure comes from the fact that the
solution to `S[9, 10, 2, 5]` is solved by using solutions to previously
calculated solutions.

    
    
        public int maxSumAfterPartitioning(int[] A, int K) {
            int[] memo = new int[A.length];
            memo[0] = A[0];
            int initMax = A[0];
    
            for(int i = 1; i < K; ++i) {
                if (A[i] > initMax) {
                    initMax = A[i];
                }
                memo[i] = (i+1) * initMax;
            }
    
            for (int i = K; i < A.length; ++i) {
                int cur = 0;
                int kIntervalMax = A[i];
    			// Backtrack up to K-1 indices to calculate current maximum for memo[i].
                for (int j = 1; j <= K; ++j) {
    			    // Keep track of the current maximum in the window [i-j+1, i].
                    if (A[i-j+1] > kIntervalMax) {
                        kIntervalMax = A[i-j+1];
                    }
    				// cur is the candidate for the solution to memo[i] as we backtrack the K-1 window.
                    cur = memo[i-j] + j * kIntervalMax;
    
                    if (cur > memo[i]) {
                        memo[i] = cur;
                    }
                }
            }
            return memo[A.length-1];
        }
    



