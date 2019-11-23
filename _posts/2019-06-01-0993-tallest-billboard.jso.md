---
layout: post
title: 993. Tallest Billboard
---
### Question
You are installing a billboard and want it to have the largest height.  The
billboard will have two steel supports, one on each side.  Each steel support
must be an equal height.

You have a collection of `rods` which can be welded together.  For example, if
you have rods of lengths 1, 2, and 3, you can weld them together to make a
support of length 6.

Return the largest possible height of your billboard installation.  If you
cannot support the billboard, return 0.



 **Example 1:**

    
    
     **Input:** [1,2,3,6]
    **Output:** 6
    **Explanation:** We have two disjoint subsets {1,2,3} and {6}, which have the same sum = 6.
    

**Example 2:**

    
    
    **Input:** [1,2,3,4,5,6]
    **Output:** 10
    **Explanation:** We have two disjoint subsets {2,3,5} and {4,6}, which have the same sum = 10.
    

**Example 3:**

    
    
    **Input:** [1,2]
    **Output:** 0
    **Explanation:** The billboard cannot be supported, so we return 0.
    



 **Note:**

  1. `0 <= rods.length <= 20`
  2. `1 <= rods[i] <= 1000`
  3. `The sum of rods is at most 5000.`

### Solution 1
##  **Explanation** :

`dp[d]` mean the maximum pair of sum we can get with pair difference `d`  
For example, if have a pair of sum `(a, b)` with `a > b`, then `dp[a - b] = b`  
If we have `dp[diff] = a`, it means we have a pair of sum `(a, a + diff)`.  
And this is the biggest pair with difference = a

`dp` is initializes with `dp[0] = 0`;

Assume we have an init state like this  
\------- y ------|----- d -----|  
\------- y ------|

 **case 1**  
If put `x` to tall side  
\------- y ------|----- d -----|----- x -----|  
\------- y ------|

We update `dp[d + x] = max(dp[d + x], y )`

 **case 2.1**  
Put `x` to low side and `d >= x`:  
\-------y------|----- d -----|  
\-------y------|--- x ---|

We update `dp[d-x] = max( dp[d - x], y + x)`

 **case 2.2**  
Put `x` to low side and `d < x`:  
\------- y ------|----- d -----|  
\------- y ------|------- x -------|  
We update `dp[x - d] = max(dp[x - d], y + d)`

case 2.1 and case2.2 can merge into `dp[abs(x - d)] = max(dp[abs(x - d)], y +
min(d, x))`

  

##  **Time Complexity** :

`O(NM)`, where we have  
`N = rod.length <= 20`  
`S = sum(rods) <= 5000`  
`M = all possible sum = min(3^N, S)`

There are 3 ways to arrange a number: in the first group, in the second, not
used.  
The number of difference will be less than `3^N`.  
The only case to reach `3^N` is when rod = `[1,3,9,27,81...]`

  

 **Java, O(SN) using array, just for better reading:**

    
    
         public int tallestBillboard(int[] rods) {
            int[] dp = new int[5001];
            for (int d = 1; d < 5001; d++) dp[d] = -10000;
            for (int x : rods) {
                int[] cur = dp.clone();
                for (int d = 0; d + x < 5001; d++) {
                    dp[d + x] = Math.max(dp[d + x], cur[d]);
                    dp[Math.abs(d - x)] = Math.max(dp[Math.abs(d - x)], cur[d] + Math.min(d, x));
                }
            }
            return dp[0];
        }
    

**Java, using HashMap:**

    
    
        public  int tallestBillboard(int[] rods) {
            Map<Integer, Integer> dp = new HashMap<>(), cur;
            dp.put(0, 0);
            for (int x : rods) {
                cur = new HashMap<>(dp);
                for (int d : cur.keySet()) {
                    dp.put(d + x, Math.max(cur.get(d), dp.getOrDefault(x + d, 0)));
                    dp.put(Math.abs(d - x), Math.max(cur.get(d) + Math.min(d, x), dp.getOrDefault(Math.abs(d - x), 0)));
                }
            }
            return dp.get(0);
        }
    

**C++:**

    
    
         int tallestBillboard(vector<int>& rods) {
            unordered_map<int, int> dp;
            dp[0] = 0;
            for (int x : rods) {
                unordered_map<int, int> cur(dp);
                for (auto it: cur) {
                    int d = it.first;
                    dp[d + x] = max(dp[d + x],cur[d]);
                    dp[abs(d - x)] = max(dp[abs(d - x)], cur[d] + min(d, x));
                }
            }
            return dp[0];
        }
    

**Python:**

    
    
        def tallestBillboard(self, rods):
             dp = {0: 0}
            for x in rods:
                for d, y in dp.items():
                    dp[d + x] = max(dp.get(x + d, 0), y)
                    dp[abs(d - x)] = max(dp.get(abs(d - x), 0), y + min(d, x))
            return dp[0]
    

  

## One Optimisation

We do the same thing for both half of `rods`.  
Then we try to find the same difference in both results.

**Time Complexity** :  
`O(NM)`, where we have  
`N = rod.length <= 20`  
`S = sum(rods) <= 5000`  
`M = all possible sum = min(3^N/2, S)`

  

