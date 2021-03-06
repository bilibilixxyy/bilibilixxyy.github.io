---
layout: post
title: 1042. Minimum Cost to Merge Stones
---
### Question
There are `N` piles of stones arranged in a row.  The `i`-th pile has
`stones[i]` stones.

A _move_ consists of merging **exactly  `K` consecutive** piles into one pile,
and the cost of this move is equal to the total number of stones in these `K`
piles.

Find the minimum cost to merge all piles of stones into one pile.  If it is
impossible, return `-1`.



 **Example 1:**

    
    
     **Input:** stones = [3,2,4,1], K = 2
    **Output:** 20
    **Explanation:**
    We start with [3, 2, 4, 1].
    We merge [3, 2] for a cost of 5, and we are left with [5, 4, 1].
    We merge [4, 1] for a cost of 5, and we are left with [5, 5].
    We merge [5, 5] for a cost of 10, and we are left with [10].
    The total cost was 20, and this is the minimum possible.
    

**Example 2:**

    
    
    **Input:** stones = [3,2,4,1], K = 3
    **Output:** -1
    **Explanation:** After any merge operation, there are 2 piles left, and we can't merge anymore.  So the task is impossible.
    

**Example 3:**

    
    
    **Input:** stones = [3,5,1,2,6], K = 3
    **Output:** 25
    **Explanation:**
    We start with [3, 5, 1, 2, 6].
    We merge [5, 1, 2] for a cost of 8, and we are left with [3, 8, 6].
    We merge [3, 8, 6] for a cost of 17, and we are left with [17].
    The total cost was 25, and this is the minimum possible.
    



 **Note:**

  * ` 1 <= stones.length <= 30`
  * `2 <= K <= 30`
  * `1 <= stones[i] <= 100`

### Solution 1
##  **Solution 1: 3D DP**

 **Intuition**  
Seem that most of games, especially stone games, are solved by dp?

 **Explanation**

`dp[i][j][m]` means the cost needed to merge `stone[i]` ~ `stones[j]` into `m`
piles.

Initial status `dp[i][i][1] = 0` and `dp[i][i][m] = infinity`

`dp[i][j][1] = dp[i][j][k] + stonesNumber[i][j]`  
`dp[i][j][m] = min(dp[i][mid][1] + dp[mid + 1][j][m - 1])`

The origine python2 solution is a bit too long on the memorization part.  
So I rewrote it in python3 with cache helper,  
so it will be clear for logic.

 **Complexity**  
Time `O(N^3/K)`, Space `O(KN^2)`

 **Python3:**

    
    
        def mergeStones(self, stones, K):
            n = len(stones)
             inf = float('inf')
            prefix = [0] * (n + 1)
            for i in range(n):
                prefix[i + 1] = prefix[i] + stones[i]
    
            import functools
    
            @functools.lru_cache(None)
            def dp(i, j, m):
                if (j - i + 1 - m) % (K - 1):
                    return inf
                if i == j:
                    return 0 if m == 1 else inf
                if m == 1:
                    return dp(i, j, K) + prefix[j + 1] - prefix[i]
                return min(dp(i, mid, 1) + dp(mid + 1, j, m - 1) for mid in range(i, j, K - 1))
            res = dp(0, n - 1, 1)
            return res if res < inf else -1
    

  

## **Solution 2: 2D DP**

**Explanation**  
Suggested by @yaoct, we can simplify the third parameter `m` in DP.

`stones[i]` ~ `stones[j]` will merge as much as possible.

Finally `(j - i) % (K - 1) + 1` piles will be left.

It's less than `K` piles and no more merger happens.

`dp[i][j]` means the minimum cost needed to merge `stones[i]` ~ `stones[j]`.

**Complexity**  
Time `O(N^3/K)` Space `O(N^2)`  
It can be improved, but I think it's fine now.

