---
layout: post
title: 905. Length of Longest Fibonacci Subsequence
---
### Question
A sequence `X_1, X_2, ..., X_n` is _fibonacci-like_ if:

  * `n >= 3`
  * `X_i + X_{i+1} = X_{i+2}` for all `i + 2 <= n`

Given a **strictly increasing**  array `A` of positive integers forming a
sequence, find the **length** of the longest fibonacci-like subsequence of
`A`.  If one does not exist, return 0.

( _Recall that a subsequence is derived from another sequence`A` by deleting
any number of elements (including none) from `A`, without changing the order
of the remaining elements.  For example, `[3, 5, 8]` is a subsequence of `[3,
4, 5, 6, 7, 8]`._ )



 **Example 1:**

    
    
     **Input:** [1,2,3,4,5,6,7,8]
    **Output:** 5
    **Explanation:** The longest subsequence that is fibonacci-like: [1,2,3,5,8].
    

**Example 2:**

    
    
    **Input:** [1,3,7,11,12,14,18]
    **Output:** 3
    **Explanation** :
    The longest subsequence that is fibonacci-like:
    [1,11,12], [3,11,14] or [7,11,18].
    



 **Note:**

  * `3 <= A.length <= 1000`
  * `1 <= A[0] < A[1] < ... < A[A.length - 1] <= 10^9`
  * _(The time limit has been reduced by 50% for submissions in Java, C, and C++.)_

### Solution 1
# Solution 1

Save array `A` to a hash set `s`.  
Start from base (A[i], A[j]) as the first two element in the sequence,  
we try to find the Fibonacci like subsequence as long as possible,

Initial `(a, b) = (A[i], A[j])`  
While the set `s` contains `a + b`, we update `(a, b) = (b, a + b)`.  
In the end we update the longest length we find.

 **Time Complexity** :  
`O(N^2logM)`, where `M` is the `max(A)`.

Quote from @renato4:  
Just clarifying a little bit more.  
Since the values grow exponentially,  
the amount of numbers needed to accommodate a sequence  
that ends in a number `M` is at most `log(M)`.

 **C++:**

    
    
         int lenLongestFibSubseq(vector<int>& A) {
            unordered_set<int> s(A.begin(), A.end());
            int res = 0;
            for (int i = 0; i < A.size(); ++i) {
                for (int j = i + 1; j < A.size(); ++j) {
                    int  a = A[i], b = A[j], l = 2;
                    while (s.count(a + b))
                        b = a + b, a = b - a, l++;
                    res = max(res, l);
                }
            }
            return res > 2 ? res : 0;
        }
    

**Java:**

    
    
         public int lenLongestFibSubseq(int[] A) {
            Set<Integer> s = new HashSet<Integer>();
            for (int x : A) s.add(x);
            int res = 2;
            for (int i = 0; i < A.length; ++i)
                for (int j = i + 1; j < A.length; ++j) {
                    int a = A[i], b = A[j], l = 2;
                    while (s.contains(a + b)) {
                        b = a + b;
                        a = b - a;
                        l++;
                    }
                    res = Math.max(res, l);
                }
            return res > 2 ? res : 0;
        }
    

**Python:**

    
    
        def lenLongestFibSubseq(self, A):
            s =  set(A)
            res = 2
            for i in range(len(A)):
                for j in range(i + 1, len(A)):
                    a, b, l = A[i], A[j], 2
                    while a + b in s:
                        a, b, l = b, a + b, l + 1
                    res = max(res, l)
            return res if res > 2 else 0
    

  

## Solution 2

Another solution is kind of dp.  
`dp[a, b]` represents the length of fibo sequence ends up with `(a, b)`  
Then we have `dp[a, b] = (dp[b - a, a] + 1 ) or 2`  
The complexity reduce to `O(N^2)`.  
In C++/Java, I use 2D dp and index as key.  
In Python, I use value as key.

**Time Complexity** :  
`O(N^2)`

**C++**

    
    
        int lenLongestFibSubseq(vector<int>& A) {
            unordered_map<int, int> m;
            int N = A. size(), res = 0;
            int dp[N][N];
            for (int j = 0; j < N; ++j) {
                m[A[j]] = j;
                for (int i = 0; i < j; ++i) {
                    int k = m.find(A[j] - A[i]) == m.end() ? -1 : m[A[j] - A[i]];
                    dp[i][j] = (A[j] - A[i] < A[i] && k >= 0) ? dp[k][i] + 1 : 2;
                    res = max(res, dp[i][j]);
                }
            }
            return res > 2 ? res : 0;
        }
    

**Java**

    
    
        public int lenLongestFibSubseq(int[] A) {
            int res =  0;
            int[][] dp = new int[A.length][A.length];
            Map<Integer, Integer> index = new HashMap<>();
            for (int j = 0; j < A.length; j++) {
                index.put(A[j], j);
                for (int i = 0; i < j; i++) {
                    int k = index.getOrDefault(A[j] - A[i], -1);
                    dp[i][j] = (A[j] - A[i] < A[i] && k >= 0) ? dp[k][i] + 1 : 2;
                    res = Math.max(res, dp[i][j]);
                }
            }
            return res > 2 ? res : 0;
        }
    

**Python**

    
    
        def lenLongestFibSubseq(self, A):
            dp = collections.defaultdict(int)
            s = set(A)
             for j in xrange(len(A)):
                for i in xrange(j):
                    if A[j] - A[i] < A[i] and A[j] - A[i] in s:
                        dp[A[i], A[j]] = dp.get((A[j] - A[i], A[i]), 2) + 1
            return max(dp.values() or [0])
    


### Solution 2
`dp[i][j]` represents the length of longest sequence which ends with `A[i]`
and `A[j]`.

    
    
    class Solution {
        public int lenLongestFibSubseq(int[] A) {
            int n = A.length;
            int[][] dp = new int[n][n];
            Map<Integer, Integer> pos = new HashMap<>();
            for (int i = 0; i < n; i++) {
                pos.put(A[i], i);
                for (int j = i; j < n; j++) {
                    dp[i][j] = 2;
                }
            }
            for (int j = 2; j < n; j++) {
                for (int i = j - 1; i > 0; i--) {
                    int prev = A[j] - A[i];
                    if (prev >= A[i]) {
                        break;
                    }
                    if (!pos.containsKey(prev)) {
                        continue;
                    }
                    dp[i][j] = dp[pos.get(prev)][i] + 1;
                }
            }
            int result = 0;
            for (int j = 2; j < n; j++) {
                for (int i = 1; i < n - 1; i++) {
                    if (dp[i][j] > 2) {
                        result = Math.max(result, dp[i][j]);
                    }
                }
            }
            return result;
        }
    }
    


### Solution 3
[https://www.youtube.com/watch?v=y7vN4ODhqHo&t=19s](https://www.youtube.com/watch?v=y7vN4ODhqHo&t=19s)

Hope it's helpful.



