---
layout: post
title: 1000. Delete Columns to Make Sorted III
---
### Question
We are given an array `A` of `N` lowercase letter strings, all of the same
length.

Now, we may choose any set of deletion indices, and for each string, we delete
all the characters in those indices.

For example, if we have an array `A = ["babca","bbazb"]` and deletion indices
`{0, 1, 4}`, then the final array after deletions is `["bc","az"]`.

Suppose we chose a set of deletion indices `D` such that after deletions, the
final array has **every element (row) in  lexicographic** order.

For clarity, `A[0]` is in lexicographic order (ie. `A[0][0] <= A[0][1] <= ...
<= A[0][A[0].length - 1]`), `A[1]` is in lexicographic order (ie. `A[1][0] <=
A[1][1] <= ... <= A[1][A[1].length - 1]`), and so on.

Return the minimum possible value of `D.length`.



 **Example 1:**

    
    
     **Input:** ["babca","bbazb"]
    **Output:** 3
    **Explanation:** After deleting columns 0, 1, and 4, the final array is A = ["bc", "az"].
    Both these rows are individually in lexicographic order (ie. A[0][0] <= A[0][1] and A[1][0] <= A[1][1]).
    Note that A[0] > A[1] - the array A isn't necessarily in lexicographic order.
    

**Example 2:**

    
    
    **Input:** ["edcba"]
    **Output:** 4
    **Explanation:** If we delete less than 4 columns, the only row won't be lexicographically sorted.
    

**Example 3:**

    
    
    **Input:** ["ghi","def","abc"]
    **Output:** 0
    **Explanation:** All rows are already lexicographically sorted.
    



 **Note:**

  1. `1 <= A.length <= 100`
  2. `1 <= A[i].length <= 100`

### Solution 1
**Intuition**

Take `n` cols as `n` elements, so we have an array of `n` elements.  
=> The final array has every row in lexicographic order.  
=> The elements in final state is in increasing order.  
=> The final elements is a sub sequence of initial array.  
=> Transform the problem to find the maximum increasing sub sequence.

  

 **Explanation**  
Now let's talking about how to find maximum increasing subsequence.  
Here we apply a O(N^2) dp solution.

`dp[i]` means the longest subsequence ends with `i`-th element.  
For all `j < i`, if `A[][j] < A[][i]`, then `dp[j] = max(dp[j], dp[i] + 1)`

  

 **Time Complexity** :  
`O(N^2)` to find increasing subsequence  
`O(M)` for comparing elements.  
So Overall `O(MN^2)`.

  

 **C++:**

    
    
        int minDeletionSize(vector<string>& A) {
            int m = A. size(), n = A[0].length(), res = n - 1, k;
            vector<int>dp(n,1);
            for (int j = 0; j < n; ++j) {
                for (int i = 0; i < j; ++i) {
                    for (k = 0; k < m; ++k) {
                        if (A[k][i] > A[k][j]) break;
                    }
                    if (k == m && dp[i] + 1 > dp[j])
                        dp[j] = dp[i] + 1;
                }
                res = min(res, n - dp[j]);
            }
            return res;
        }
    

**Java:**

    
    
        public int minDeletionSize(String[] A) {
            int m = A. length, n = A[0].length(), res = n - 1, k;
            int[] dp = new int[n];
            Arrays.fill(dp, 1);
            for (int j = 0; j < n; ++j) {
                for (int i = 0; i < j; ++i) {
                    for (k = 0; k < m; ++k) {
                        if (A[k].charAt(i) > A[k].charAt(j)) break;
                    }
                    if (k == m && dp[i] + 1 > dp[j])
                        dp[j] = dp[i] + 1;
                }
                res = Math.min(res, n - dp[j]);
            }
            return res;
        }
    

**Python:**

    
    
        def minDeletionSize(self, A):
            n = len(A)
            dp = [ 1] * n
            for j in xrange(1, n):
                for i in xrange(j):
                    if all(a[i] <= a[j] for a in A):
                        dp[j] = max(dp[j], dp[i] + 1)
            return n - max(dp)
    


### Solution 2
A twist of the [300\. Longest Increasing
Subsequence](https://leetcode.com/problems/longest-increasing-
subsequence/description/) problem.

For each position `i`,we track the maximum increasing subsequence. To do that,
we analyze all `j < i`, and if `A[j] < A[i]` for _all strings_ , then `dp[i] =
dp[j] + 1`.The runtime complexity is O(n * n * m), where n is the number of
characters, and m is the number of strings.

Unlike #300, we cannot use the binary search approach here because there is no
stable comparasion (e.g. `["ba", "ab"]` are both "less" than each other).

    
    
    int minDeletionSize(vector<string>& A) {
      vector<int> dp(A[0].size(), 1);
      for (auto i = 0; i < A[0].size(); ++i) {
        for (auto j = 0; j < i; ++j)
          for (auto k = 0; k <= A.size(); ++k) {
            if (k == A.size()) dp[i] = max(dp[i], dp[j] + 1);
            else if (A[k][j] > A[k][i]) break;
          }
      }
      return A[0].size() - *max_element(begin(dp), end(dp));
    }
    


### Solution 3
for A = ["babca","bbazb"]  
first we conver it to  
`b,b` line 1  
`a,b` line 2  
`b,a` line 3  
`c,z` line 4  
`a,b` line 5

whether we should left `line 1` or not is determined by which will get smaller
`D.length`.  
if we do not delete this line, then the next line's element should all be
larger than this line, otherwise we must delete next line

we define function `solve(A, index, start)` means the `D.length` if we use
`start` as the first element in `A[index:]`.  
`index` means the line which we are considering  
`start` is a list, represent the last line we choose left.

so if `any(A[index][i] < start[i])`, we delete this line, and analyse solve(A,
index+1, start)

otherwise, if `all(A[index][i] >= start[i])`, we choose the min D.length in
case `left A[index]` and case `delete A[index]`, e.g.
`min(solve(A,index+1,tuple(A[index])), 1+solve(A,index+1, start))`

cause there are many double count, so I use `tuple format` to represent
`start`

    
    
    class Solution(object):
        def minDeletionSize(self, A):
            cache = {}
            def solve(A, index, start):
                if index >= len(A): return 0
                if (index, start) in cache: return cache[index,start]
                
                if any(A[index][j] < start[j] for j in range(len(A[index]))):
                    cache[index,start] = 1 + solve(A, index+1, start)
                    return cache[index,start]
                
                cache[index,start] = min(solve(A,index+1,tuple(A[index])), 1+solve(A,index+1, start))
                return cache[index,start]
            
            return solve(list(zip(*A)), 0, tuple(['a']*len(A)))
    