**Python:**

    
    
        def tallestBillboard(self, rods):
            def helper(A):
                 dp = {0: 0}
                for x in A:
                    for d, y in dp.items():
                        dp[d + x] = max(dp.get(x + d, 0), y)
                        dp[abs(d - x)] = max(dp.get(abs(d - x), 0), y + min(d, x))
                return dp
    
            dp, dp2 = helper(rods[:len(rods) / 2]), helper(rods[len(rods) / 2:])
            return max(dp[d] + dp2[d] + d for d in dp if d in dp2)
    


### Solution 2
Consider this problem as:  
Given a list of numbers, multiply each number with `1` or `0` or `-1`, make
the sum of all numbers to 0. Find a combination which has the largest sum of
all positive numbers.  
e.g. Given `[1,2,3,4,5,6]`, we have `1*0 + 2 + 3 - 4 + 5 - 6 = 0`, the sum of
all positive numbers is `2 + 3 + 5 = 10`. The answer is 10.

This is a knapsack problem.  
`dp[i][j]` represents whether the sum of first `i` numbers can be `j - 5000`.
`dp[0][5000] = true`.  
Then `dp[i + 1][j] = dp[i][j - rods[i]] | dp[i][j + rods[i]] | dp[i][j]`.  
`max[i][j]` represents the largest sum of all positive numbers when the sum of
first `i` numbers is `j - 5000`.

Time complexity: `O(N*sum)`

    
    
    class Solution {
        public int tallestBillboard(int[] rods) {
            int n = rods.length;
            boolean[][] dp = new boolean[n + 1][10001];
            int[][] max = new int[n + 1][10001];
            dp[0][5000] = true;
            for (int i = 0; i < n; i++) {
                for (int j = 0; j <= 10000; j++) {
                    if (j - rods[i] >= 0 && dp[i][j - rods[i]]) {
                        dp[i + 1][j] = true;
                        max[i + 1][j] = Math.max(max[i + 1][j], max[i][j - rods[i]] + rods[i]);
                    }
                    if (j + rods[i] <= 10000 && dp[i][j + rods[i]]) {
                        dp[i + 1][j] = true;
                        max[i + 1][j] = Math.max(max[i + 1][j], max[i][j + rods[i]]);
                    }
                    if (dp[i][j]) {
                        dp[i + 1][j] = true;
                        max[i + 1][j] = Math.max(max[i + 1][j], max[i][j]);
                    }
                }
            }
            return max[n][5000];
        }
    }
    

P.S. This solution can be optimized to O(sum) space and use `max` array only.


### Solution 3
I know it's not as cool as a DP solution, though during the context it may be
quicker to do DFS and add memoisation if you get TLE.

So we go through all rods, and either skip the rod, add it to the first
support (`s1`), or to the second support (`s2`). The result is the maximum of
these three operations. When we exhausted all rods (`i >= rs.size()`), we
return the rod size if both rods are the same, or zero. This way, our simple
DFS solution can be implemented in just a few lines of code:

    
    
    int tallestBillboard(vector<int>& rods, int i = 0, int s1 = 0, int s2 = 0) {
      if (i >= rods.size()) return s1 == s2 ? s1 : 0;
      return max({ tallestBillboard(rods, i + 1, s1, s2), 
                   tallestBillboard(rods, i + 1, s1 + rods[i], s2), 
                   tallestBillboard(rods, i + 1, s1, s2 + rods[i]) });
    }
    

You'll probably get TLE (certainly, in this particular case) for a simple DFS
solution, so the next step is to think about memoisation to avoid processing
identical conditions over and over again. We could memoise support sizes `s1`
and `s2` for the current rod number `i`. However, that would require a lot of
memory (and we will get MLE or TLE).

The intuition here is that we do not need to memoise the actual support sizes;
all is what it's important is the delta: `abs(s1 - s2)`. For example, if for
`i` rod, first support is `s1 == 50`, the second is `s2 == 30`, and in the
final suport sizes matches and equals 200, we will record `m[i][50 - 30] = 200
- 50` or `m[i][20] = 150`. Next time we process `i` and support sizes are 100
and 80 (the delta is 20), we know that there are matched size in the end that
adds 150 to the larger support: `m[i][100 - 80] + max(100, 80) = m[i][20] +
100 = 150 + 100 = 250`.

    
    
    int dfs(vector<int>& rs, int i, int s1, int s2, int m_sum, vector<vector<int>> &m) {
      if (s1 > m_sum || s2 > m_sum) return -1;
      if (i >= rs.size()) return s1 == s2 ? s1 : -1;
      if (m[i][abs(s1 - s2)] == -2) {
        m[i][abs(s1 - s2)] = max(-1, max({ dfs(rs, i + 1, s1, s2, m_sum, m),
          dfs(rs, i + 1, s1 + rs[i], s2, m_sum, m), dfs(rs, i + 1, s1, s2 + rs[i], m_sum, m) }) - max(s1, s2));
      }
      return m[i][abs(s1 - s2)] + (m[i][abs(s1 - s2)] == -1 ? 0 : max(s1, s2));
    }
    int tallestBillboard(vector<int>& rods) {
      int m_sum = accumulate(begin(rods), end(rods), 0) / 2;
      vector<vector<int>> m(rods.size(), vector<int>(m_sum + 1, -2));
      return max(0, dfs(rods, 0, 0, 0, m_sum, m));
    }
    

As an additinal optimization, I am also calculating maximum possible billboard
(sum of all rods divide by 2), and using it for pruning and vector allocation.
As the result, this solution runtime beats most of DP solutions other folks
posted.



