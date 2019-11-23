---
layout: post
title: 911. Profitable Schemes
---
### Question
There are G people in a gang, and a list of various crimes they could commit.

The `i`-th crime generates a `profit[i]` and requires `group[i]` gang members
to participate.

If a gang member participates in one crime, that member can't participate in
another crime.

Let's call a _profitable  scheme_ any subset of these crimes that generates at
least `P` profit, and the total number of gang members participating in that
subset of crimes is at most G.

How many schemes can be chosen?  Since the answer may be very large, **return
it modulo** `10^9 + 7`.



 **Example 1:**

    
    
     **Input:** G = 5, P = 3, group = [2,2], profit = [2,3]
    **Output:** 2
    **Explanation:**
    To make a profit of at least 3, the gang could either commit crimes 0 and 1, or just crime 1.
    In total, there are 2 schemes.
    

**Example 2:**

    
    
    **Input:** G = 10, P = 5, group = [2,3,5], profit = [6,7,8]
    **Output:** 7
    **Explanation:**
    To make a profit of at least 5, the gang could commit any crimes, as long as they commit one.
    There are 7 possible schemes: (0), (1), (2), (0,1), (0,2), (1,2), and (0,1,2).
    



 **Note:**

  1. `1 <= G <= 100`
  2. `0 <= P <= 100`
  3. `1 <= group[i] <= 100`
  4. `0 <= profit[i] <= 100`
  5. `1 <= group.length = profit.length <= 100`

### Solution 1
Well, it is a Knapsack problem and my first intuition is dp.

`dp[i][j]` means the count of schemes with `i` profit and `j` members.

The dp equation is simple here:  
`dp[i + p][j + g] += dp[i][j])` if `i + p < P`  
`dp[P][j + g] += dp[i][j])` if `i + p >= P`

Don't forget to take care of overflow.

For each pair `(p, g)` of `(profit, group)`, I update the count in `dp`.

 **Time Complexity** :  
O(NPG)

 **C++:**

    
    
         int profitableSchemes(int G, int P, vector<int> group, vector<int> profit) {
            vector<vector<int>> dp(P + 1, vector<int>(G + 1, 0));
            dp[0][0] = 1;
            int res = 0, mod = 1e9 + 7;
            for (int k = 0; k < group.size(); k++) {
                int g = group[k], p = profit[k];
                for (int i = P; i >= 0; i--)
                    for (int j = G - g; j >= 0; j--)
                        dp[min(i + p, P)][j + g] = (dp[min(i + p, P)][j + g] + dp[i][j]) % mod;
            }
            for (int x: dp[P]) res = (res + x) % mod;
            return res;
        }
    

**Java:**

    
    
         public int profitableSchemes(int G, int P, int[] group, int[] profit) {
            int[][] dp = new int[P + 1][G + 1];
            dp[0][0] = 1;
            int res = 0, mod = (int)1e9 + 7;
            for (int k = 0; k < group.length; k++) {
                int g = group[k], p = profit[k];
                for (int i = P; i >= 0; i--)
                    for (int j = G - g; j >= 0; j--)
                        dp[Math.min(i + p, P)][j + g] = (dp[Math.min(i + p, P)][j + g] + dp[i][j]) % mod;
            }
            for (int x : dp[P]) res = (res + x) % mod;
            return res;
        }
    

**Python:**

    
    
        def profitableSchemes(self, G, P, group, profit):
            dp = [[ 0] * (G + 1) for i in range(P + 1)]
            dp[0][0] = 1
            for p, g in zip(profit, group):
                for i in range(P, -1, -1):
                    for j in range(G - g, -1, -1):
                        dp[min(i + p, P)][j + g] += dp[i][j]
            return sum(dp[P]) % (10**9 + 7)
    


### Solution 2
dp[k][i][j] means for first k crime with i members and `at least` j profit,
what is total schemes can be chosen.  
And we need this `Math.max(0, j - p)`, because this is for at least j profit.

`dp[k][i][j] = dp[k - 1][i][j] + dp[k - 1][i - current group][Math.max(0, j -
current profit)]`

This is 3d original solution:

    
    
    class Solution {
        private int mod = (int)1e9 + 7;
        public int profitableSchemes(int G, int P, int[] group, int[] profit) {
            int[][][] dp = new int[group.length + 1][G + 1][P + 1];
            dp[0][0][0] = 1;
            for (int k = 1; k <= group.length; k++) {
                int g = group[k - 1];
                int p = profit[k - 1];
                for (int i = 0; i <= G; i++) {
                    for (int j = 0; j <= P; j++) {
                        dp[k][i][j] = dp[k - 1][i][j];
                        if (i >= g) {
                            dp[k][i][j] = (dp[k][i][j] + dp[k - 1][i - g][Math.max(0, j - p)])%mod;
                        }
                    }
                }
            }
            int sum = 0;                                                       
            for(int i = 0; i <= G; i++){
                sum = (sum + dp[group.length][i][P])%mod;
            }
            return sum;
        }
    }
    

Because all k dimension only depends on k - 1 dimension, so we can improve it
to 2D array which only has i and j dimension.  
Just be careful about that i and j should be decrease, in order to get correct
old k - 1 dimension value.

    
    
    class Solution {
        private int mod = (int)1e9 + 7;
        public int profitableSchemes(int G, int P, int[] group, int[] profit) {
            int[][] dp = new int[G + 1][P + 1];
            dp[0][0] = 1;
            for (int k = 1; k <= group.length; k++) {
                int g = group[k - 1];
                int p = profit[k - 1];
                for (int i = G; i >= g; i--) {
                    for (int j = P; j >= 0; j--) {
                        dp[i][j] = (dp[i][j] + dp[i - g][Math.max(0, j - p)])%mod;
                    }
                }
            }
            int sum = 0;                                                       
            for(int i = 0; i <= G; i++){
                sum = (sum + dp[i][P])%mod;
            }
            return sum;
        }
    }
    


### Solution 3
<https://www.youtube.com/watch?v=LKcTXCIh668>

I wasted 5+ mins b/c some unknown LeetCode bug in test case 2, problem 3. You
can find it from 12:00 to 17:00. Argueable if I'm more experienced, this bug
(not mine) should cost me less time.



