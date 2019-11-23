---
layout: post
title: 1024. Triples with Bitwise AND Equal To Zero
---
### Question
Given an array of integers `A`, find the number of triples of indices (i, j,
k) such that:

  * `0 <= i < A.length`
  * `0 <= j < A.length`
  * `0 <= k < A.length`
  * `A[i] & A[j] & A[k] == 0`, where `&` represents the bitwise-AND operator.



 **Example 1:**

    
    
     **Input:** [2,1,3]
    **Output:** 12
    **Explanation:** We could choose the following i, j, k triples:
    (i=0, j=0, k=1) : 2 & 2 & 1
    (i=0, j=1, k=0) : 2 & 1 & 2
    (i=0, j=1, k=1) : 2 & 1 & 1
    (i=0, j=1, k=2) : 2 & 1 & 3
    (i=0, j=2, k=1) : 2 & 3 & 1
    (i=1, j=0, k=0) : 1 & 2 & 2
    (i=1, j=0, k=1) : 1 & 2 & 1
    (i=1, j=0, k=2) : 1 & 2 & 3
    (i=1, j=1, k=0) : 1 & 1 & 2
    (i=1, j=2, k=0) : 1 & 3 & 2
    (i=2, j=0, k=1) : 3 & 2 & 1
    (i=2, j=1, k=0) : 3 & 1 & 2
    



 **Note:**

  1. ` 1 <= A.length <= 1000`
  2. `0 <= A[i] < 2^16`

### Solution 1
 **Update:**

Thanks for the inspiring solutions in the comments using `HashMap` which runs
`O(n^2)` time. I didn't come up with such solutions in the contest. That's
really efficient for this problem specifically. However, I think my solution
is more scalable in terms of **the number of indices to be picked** is very
large. Say it's `M` instead of `3`, the runtime is `O(M * 2^16 * n)`.

Here I updated an `O(M * 2^16)` space version to help understanding, where
`dp[i][j]` represents the number of combinations if we pick `i` numbers where
the `AND` of these numbers is `j`:

    
    
    class Solution {
        public int countTriplets(int[] A) {
            int N = 1 << 16, M = 3;
            int[][] dp = new int[M + 1][N];
            dp[0][N - 1] = 1;
            for (int i = 0; i < M; i++) {
                for (int k = 0; k < N; k++) {
                    for (int a : A) {
                        dp[i + 1][k & a] += dp[i][k];
                    }
                }
            }
            return dp[M][0];
        }
    }
    

Appreciate for other solutions again!

**Original Post:**

For each `i`, assume that if we pick `i` numbers from `A` array(allow
duplicates), the `AND` of these `i` numbers is `k`.  
Then `dp[k]` represents the number of combinations for such `i` and `k`. We
update this `dp` array for 3 times.

Example:  
`i=2` means we pick `2` numbers.  
`dp[10] = 5` means when we pick `2` numbers, the count of combinations is `5`,
where the `AND` result of such numbers is `10`.

Tricky:  
We initialize the `dp[(1<<16) - 1]` to 1 because the AND result of every
number with `(1<<16) - 1` is the number itself.

Time complexity:  
`O(3 * 2^16 * n)`

Space:  
`O(2^16)`

    
    
    class Solution {
        public int countTriplets(int[] A) {
            int N = 1 << 16;
            int[] dp = new int[N];
            dp[N - 1] = 1;
            for (int i = 0; i < 3; i++) {
                int[] temp = new int[N];
                for (int k = 0; k < N; k++) {
                    for (int a : A) {
                        temp[k & a] += dp[k];
                    }
                }
                dp = temp;
            }
            return dp[0];
        }
    }
    


### Solution 2
First, we process all tuples `A[i]` and `A[j]`, and store the result of `A[i]
& A[j]` in the hash map `tuples`. We also count there how many times each
result was produced.

Then, we go through the array again, and check each element against all
tuples. If it produces zero, we add the tuple counter to the result.

    
    
    int countTriplets(vector<int>& A, int cnt = 0) {
      unordered_map<int, int> tuples;
      for (auto a : A)
        for (auto b : A) ++tuples[a & b];
      for (auto a : A)
        for (auto t : tuples)
          if ((t.first & a) == 0) cnt += t.second;
      return cnt;
    }
    

If we know that the input is large, we could use an array instead of hash set.
The size of this array will be `1 << 16`. This will speed up things quite a
bit.

    
    
    int countTriplets(vector<int>& A, int cnt = 0) {
      int tuples[1 << 16] = {};
      for (auto a : A)
        for (auto b : A) ++tuples[a & b];
      for (auto a : A)
        for (auto i = 0; i < (1 << 16); ++i)
          if ((i & a) == 0) cnt += tuples[i];
      return cnt;
    }
    

## Complexity Analysis

Time: _O(n * n)_. We can have 2^16 tuples at most. When n is large, n * n will
dominate n * 2^16. So it is O(n * n) in the big O notation.  
Memory: _O(n)_ for the first solution; _O(2 ^ 16)_ , or, stricter, _O(1)_ for
the second one.


### Solution 3
For Chinese speakers, you can go to the following websites for details.  
<https://www.acwing.com/solution/leetcode/content/876/>

  1. 首先枚举 i 和 j，同时使用一个数组 f 记录 A[i] & A[j] 的值所对应的答案个数，即有多少个数字能使得 A[i] & A[j] & x == 0。First, we enumerate i and j, and use a vector f to save the number of solutions corresponding to A[i] & A[j], i.e. the number of x such that A[i] & A[j] & x == 0.
  2. 枚举时，如果对应的 A[i] & A[j] 还没有记录，则暴力枚举 k，求出后将其添加到 f 中。如果有记录，则不再枚举，直接使用这个记录的值。 When enumerating, if there is no record for A[i] & A[j], we will use brute force to compute the number of k, and then save it into the vector f. Otherwise, we can use this record directly.

    
    
    class Solution {
    public:
        int countTriplets(vector<int>& A) {
            int n = A.size(), ans = 0;
            vector<int> f(1 << 16, -1);
    
            for (int i = 0; i < n; i++)
                for (int j = 0; j < n; j++) {
                    int x = A[i] & A[j];
                    if (f[x] == -1) {
                        f[x] = 0;
                        for (int k = 0; k < n; k++)
                            if ((x & A[k]) == 0)
                                f[x]++;
                    }
                    ans += f[x];
                }
    
            return ans;
        }
    };
    

For Chinese speakers, you can find the DP solution in the following site.  
本题动态规划的解法可以在 <https://www.acwing.com/solution/leetcode/content/876/> 中找到。

For Chinese speakers, you may want to explore more solutions in the Acwing
website.  
更多精彩题解，尽在 <https://www.acwing.com/solution/leetcode/>