**Java**

    
    
         public int mergeStones(int[] stones, int K) {
            int n = stones.length;
            if ((n - 1) % (K - 1) > 0) return -1;
    
            int[] prefix = new int[n+1];
            for (int i = 0; i <  n; i++)
                prefix[i + 1] = prefix[i] + stones[i];
    
            int[][] dp = new int[n][n];
            for (int m = K; m <= n; ++m)
                for (int i = 0; i + m <= n; ++i) {
                    int j = i + m - 1;
                    dp[i][j] = Integer.MAX_VALUE;
                    for (int mid = i; mid < j; mid += K - 1)
                        dp[i][j] = Math.min(dp[i][j], dp[i][mid] + dp[mid + 1][j]);
                    if ((j - i) % (K - 1) == 0)
                        dp[i][j] += prefix[j + 1] - prefix[i];
                }
            return dp[0][n - 1];
        }
    

**C++**

    
    
        int mergeStones(vector<int>& stones, int K) {
            int n = stones. size();
            if ((n - 1) % (K - 1)) return -1;
    
            vector<int> prefix(n + 1);
            for (int i = 0; i <  n; i++)
                prefix[i + 1] = prefix[i] + stones[i];
    
            vector<vector<int> > dp(n, vector<int>(n));
            for (int m = K; m <= n; ++m)
                for (int i = 0; i + m <= n; ++i) {
                    int j = i + m - 1;
                    dp[i][j] = INT_MAX;
                    for (int mid = i; mid < j; mid += K - 1)
                        dp[i][j] = min(dp[i][j], dp[i][mid] + dp[mid + 1][j]);
                    if ((j - i) % (K - 1) == 0)
                        dp[i][j] += prefix[j + 1] - prefix[i];
                }
            return dp[0][n - 1];
        }
    

**Python3**

    
    
        def mergeStones(self, stones, K):
            n = len(stones)
             if (n - 1) % (K - 1): return -1
            prefix = [0] * (n + 1)
            for i in range(n):
                prefix[i + 1] = prefix[i] + stones[i]
    
            import functools
            @functools.lru_cache(None)
            def dp(i, j):
                if j - i + 1 < K: return 0
                res = min(dp(i, mid) + dp(mid + 1, j) for mid in range(i, j, K - 1))
                if (j - i) % (K - 1) == 0:
                    res += prefix[j + 1] - prefix[i]
                return res
            return dp(0, n - 1)
    

  

## **FAQ**

 **Q: Why`mid` jump at step `K - 1`**  
A: We can merge `K` piles into one pile,  
we can't merge `K + 1` piles into one pile.  
We can merge `K + K - 1` piles into on pile,  
We can merge `K + (K - 1) * steps` piles into one pile.

  

 **Update 2019-03-04**  
Sorry guys, It seems that somehow it started kind of debate in one of my
replies.  
I didn't mean to do that and I feel I have to say something.

  1. Yes, I agree that people have right to express in their comfortable language, including Chinese.  
It's not the same as the situation of a meeting room.  
User don't take others' time and force them to listen to you.  
Reader can choose what they want to read.  
Like ebooker and trip adviser, they have comments in all languages.

  2. I strongly disagree any unreasonable downvotes.  
Posts and reply should not be downvoted for no reason like language.  
Personally I receive downvotes for each of my posts.  
Of course, people have right to do that but please at least say something or
leave a message.  
Like "I downvote for the reason that....", so that I can also improve somehow,
right?

  3. I encourage everyone to express in English and discuss with all others.  
The main reason is that English is still one important skill for engineers.  
We need to learn from documents in English.  
Moreover, as a Chinese engineer, I hope I can bring the good stuff back to the
world.

  4. In the end, the most important, I encourage everyone to learn some Chinese :)


### Solution 2
Let's first think this problem in a simple way, **what if we can only merge 2
adjacent piles into one pile**?

For given example [3,2,4,1], we will normally think this as a greedy problem,
we always merge two relatively small piles.  
[[3, 2], 4, 1] -> [5, [4, 1]] -> [5, 5] -> [10](cost: 20).

While one counterexample is [6,4,4,6],  
if we merge greedily, [6, [4, 4], 6] -> [[6, 8], 6] -> [14, 6] -> [20] (cost:
42),  
while the optimal way is [[6, 4], 4, 6] -> [10, [4, 6]] -> [10, 10] -> [20]
(cost:40).

