---
layout: post
title: 747. Min Cost Climbing Stairs
---
### Question
On a staircase, the `i`-th step has some non-negative cost `cost[i]` assigned
(0 indexed).

Once you pay the cost, you can either climb one or two steps. You need to find
minimum cost to reach the top of the floor, and you can either start from the
step with index 0, or the step with index 1.

 **Example 1:**  

    
    
     **Input:** cost = [10, 15, 20]
    **Output:** 15
    **Explanation:** Cheapest is start on cost[1], pay that cost and go to the top.
    

**Example 2:**  

    
    
    **Input:** cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
    **Output:** 6
    **Explanation:** Cheapest is start on cost[0], and only step on 1s, skipping cost[3].
    

**Note:**  

  1. `cost` will have a length in the range `[2, 1000]`.
  2. Every `cost[i]` will be an integer in the range `[0, 999]`.

### Solution 1
A bit DP and a bit Greedy

    
    
    public int minCostClimbingStairs(int[] cost) {
            for (int i = 2; i < cost.length; i++) {
                cost[i] += Math.min(cost[i-1], cost[i-2]);
            }
            return Math.min(cost[cost.length-1], cost[cost.length-2]);
        }
    


### Solution 2
**Solution #1:** Bottom-Up dynamic programming

Let `dp[i]` be the minimum cost to reach the i-th stair.

 **Base case:**

`dp[0]=cost[0]`  
`dp[1]=cost[1]`

 **DP formula:**

`dp[i]=cost[i]+min(dp[i-1],dp[i-2])`

 **Note:** the top floor `n` can be reached from either 1 or 2 stairs away,
return the minimum.

    
    
     class Solution {
    public:
        int minCostClimbingStairs(vector<int>& cost) {
            int n=(int)cost.size();
            vector<int> dp(n);
            dp[0]=cost[0];
            dp[1]=cost[1];
            for (int i=2; i<n; ++i)
                dp[i]=cost[i]+min(dp[i-2],dp[i-1]);
            return min(dp[n-2],dp[n-1]);
        }
    };
    

**Solution #2:** same DP concept as above, however, more concisely we only
need the last two answers ( `b` and `c` ) in order to calculate the current
answer `a` as follows:

Let `a` be the min cost to reach the `i-th` stair  
Let `b` be the min cost to reach the `i-1th` stair ( one stair away from
`i-th` stair )  
Let `c` be the min cost to reach the `i-2th` stair ( two stairs away from
`i-th` stair )

**Base case:**

`c=cost[0]` ( two stairs away from `i-th` stair starting at i=2 )  
`b=cost[1]` ( one stair away from `i-th` stair starting at i=2 )

**DP formula:**

`a=cost[i]+min(b,c)`

    
    
     class Solution {
    public:
        int minCostClimbingStairs(vector<int>& cost) {
            int b=cost[1],c=cost[0];
            for (int i=2,a; i<cost.size(); ++i,c=b,b=a) a=cost[i]+min(b,c);
            return min(b,c);
        }
    };
    

**Solution #3:** Recursive Top-Down

Assume we know the minimum cost to reach the stairs `n-2` and `n-1`, then the
minimum cost to reach the n-th stair is the minimum between the minimum cost
to reach stair `n-2` and the minimum cost to reach stair `n-1` plus the cost
of the n-th stair ( If the n-th stair exists. The last stair does not exist,
its cost is 0. )

**Base case:**

`memo[0]=c[0]`  
`memo[1]=c[1]`

**Recursive formula:**

Let the minimum cost to reach stair `n` be stored in a hash table `memo[n]`.
This is needed in order for overlapping sub-problems to only be calculated
once. (Note: without this memo-ization TLE will occur.)

`memo[n]`=(cost of n-th stair)+min(min cost to reach stair `n-2`, min cost to
reach stair `n-1`)

**Note:** the very last stair is reached when `n` is equal to the size of the
`cost` vector. As mentioned previously, the last stair's cost is 0.

    
    
     class Solution {
    public:
        int minCostClimbingStairs(vector<int>& cost) {
            return go(cost, cost.size());
        }
    private:
        unordered_map<int,int> memo;
        int go(vector<int>& c, int n){
            if (memo.count(n)) return memo[n];
            if (n<=1) return memo[n]=c[n];
            return memo[n]=(n==c.size() ? 0 : c[n])+min(go(c,n-2),go(c,n-1));
        }
    };
    


### Solution 3
This is the example provided in the Question

    
    
    Input: cost = [10, 15, 20]
    Output: 15
    Explanation: Cheapest is start on cost[1], pay that cost and go to the top.
    

Won't it be cheap to start at cost[0] and since you paid 10, Climb two steps
to reach 20 which is top. Hence you only pay 10.

Or is my understanding of question is not corrrect ?



