---
layout: post
title: 629. K Inverse Pairs Array
---
### Question
Given two integers `n` and `k`, find how many different arrays consist of
numbers from `1` to `n` such that there are exactly `k` inverse pairs.

We define an inverse pair as following: For `ith` and `jth` element in the
array, if `i` < `j` and `a[i]` > `a[j]` then it's an inverse pair; Otherwise,
it's not.

Since the answer may be very large, the answer should be modulo 109 \+ 7.

 **Example 1:**

    
    
     **Input:** n = 3, k = 0
    **Output:** 1
    **Explanation:** 
    Only the array [1,2,3] which consists of numbers from 1 to 3 has exactly 0 inverse pair.
    



**Example 2:**

    
    
    **Input:** n = 3, k = 1
    **Output:** 2
    **Explanation:** 
    The array [1,3,2] and [2,1,3] have exactly 1 inverse pair.
    



 **Note:**

  1. The integer `n` is in the range [1, 1000] and `k` is in the range [0, 1000].

### Solution 1
dp[n][k] denotes the number of arrays that have k inverse pairs for array
composed of 1 to n  
we can establish the recursive relationship between dp[n][k] and dp[n-1][i]:

if we put n as the last number then all the k inverse pair should come from
the first n-1 numbers  
if we put n as the second last number then there's 1 inverse pair involves n
so the rest k-1 comes from the first n-1 numbers  
...  
if we put n as the first number then there's n-1 inverse pairs involve n so
the rest k-(n-1) comes from the first n-1 numbers

`dp[n][k] =
dp[n-1][k]+dp[n-1][k-1]+dp[n-1][k-2]+...+dp[n-1][k+1-n+1]+dp[n-1][k-n+1]`

It's possible that some where in the right hand side the second array index
become negative, since we cannot generate negative inverse pairs we just treat
them as 0, but still leave the item there as a place holder.

`dp[n][k] =
dp[n-1][k]+dp[n-1][k-1]+dp[n-1][k-2]+...+dp[n-1][k+1-n+1]+dp[n-1][k-n+1]`  
`dp[n][k+1] =
dp[n-1][k+1]+dp[n-1][k]+dp[n-1][k-1]+dp[n-1][k-2]+...+dp[n-1][k+1-n+1] `

so by deducting the first line from the second line, we have

`dp[n][k+1] = dp[n][k]+dp[n-1][k+1]-dp[n-1][k+1-n]`

Below is the java code:

    
    
        public static int kInversePairs(int n, int k) {
            int mod = 1000000007;
            if (k > n*(n-1)/2 || k < 0) return 0;
            if (k == 0 || k == n*(n-1)/2) return 1;
            long[][] dp = new long[n+1][k+1];
            dp[2][0] = 1;
            dp[2][1] = 1;
            for (int i = 3; i <= n; i++) {
                dp[i][0] = 1;
                for (int j = 1; j <= Math.min(k, i*(i-1)/2); j++) {
                    dp[i][j] = dp[i][j-1] + dp[i-1][j];
                    if (j >= i) dp[i][j] -= dp[i-1][j-i];
                    dp[i][j] = (dp[i][j]+mod) % mod;
                }
            }
            return (int) dp[n][k];
        }


### Solution 2
For a better understanding, I would use O(n * k ) space instead of O(k) space.
It's easy to write O(k) space version when you understand this DP process.  
As @awice said in his [Post ](https://discuss.leetcode.com/topic/93721/python-
straightforward-with-explanation)

> For example, if we have some permutation of 1...4

>

>   * 5 x x x x creates 4 new inverse pairs

>   * x 5 x x x creates 3 new inverse pairs  
> ...

>   * x x x x 5 creates 0 new inverse pairs

>

### O(n * k ^ 2) Solution

We can use this formula to solve this problem

    
    
    dp[i][j] //represent the number of permutations of (1...n) with k inverse pairs.
    dp[i][j] = dp[i-1][j] + dp[i-1][j-1] + dp[i-1][j-2] + ..... +dp[i-1][j - i + 1]
    

So, We write a O(k*n^2) Solution through above formula like this

    
    
    public:
        int kInversePairs(int n, int k) {
            vector<vector<int>> dp(n + 1, vector<int>(k+1, 0));
            dp[0][0] = 1;
            for(int i = 1; i <= n; ++i){
                for(int j = 0; j < i; ++j){ // In number i, we can create 0 ~ i-1 inverse pairs 
                    for(int m = 0; m <= k; ++m){ //dp[i][m] +=  dp[i-1][m-j]
                        if(m - j >= 0 && m - j <= k){
                            dp[i][m] = (dp[i][m] + dp[i-1][m-j]) % mod; 
                        }
                    }
                }
            }
            return dp[n][k];
        }
    private:
        const int mod = pow(10, 9) + 7;
    };
    