What if we think this problem reversely, **which two piles should we merge at
the last step**?

We don't know which two piles to merge for now, but we can know the cost of
that step, which is **the sum of that two piles.**  
[3 | 2, 4, 1]  
3 + 7 = 10  
[3 , 2 | 4, 1]  
5 + 5 = 10  
[3 , 2, 4 | 1]  
9 + 1 = 10  
No matter how to split the two piles, the sum is always the sum of the two
piles.

Now the only thing that matters is how to get the minimum cost to split to two
piles.  
So we need to know **the minimum cost of merging left part to 1 pile, and
minimum cost of merging right part to 1 pile** , this is a typical sub
problem.

 **State: Minimum cost merging piles from i to j to 1 pile.**

 **Function: dp[i][j] = min(sum[i][j] + dp[i][k] + dp[k + 1][j]) (i <= k <
j)**

 **Init: dp[i][i] = 0 (Already a pile)**

 **Answer: dp[1][len] (len is the stones number)**

    
    
    public int mergeStonesTwo(int[] stones) {
         if (stones == null || stones.length == 0) {
            return 0;
        }
        int len = stones.length;
        int max = Integer.MAX_VALUE;
        int[][] dp = new int[len + 1][len + 1];
        int[] prefixSum = new int[len + 1];
        int i, j, k, l;
        for (i = 1; i <= len; i++) {
            prefixSum[i] = prefixSum[i - 1] + stones[i - 1];
        }
        
        for (i = 1; i <= len; i++) {
            dp[i][i] = 0;
        }
        
        for (l = 2; l <= len; l++) {
            for (i = 1; i <= len - l + 1; i++) {
                j = i + l - 1;
                dp[i][j] = max;
                int sum = prefixSum[j] - prefixSum[i - 1];
                for (k = i; k < j; k++) {
                    dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k + 1][j] + sum);
                }
            }
        }
        
        return dp[1][len];
    }
    

**Time Complexity: O(n ^ 3) (n is the number of stone)**

During interview, this might be our first question, the follow-up may ask us
what if we can merge x consecutive piles? Just like original problem.  
Now our sub problem becomes: we need to know **the minimum cost of merging
left part to x - 1 piles and right part to 1 pile.**  
Our state has one more information to know, how many piles. So we add the
field to our dp array.

**State: Minimum cost merging piles from i to j to k pile.**

**Function:  
dp[i][j][1] = dp[i][j][K] + sum[i][j] (dp[i][j][K] != max)  
dp[i][j][k] = min(dp[i][t][k - 1] + dp[t + 1][j][1]) (t ∈ [i, j)  &&
dp[i][t][k - 1] != max && dp[t+1][j][1] != max && k ∈ [2, K])**

**Init: dp[i][i][1] = 0 (Already a pile) others = max**

**Answer: dp[1][len][1] (len is the stones number)**

**Time Complexity: O(n^3 * K) (n is the number of stone)**

