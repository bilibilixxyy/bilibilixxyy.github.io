---
layout: post
title: 646. Maximum Length of Pair Chain
---
### Question
You are given `n` pairs of numbers. In every pair, the first number is always
smaller than the second number.

Now, we define a pair `(c, d)` can follow another pair `(a, b)` if and only if
`b < c`. Chain of pairs can be formed in this fashion.

Given a set of pairs, find the length longest chain which can be formed. You
needn't use up all the given pairs. You can select pairs in any order.

 **Example 1:**  

    
    
     **Input:** [[1,2], [2,3], [3,4]]
    **Output:** 2
    **Explanation:** The longest chain is [1,2] - > [3,4]
    

**Note:**  

  1. The number of given pairs will be in the range [1, 1000].

### Solution 1
    
    
        public int findLongestChain(int[][] pairs) {
            if (pairs == null || pairs.length == 0) return 0;
            Arrays.sort(pairs, (a, b) -> (a[0] - b[0]));
            int[] dp = new int[pairs.length];
            Arrays.fill(dp, 1);
            for (int i = 0; i < dp.length; i++) {
                for (int j = 0; j < i; j++) {
                    dp[i] = Math.max(dp[i], pairs[i][0] > pairs[j][1]? dp[j] + 1 : dp[j]);
                }
            }
            return dp[pairs.length - 1];
        }
    


### Solution 2
    
    
    def findLongestChain(self, pairs):
        cur, res = float('-inf'), 0
        for p in sorted(pairs, key=lambda x: x[1]):
            if cur < p[0]: cur, res = p[1], res + 1
        return res


### Solution 3
This is equivalent to interval scheduling problem.

    
    
    public int findLongestChain(int[][] pairs) {
        Arrays.sort(pairs, (a,b) -> a[1] - b[1]);
        int sum = 0, n = pairs.length, i = -1;
        while (++i < n) {
            sum++;
            int curEnd = pairs[i][1];
            while (i+1 < n && pairs[i+1][0] <= curEnd) i++;
        }
        return sum;
    }
    



