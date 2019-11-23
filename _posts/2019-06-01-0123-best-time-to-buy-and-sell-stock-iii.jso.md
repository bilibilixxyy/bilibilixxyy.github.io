---
layout: post
title: 123. Best Time to Buy and Sell Stock III
---
### Question
Say you have an array for which the _i_ th element is the price of a given
stock on day _i_.

Design an algorithm to find the maximum profit. You may complete at most _two_
transactions.

 **Note:  **You may not engage in multiple transactions at the same time
(i.e., you must sell the stock before you buy again).

 **Example 1:**

    
    
     **Input:** [3,3,5,0,0,3,1,4]
    **Output:** 6
    **Explanation:** Buy on day 4 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3.
                  Then buy on day 7 (price = 1) and sell on day 8 (price = 4), profit = 4-1 = 3.

**Example 2:**

    
    
    **Input:** [1,2,3,4,5]
    **Output:** 4
    **Explanation:** Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
                  Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are
                 engaging multiple transactions at the same time. You must sell before buying again.
    

**Example 3:**

    
    
    **Input:** [7,6,4,3,1]
    **Output:** 0
    **Explanation:** In this case, no transaction is done, i.e. max profit = 0.

### Solution 1
The thinking is simple and is inspired by the best solution from Single Number
II (I read through the discussion after I use DP).  
Assume we only have 0 money at first;  
4 Variables to maintain some interested 'ceilings' so far:  
The maximum of if we've just buy 1st stock, if we've just sold 1nd stock, if
we've just buy 2nd stock, if we've just sold 2nd stock.  
Very simple code too and work well. I have to say the logic is simple than
those in Single Number II.

    
    
    public class Solution {
        public int maxProfit(int[] prices) {
            int hold1 = Integer.MIN_VALUE, hold2 = Integer.MIN_VALUE;
            int release1 = 0, release2 = 0;
            for(int i:prices){                              // Assume we only have 0 money at first
                release2 = Math.max(release2, hold2+i);     // The maximum if we've just sold 2nd stock so far.
                hold2    = Math.max(hold2,    release1-i);  // The maximum if we've just buy  2nd stock so far.
                release1 = Math.max(release1, hold1+i);     // The maximum if we've just sold 1nd stock so far.
                hold1    = Math.max(hold1,    -i);          // The maximum if we've just buy  1st stock so far. 
            }
            return release2; ///Since release1 is initiated as 0, so release2 will always higher than release1.
        }
    }


### Solution 2
Solution is commented in the code. Time complexity is O(k _n), space
complexity can be O(n) because this DP only uses the result from last step.
But for cleaness this solution still used O(k_ n) space complexity to preserve
similarity to the equations in the comments.

    
    
    class Solution {
    public:
        int maxProfit(vector<int> &prices) {
            // f[k, ii] represents the max profit up until prices[ii] (Note: NOT ending with prices[ii]) using at most k transactions. 
            // f[k, ii] = max(f[k, ii-1], prices[ii] - prices[jj] + f[k-1, jj]) { jj in range of [0, ii-1] }
            //          = max(f[k, ii-1], prices[ii] + max(f[k-1, jj] - prices[jj]))
            // f[0, ii] = 0; 0 times transation makes 0 profit
            // f[k, 0] = 0; if there is only one price data point you can't make any money no matter how many times you can trade
            if (prices.size() <= 1) return 0;
            else {
                int K = 2; // number of max transation allowed
                int maxProf = 0;
                vector<vector<int>> f(K+1, vector<int>(prices.size(), 0));
                for (int kk = 1; kk <= K; kk++) {
                    int tmpMax = f[kk-1][0] - prices[0];
                    for (int ii = 1; ii < prices.size(); ii++) {
                        f[kk][ii] = max(f[kk][ii-1], prices[ii] + tmpMax);
                        tmpMax = max(tmpMax, f[kk-1][ii] - prices[ii]);
                        maxProf = max(f[kk][ii], maxProf);
                    }
                }
                return maxProf;
            }
        }
    };


### Solution 3
It's not difficult to get the DP recursive formula:

    
    
    dp[k, i] = max(dp[k, i-1], prices[i] - prices[j] + dp[k-1, j-1]), j=[0..i-1]
    