But the above solution is too slow, it spends 1000+ms

### O(n * l) Solution

Look back to the above formula.

`dp[i][j] = dp[i-1][j] + dp[i-1][j-1] + dp[i-1][j-2] + ..... +dp[i-1][j - i +
1]`  
Let's consider this example  
if `i = 5`

    
    
    dp[i][0] = dp[i-1][0] (creates 0 inverse pair)
    dp[i][1] = dp[i-1][0] (1) + dp[i-1][1] (0)  =  dp[i][0] + dp[i-1][1]
    dp[i][2] = dp[i-1][0] (2) + dp[i-1][1] (1) + dp[i-1][2] (0) = dp[i][1] + dp[i-1][2]
    .
    .
    .
    dp[i][4] = dp[i-1][0] (4) + dp[i-1][1] (3) + dp[i-1][2] (2) + dp[i-1][3] (1) + dp[i-1][4] (0)  = dp[i][3] + dp[i-1][4]
    

Can you find the rules about above formula.  
if `j < i` , we can compute `dp[i][j] = dp[i][j-1] +dp[i-1][j]`

So, how about `j >= i`  
We know if we add number i into permutation(0 .. i -1 ), i can create 0 ~i -1
inverse pair  
If `j >= i` , we still use `dp[i][j] = dp[i][j-1] +dp[i-1][j]`.  
We must minus `dp[i][j-i]`. (In fact it minus`dp[i-1][j-i]`, because every`j
>= i`in dp vecor,it minus `dp[i-1][j-i]`individually)  
For example, if `i = 5`

`dp[i][5] = dp[i][4] + dp[i-1][5] - dp[i-1][0]`  
`dp[i][6] = dp[i][5] + dp[i-1][6] - dp[i-1][1]`

reference code

    
    
    class Solution {
    public:
        int kInversePairs(int n, int k) {
            vector<vector<int>> dp(n+1, vector<int>(k+1));
            dp[0][0] = 1;
            for(int i = 1; i <= n; ++i){
                dp[i][0] = 1;
                for(int j = 1; j <= k; ++j){
                    dp[i][j] = (dp[i][j-1] + dp[i-1][j]) % mod;
                    if(j - i >= 0){
                        dp[i][j] = (dp[i][j] - dp[i-1][j-i] + mod) % mod; 
                        //It must + mod, If you don't know why, you can check the case 1000, 1000
                    }
                }
            }
            return dp[n][k];
        }
    private:
        const int mod = pow(10, 9) + 7;
    };
    


### Solution 3
Let's try for a top-down dp. Suppose we know `dp[n][k]`, the number of
permutations of (1...n) with k inverse pairs.

Looking at a potential recursion for `dp[n+1][k]`, depending on where we put
the element (n+1) in our permutation, we may add 0, 1, 2, ..., n new inverse
pairs. For example, if we have some permutation of 1...4, then:

  * 5 x x x x creates 4 new inverse pairs
  * x 5 x x x creates 3 new inverse pairs
  * ...
  * x x x x 5 creates 0 new inverse pairs

where in the above I'm representing any permutation of 1...4 with x's.  
Thus, `dp[n+1][k] = sum_{x=0..n} dp[n][k-x]`.

This dp has `NK` states with `K/2` work, which isn't fast enough. We need to
optimize further.

Let `ds[n][k] = sum_{x=0..k-1} dp[n][x]`.  
Then `dp[n+1][k] = ds[n][k+1] - ds[n][k-n]`,  
and the left hand side is `ds[n+1][k+1] - ds[n+1][k]`.  
Thus, we can perform all calculations in terms of `ds`.

Finally, to save space, we will only store the two most recent rows of `ds`,
using `ds` and `new`.

In the code, we refer to `-ds[n][k-n+1]` instead of `-ds[n][k-n]` because the
`n` being considered is actually n+1. For example, when `n=2`, we are
appending information about `ds[2][k]` to `new`, so our formula of `dp[n+1][k]
= ds[n][k+1] - ds[n][k-n]` is `dp[2][k] = ds[1][k+1] - ds[1][k-1]`.

    
    
    def kInversePairs(self, N, K):
        MOD = 10**9 + 7
        ds = [0] + [1] * (K + 1)
        for n in xrange(2, N+1):
            new = [0]
            for k in xrange(K+1):
                v = ds[k+1]
                v -= ds[k-n+1] if k >= n else 0
                new.append( (new[-1] + v) % MOD )
            ds = new
        return (ds[K+1] - ds[K]) % MOD
    