Similar problem include [312\. Burst
Balloons](https://leetcode.com/problems/burst-balloons/description/). They are
all dynamic programming problem related to interval.

    
    
    // Bottom-Up
    class Solution {
        public int mergeStones(int[] stones, int K) {
            int len = stones.length;
            if ((len - 1) % (K - 1) != 0) {
                return -1;
            }
            
            int i, j, k, l, t;
            
            int[] prefixSum = new int[len + 1];
            for (i = 1; i <= len; i++) {
                prefixSum[i] = prefixSum[i - 1] + stones[i - 1];
            }
            
            int max = 99999999;
            int[][][] dp = new int[len + 1][len + 1][K + 1];
            for (i = 1; i <= len; i++) {
                for (j = 1; j <= len; j++) {
                    for (k = 1; k <= K; k++) {
                        dp[i][j][k] = max;
                    }
                }
            }
            
            for (i = 1; i <= len; i++) {
                dp[i][i][1] = 0;
            }
    
            for (l = 2; l <= len; l++) {
                for (i = 1; i <= len - l + 1; i++) {
                    j = i + l - 1;
                    for (k = 2; k <= K; k++) {
                        for (t = i; t < j; t++) {
                            dp[i][j][k] = Math.min(dp[i][j][k], dp[i][t][k - 1] + dp[t + 1][j][1]);
                        }
                    }
    
                    dp[i][j][1] = dp[i][j][K] + prefixSum[j] - prefixSum[i - 1];   
                }
            }
    
            return dp[1][len][1];
        }
    }
    
    
    
    // Top-Down
    class Solution {
        int[][][] dp;
        int max = 99999999;
        int K;
        
        public int mergeStones(int[] stones, int K) {
            this.K = K;
            int len = stones.length;
            if ((len - 1) % (K - 1) != 0) {
                return -1;
            }
            dp = new int[len + 1][len + 1][K + 1];
            int[] prefixSum = new int[len + 1];
            
            int i;
            for (i = 1; i <= len; i++) {
                prefixSum[i] = prefixSum[i - 1] + stones[i - 1];
            }
            
            return getResult(prefixSum, 1, len, 1);
        }
        
        private int getResult(int[] prefixSum, int left, int right, int piles) {
            if (dp[left][right][piles] != 0) {
                return dp[left][right][piles];
            }
            int res = max;
            int t;
            if (left == right) {
                res = (piles == 1) ? 0 : max;
            }
            else {
                if (piles == 1) {
                    res = getResult(prefixSum, left, right, K) + prefixSum[right] - prefixSum[left - 1]; 
                }
                else {
                    for (t = left; t < right; t++) {
                        res = Math.min(res, getResult(prefixSum, left, t, piles - 1) + getResult(prefixSum, t + 1, right, 1));
                    }
                }
            }
            dp[left][right][piles] = res;
            return res;
        }
    }
    

The max value is somewhat confusing here, so I update the version using
Integer.MAX_VALUE. The idea of MAX is to mark the invalid state. If we don't
check, the res may be negative, cause Integer.MAX_VALUE + 1 =
Integer.MIN_VALUE.

    
    
    // bottom-up
    class Solution {
        public int mergeStones(int[] stones, int K) {
            int len = stones.length;
            if ((len - 1) % (K - 1) != 0) {
                return -1;
            }
            
            int i, j, k, l, t;
            
            int[] prefixSum = new int[len + 1];
            for (i = 1; i <= len; i++) {
                prefixSum[i] = prefixSum[i - 1] + stones[i - 1];
            }
            
            int max = Integer.MAX_VALUE;
            int[][][] dp = new int[len + 1][len + 1][K + 1];
            for (i = 1; i <= len; i++) {
                for (j = 1; j <= len; j++) {
                    for (k = 1; k <= K; k++) {
                        dp[i][j][k] = max;
                    }
                }
            }
            
            for (i = 1; i <= len; i++) {
                dp[i][i][1] = 0;
            }
    
            for (l = 2; l <= len; l++) {
                for (i = 1; i <= len - l + 1; i++) {
                    j = i + l - 1;
                    for (k = 2; k <= K; k++) {
                        for (t = i; t < j; t++) {
                            if (dp[i][t][k - 1] == max || dp[t + 1][j][1] == max) {
                                continue;
                            }
                            dp[i][j][k] = Math.min(dp[i][j][k], dp[i][t][k - 1] + dp[t + 1][j][1]);
                        }
                    }
                    if (dp[i][j][K] == max) {
                        continue;
                    }
                    dp[i][j][1] = dp[i][j][K] + prefixSum[j] - prefixSum[i - 1];   
                }
            }
    
            return dp[1][len][1];
        }
    }
    
    
    
    // Top-down
    class Solution {
        int[][][] dp;
        int max = Integer.MAX_VALUE;
        int K;
        
        public int mergeStones(int[] stones, int K) {
            this.K = K;
            int len = stones.length;
            if ((len - 1) % (K - 1) != 0) {
                return -1;
            }
            dp = new int[len + 1][len + 1][K + 1];
            int[] prefixSum = new int[len + 1];
            
            int i;
            for (i = 1; i <= len; i++) {
                prefixSum[i] = prefixSum[i - 1] + stones[i - 1];
            }
            
            getResult(prefixSum, 1, len, 1);
            return dp[1][len][1];
        }
        
        private int getResult(int[] prefixSum, int left, int right, int piles) {
            if (dp[left][right][piles] != 0) {
                return dp[left][right][piles];
            }
            int res = max;
            int t;
            if (left == right) {
                res = (piles == 1) ? 0 : max;
            }
            else {
                if (piles == 1) {
                    int mergeK = getResult(prefixSum, left, right, K);
                    if (mergeK != max) {
                        res = mergeK + prefixSum[right] - prefixSum[left - 1]; 
                    }
                }
                else {
                    for (t = left; t < right; t++) {
                        int l = getResult(prefixSum, left, t, piles - 1);
                        int r = getResult(prefixSum, t + 1, right, 1);
                        if (l != max && r != max) {
                            res = Math.min(res, l + r);
                        }
                    }
                }
            }
            dp[left][right][piles] = res;
            return res;
        }
    }
    


### Solution 3
> We keep merging K piles of stones until there is only one pile.

>

> For the last step, stones[i .. j] are divided into K piles, and we merge
them into one pile, which costs `sum(nums[i .. j]) + cost to make stones[i ..
j] form K piles`.

The problem `get the minimum cost to make stones[i .. j] form 1 pile` equals
to

    
    
    	the minimum cost to make stones[i .. j] form K piles
    	+ sum(nums[i .. j])
    

The subproblem `the minimum cost to make stones[i .. j] form K piles` equals
to

    
    
    	the minimum cost to make stones[i .. k] form K - 1 piles
    	+ the minimum cost to make stones[k + 1 .. j] form 1 pile 
    	+ sum(nums[i .. j])
    

* * *
    
    
        private int K;
        private int[] preSum; // preSum[i] is sum of stones[0] to stones[i].
        private int[][][] memo;
    
        public int mergeStones(int[] stones, int K) {
            if (stones.length == 1) return 0;
            
            this.K = K;
            int stonesLen = stones.length;
            memo = new int[stonesLen + 1][stonesLen + 1][K + 1];
            preSum = new int[stonesLen + 1];
            
            buildPreSum(stones, K);
            
            int result = mergeStonesRecur(1, stones.length, 1, stones);
            
            return result == Integer.MAX_VALUE ? -1 : result;
        }
    
        private void buildPreSum(int[] stones, int K) {
            preSum[0] = 0;
            for (int i = 1; i < preSum.length; i++) {
                preSum[i] = preSum[i - 1] + stones[i - 1];
            }
        }
    
        // Minimum cost merging piles from i to j into targetPiles pile.
        public int mergeStonesRecur(int i, int j, int targetPiles, int[] stones) {
            if (memo[i][j][targetPiles] != 0) return memo[i][j][targetPiles];
            
            if (j - i + 1 < targetPiles) return Integer.MAX_VALUE;
            if (i == j) return (targetPiles == 1) ? 0 : Integer.MAX_VALUE;
            
            if (targetPiles == 1) {
                int subMinCost = mergeStonesRecur(i, j, K, stones);
                if (subMinCost != Integer.MAX_VALUE) memo[i][j][targetPiles] = preSum[j] - preSum[i - 1] + subMinCost;
                else memo[i][j][targetPiles] = subMinCost;
                return memo[i][j][targetPiles];
            }
    
            int minCost = Integer.MAX_VALUE;
            for (int k = i; k <= j - 1; k++) {
                int leftCost = mergeStonesRecur(i, k, targetPiles - 1, stones);
                if (leftCost == Integer.MAX_VALUE) continue;
                int rightCost = mergeStonesRecur(k + 1, j, 1, stones);
                if (rightCost == Integer.MAX_VALUE) continue;
                minCost = Math.min(leftCost + rightCost, minCost);
            }
            
            memo[i][j][targetPiles] = minCost;
            return minCost;
        }
    

**(人 •͈ᴗ•͈)** Thanks for voting!