For k transactions, on i-th day,  
if we don't trade then the profit is same as previous day dp[k, i-1];  
and if we bought the share on j-th day where j=[0..i-1], then sell the share
on i-th day then the profit is prices[i] - prices[j] + dp[k-1, j-1] .  
Actually j can be i as well. When j is i, the one more extra item prices[i] -
prices[j] + dp[k-1, j] = dp[k-1, i] looks like we just lose one chance of
transaction.

I see someone else use the formula dp[k, i] = max(dp[k, i-1], prices[i] -
prices[j] + dp[k-1, j]), where the last one is dp[k-1, j] instead of dp[k-1,
j-1]. It's not the direct sense, as if the share was bought on j-th day, then
the total profit of previous transactions should be done on (j-1)th day.
However, the result based on that formula is also correct, because if the
share was sold on j-th day and then bought again, it is the same if we didn't
trade on that day.

So the straigtforward implementation is:

    
    
            public int MaxProfitDp(int[] prices) {
                if (prices.Length == 0) return 0;
                var dp = new int[3, prices.Length];
                for (int k = 1; k <= 2; k++)  {
                    for (int i = 1; i < prices.Length; i++) {
                        int min = prices[0];
                        for (int j = 1; j <= i; j++)
                            min = Math.Min(min, prices[j] - dp[k-1, j-1]);
                        dp[k, i] = Math.Max(dp[k, i-1], prices[i] - min);
                    }
                }
    
                return dp[2, prices.Length - 1];
            }
    

Time complexity is O(kn^2), space complexity is O(kn).  
In the above code, min is repeated calculated. It can be easily improved as:

    
    
            public int MaxProfitDpCompact1(int[] prices) {
                if (prices.Length == 0) return 0;
                var dp = new int[3, prices.Length];
                for (int k = 1; k <= 2; k++) {
                    int min = prices[0];
                    for (int i = 1; i < prices.Length; i++) {
                        min = Math.Min(min, prices[i] - dp[k-1, i-1]);
                        dp[k, i] = Math.Max(dp[k, i-1], prices[i] - min);
                    }
                }
    
                return dp[2, prices.Length - 1];
            }
    

Time complexity is O(kn), space complexity is O(kn).  
If we slight swap the two 'for' loops:

    
    
            public int MaxProfitDpCompact1T(int[] prices) {
                if (prices.Length == 0) return 0;
                var dp = new int[3, prices.Length];
                var min = new int[3];
                Array.Fill(min, prices[0]);
                for (int i = 1; i < prices.Length; i++) {
                    for (int k = 1; k <= 2; k++) {
                        min[k] = Math.Min(min[k], prices[i] - dp[k-1, i-1]);
                        dp[k, i] = Math.Max(dp[k, i-1], prices[i] - min[k]);
                    }
                }
    
                return dp[2, prices.Length - 1];
            }
    

We need to save min for each transaction, so there are k 'min'.  
We can find the second dimension (variable i) is only dependent on the
previous one (i-1), so we can compact this dimension. (We can choose the first
dimension (variable k) as well since it is also only dependent on its previous
one k-1, but can't compact both.)

    
    
            public int MaxProfitDpCompact2(int[] prices) {
                if (prices.Length == 0) return 0;
                var dp = new int[3];
                var min = new int[3];
                Array.Fill(min, prices[0]);
                for (int i = 1; i < prices.Length; i++)  {
                    for (int k = 1; k <= 2; k++) {
                        min[k] = Math.Min(min[k], prices[i] - dp[k-1]);
                        dp[k] = Math.Max(dp[k], prices[i] - min[k]);
                    }
                }
    
                return dp[2];
            }
    

So time complexity is O(kn), space complexity becomes O(k).  
In this case, K is 2. We can expand the array to all named variables:

    
    
            public int MaxProfitDpCompactFinal(int[] prices)  {
                int buy1 = int.MaxValue, buy2 = int.MaxValue;
                int sell1 = 0, sell2 = 0;
    
                for (int i = 0; i < prices.Length; i++) {
                    buy1 = Math.Min(buy1, prices[i]);
                    sell1 = Math.Max(sell1, prices[i] - buy1);
                    buy2 = Math.Min(buy2, prices[i] - sell1);
                    sell2 = Math.Max(sell2, prices[i] - buy2);
                }
    
                return sell2;
            }
    

We can also explain the above codes in other words. On every day, we buy the
share with the price as low as we can, and sell the share with price as high
as we can. For the second transaction, we integrate the profit of first
transaction into the cost of the second buy, then the profit of the second
sell will be the total profit of two transactions.



