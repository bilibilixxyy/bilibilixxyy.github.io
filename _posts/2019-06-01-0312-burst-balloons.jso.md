---
layout: post
title: 312. Burst Balloons
---
### Question
Given `n` balloons, indexed from `0` to `n-1`. Each balloon is painted with a
number on it represented by array `nums`. You are asked to burst all the
balloons. If the you burst balloon `i` you will get `nums[left] * nums[i] *
nums[right]` coins. Here `left` and `right` are adjacent indices of `i`. After
the burst, the `left` and `right` then becomes adjacent.

Find the maximum coins you can collect by bursting the balloons wisely.

 **Note:**

  * You may imagine `nums[-1] = nums[n] = 1`. They are not real therefore you can not burst them.
  * 0 ≤ `n` ≤ 500, 0 ≤ `nums[i]` ≤ 100

 **Example:**

    
    
     **Input:** [3,1,5,8]
    **Output:** 167 
    **Explanation:**nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
                 coins =  3*1*5      +  3*5*8    +  1*3*8      + 1*8*1   = 167
    

### Solution 1
 **See[here for a better view](http://algobox.org/burst-balloons/)**

 **Be Naive First**

When I first get this problem, it is far from dynamic programming to me. I
started with the most naive idea the backtracking.

We have n balloons to burst, which mean we have n steps in the game. In the i
th step we have n-i balloons to burst, i = 0~n-1. Therefore we are looking at
an algorithm of O(n!). Well, it is slow, probably works for n < 12 only.

Of course this is not the point to implement it. We need to identify the
redundant works we did in it and try to optimize.

Well, we can find that for any balloons left the maxCoins does not depends on
the balloons already bursted. This indicate that we can use memorization (top
down) or dynamic programming (bottom up) for all the cases from small numbers
of balloon until n balloons. How many cases are there? For k balloons there
are C(n, k) cases and for each case it need to scan the k balloons to compare.
The sum is quite big still. It is better than O(n!) but worse than O(2^n).

 **Better idea**

We then think can we apply the divide and conquer technique? After all there
seems to be many self similar sub problems from the previous analysis.

Well, the nature way to divide the problem is burst one balloon and separate
the balloons into 2 sub sections one on the left and one one the right.
However, in this problem the left and right become adjacent and have effects
on the maxCoins in the future.

Then another interesting idea come up. Which is quite often seen in dp problem
analysis. That is reverse thinking. Like I said the coins you get for a
balloon does not depend on the balloons already burst. Therefore  
instead of divide the problem by the first balloon to burst, we divide the
problem by the last balloon to burst.

Why is that? Because only the first and last balloons we are sure of their
adjacent balloons before hand!

For the first we have `nums[i-1]*nums[i]*nums[i+1]` for the last we have
`nums[-1]*nums[i]*nums[n]`.

OK. Think about n balloons if i is the last one to burst, what now?

We can see that the balloons is again separated into 2 sections. But this time
since the balloon i is the last balloon of all to burst, the left and right
section now has well defined boundary and do not affect each other! Therefore
we can do either recursive method with memoization or dp.

 **Final**

Here comes the final solutions. Note that we put 2 balloons with 1 as
boundaries and also burst all the zero balloons in the first round since they
won't give any coins.  
The algorithm runs in O(n^3) which can be easily seen from the 3 loops in dp
solution.

 **Java D &C with Memoization**

    
    
    public int maxCoins(int[] iNums) {
        int[] nums = new int[iNums.length + 2];
        int n = 1;
        for (int x : iNums) if (x > 0) nums[n++] = x;
        nums[0] = nums[n++] = 1;
    
    
        int[][] memo = new int[n][n];
        return burst(memo, nums, 0, n - 1);
    }
    
    public int burst(int[][] memo, int[] nums, int left, int right) {
        if (left + 1 == right) return 0;
        if (memo[left][right] > 0) return memo[left][right];
        int ans = 0;
        for (int i = left + 1; i < right; ++i)
            ans = Math.max(ans, nums[left] * nums[i] * nums[right] 
            + burst(memo, nums, left, i) + burst(memo, nums, i, right));
        memo[left][right] = ans;
        return ans;
    }
    // 12 ms
    

**Java DP**

    
    
    public  int maxCoins(int[] iNums) {
        int[] nums = new int[iNums.length + 2];
        int n = 1;
        for (int x : iNums) if (x > 0) nums[n++] = x;
        nums[0] = nums[n++] = 1;
    
    
        int[][] dp = new int[n][n];
        for (int k = 2; k < n; ++k)
            for (int left = 0; left < n - k; ++left) {
                int right = left + k;
                for (int i = left + 1; i < right; ++i)
                    dp[left][right] = Math.max(dp[left][right], 
                    nums[left] * nums[i] * nums[right] + dp[left][i] + dp[i][right]);
            }
    
        return dp[0][n - 1];
    }
    // 17 ms
    

**C++ DP**

    
    
     int maxCoinsDP(vector<int> &iNums) {
        int nums[iNums.size() + 2];
        int n = 1;
        for (int x : iNums) if (x > 0) nums[n++] = x;
        nums[0] = nums[n++] = 1;
    
    
        int dp[n][n] = {};
        for (int k = 2; k < n; ++k) {
            for (int left = 0; left < n - k; ++left)
                int right = left + k;
                for (int i = left + 1; i < right; ++i)
                    dp[left][right] = max(dp[left][right],
                         nums[left] * nums[i] * nums[right] + dp[left][i] + dp[i][right]);
            }
    
        return dp[0][n - 1];
    }
    // 16 ms
    

**Python DP**

    
    
    def maxCoins(self, iNums):
        nums = [ 1] + [i for i in iNums if i > 0] + [1]
        n = len(nums)
        dp = [[0]*n for _ in xrange(n)]
    
        for k in xrange(2, n):
            for left in xrange(0, n - k):
                right = left + k
                for i in xrange(left + 1,right):
                    dp[left][right] = max(dp[left][right],
                           nums[left] * nums[i] * nums[right] + dp[left][i] + dp[i][right])
        return dp[0][n - 1]
    
    # 528ms


### Solution 2
I think the most upvoted post didn't talk about what dp[i][j] represent and
what exactly does the transition function :

**for (int k = left; k <= right; ++k)  
dp[left][right] = max(dp[left][right], nums[left-1] * nums[k] * nums[right+1]
+ dp[left][k-1] + dp[k+1][right])****  
means.

Or maybe it did talk about it but I miss it. But anyway here is my understand
of this problem after reading countless of posts and comments :

First of all, dp[i][j] in here means, the maximum coins we get after we burst
**all** the balloons between i and j in the original array.

For example with input [3,1,5,8] :

dp[0][0] means we burst ballons between [0,0], which means we only burst the
first balloon in original array. So dp[0][0] is 1 * 3 * 1 = 3.

dp[1][1] means we burst balloons between [1][1], which means we only burst the
second ballon in the original array. So dp[1][1] is 3 * 1 * 5 = 15.

So in the end for this problem we want to find out dp[0][ arr.length - 1 ],
which is the maximum value we can get when we burst all the balloon between [0
, length -1]

To get that we need the transition function :

**for (int k = left; k <= right; ++k)  
dp[left][right] = max(dp[left][right], nums[left-1] * nums[k] * nums[right+1]
+ dp[left][k-1] + dp[k+1][right])****

This transition function basically says in order to get the maximum value we
can get for bursting all the balloons between [ i , j] , we just loop through
each balloon between these two indexes and make them to be the last balloon to
be burst,

why we pick it as the last balloon to burst ?

For example when calculating dp[0,3] and picking index 2 as the last balloon
to burst,

[ 3 , 1 , 5 , 8] , that means 5 is the last balloon to burst between [0,3] ,
to get the maximum value when picking 5 as the last balloon to burst :

max = maximum value of bursting all the balloon on the left side of 5 +
maximum value of bursting all the balloon on the right side of 5 + bursting
balloon 5 when left side and right side are gone.

That is dp[0, 1] + nums[0 - 1] * nums[2] * nums[3 + 1] + + dp[3,3];

That is dp[left, k - 1] + nums[left - 1] * nums[k] * nums[right + 1] + dp[k+1,
right] ;

to get the maximum dp[left, right] we just loop through all the possible value
of k to get the maximum.

Hope it helps!


### Solution 3
    
    
    int maxCoins(vector<int>& nums) {
        int N = nums.size();
        nums.insert(nums.begin(), 1);
        nums.insert(nums.end(), 1);
    
        // rangeValues[i][j] is the maximum # of coins that can be obtained
        // by popping balloons only in the range [i,j]
        vector<vector<int>> rangeValues(nums.size(), vector<int>(nums.size(), 0));
        
        // build up from shorter ranges to longer ranges
        for (int len = 1; len <= N; ++len) {
            for (int start = 1; start <= N - len + 1; ++start) {
                int end = start + len - 1;
                // calculate the max # of coins that can be obtained by
                // popping balloons only in the range [start,end].
                // consider all possible choices of final balloon to pop
                int bestCoins = 0;
                for (int final = start; final <= end; ++final) {
                    int coins = rangeValues[start][final-1] + rangeValues[final+1][end]; // coins from popping subranges
                    coins += nums[start-1] * nums[final] * nums[end+1]; // coins from final pop
                    if (coins > bestCoins) bestCoins = coins;
                }
                rangeValues[start][end] = bestCoins;
            }
        }
        return rangeValues[1][N];
    }



