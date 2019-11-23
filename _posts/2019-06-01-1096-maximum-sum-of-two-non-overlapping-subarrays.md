---
layout: post
title: 1096. Maximum Sum of Two Non-Overlapping Subarrays
---
### Question
Given an array `A` of non-negative integers, return the maximum sum of
elements in two non-overlapping (contiguous) subarrays, which have lengths `L`
and `M`.  (For clarification, the `L`-length subarray could occur before or
after the `M`-length subarray.)

Formally, return the largest `V` for which `V = (A[i] + A[i+1] + ... +
A[i+L-1]) + (A[j] + A[j+1] + ... + A[j+M-1])` and either:

  * `0 <= i < i + L - 1 < j < j + M - 1 < A.length`, **or**
  * `0 <= j < j + M - 1 < i < i + L - 1 < A.length`.



 **Example 1:**

    
    
     **Input:** A = [0,6,5,2,2,5,1,9,4], L = 1, M = 2
    **Output:** 20
    **Explanation:** One choice of subarrays is [9] with length 1, and [6,5] with length 2.
    

**Example 2:**

    
    
    **Input:** A = [3,8,1,3,2,1,8,9,0], L = 3, M = 2
    **Output:** 29
    **Explanation:** One choice of subarrays is [3,8,1] with length 3, and [8,9] with length 2.
    

**Example 3:**

    
    
    **Input:** A = [2,1,5,6,0,9,5,0,3,8], L = 4, M = 3
    **Output:** 31
    **Explanation:** One choice of subarrays is [5,6,0,9] with length 4, and [3,8] with length 3.
    



 **Note:**

  1. `L >= 1`
  2. `M >= 1`
  3. `L + M <= A.length <= 1000`
  4. `0 <= A[i] <= 1000`

### Solution 1
## **Explanation**

`Lsum`, sum of the last `L` elements  
`Msum`, sum of the last `M` elements

`Lmax`, max sum of contiguous `L` elements before the last `M` elements.  
`Mmax`, max sum of contiguous `M` elements before the last `L` elements/

##  **Complexity**

Two pass, `O(N)` time,  
`O(1)` extra space.

It can be done in one pass. I just don't feel like merging them.  
If you don't like change the original input, don't have to.

  

 **Java:**

    
    
        public int maxSumTwoNoOverlap(int[] A, int L, int M) {
             for (int i = 1; i < A.length; ++i)
                A[i] += A[i - 1];
            int res = A[L + M - 1], Lmax = A[L - 1], Mmax = A[M - 1];
            for (int i = L + M; i < A.length; ++i) {
                Lmax = Math.max(Lmax, A[i - M] - A[i - L - M]);
                Mmax = Math.max(Mmax, A[i - L] - A[i - L - M]);
                res = Math.max(res, Math.max(Lmax + A[i] - A[i - M], Mmax + A[i] - A[i - L]));
            }
            return res;
        }
    

**Another Java**

    
    
        public int maxSumTwoNoOverlap(int[] A, int L, int M) {
            int res =  0, Lsum = 0, Lmax = 0, Msum = 0, Mmax = 0;
            for (int i = 0; i < A.length; ++i) {
                Msum += A[i];
                if (i - M >= 0) Msum -= A[i - M];
                if (i - M >= 0) Lsum += A[i - M];
                if (i - M - L >= 0) Lsum -= A[i - L - M];
                Lmax = Math.max(Lmax, Lsum);
                res = Math.max(res, Lmax + Msum);
            }
            Lsum = Lmax = Msum = Mmax = 0;
            for (int i = 0; i < A.length; ++i) {
                Lsum += A[i];
                if (i - L >= 0) Lsum -= A[i - L];
                if (i - L >= 0) Msum += A[i - L];
                if (i - M - L >= 0) Msum -= A[i - L - M];
                Mmax = Math.max(Mmax, Msum);
                res = Math.max(res, Mmax + Lsum);
            }
            return res;
        }
    }
    

**C++:**

    
    
        int maxSumTwoNoOverlap(vector<int>& A, int L, int M) {
             for (int i = 1; i < A.size(); ++i)
                A[i] += A[i - 1];
            int res = A[L + M - 1], Lmax = A[L - 1], Mmax = A[M - 1];
            for (int i = L + M; i < A.size(); ++i) {
                Lmax = max(Lmax, A[i - M] - A[i - L - M]);
                Mmax = max(Mmax, A[i - L] - A[i - L - M]);
                res = max(res, max(Lmax + A[i] - A[i - M], Mmax + A[i] - A[i - L]));
            }
            return res;
        }
    

**Python:**

    
    
        def maxSumTwoNoOverlap(self, A, L, M):
             for i in xrange(1, len(A)):
                A[i] += A[i - 1]
            res, Lmax, Mmax = A[L + M - 1], A[L - 1], A[M - 1]
            for i in xrange(L + M, len(A)):
                Lmax = max(Lmax, A[i - M] - A[i - L - M])
                Mmax = max(Mmax, A[i - L] - A[i - L - M])
                res = max(res, Lmax + A[i] - A[i - M], Mmax + A[i] - A[i - L])
            return res
    


### Solution 2
Similar to [Best Time to Buy and Sell Stock
III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/), but
instead of maximum profit, we track maximum sum of `N` elements.

Left-to-right, track the maximum sum of `L` elements in `left`. Right-to-left,
track the maximum sum of `M` elements in `right`.

Then, find the split point where `left[i] + right[i]` gives us the maximum
sum.

 **Note:** we need to do it twice for (L, M) and (M, L).

    
    
     int maxTwoNoOverlap(vector<int>& A, int L, int M, int sz, int res = 0) {
      vector<int> left(sz + 1), right(sz + 1);
      for (int i = 0, j = sz - 1, s_r = 0, s_l = 0; i < sz; ++i, --j) {
        s_l += A[i], s_r += A[j];
        left[i + 1] = max(left[i], s_l);
        right[j] = max(right[j + 1], s_r);
        if (i + 1 >= L) s_l -= A[i + 1 - L];
        if (i + 1 >= M) s_r -= A[j + M - 1];
      }
      for (auto i = 0; i < A.size(); ++i) {
        res = max(res, left[i] + right[i]);
      }
      return res;
    }
    int maxSumTwoNoOverlap(vector<int>& A, int L, int M) {
      return max(maxTwoNoOverlap(A, L, M, A.size()), maxTwoNoOverlap(A, M, L, A.size()));
    }
    


### Solution 3
  1. Scan the prefix sum array from index `L + M`, which is the first possible position;
  2. update the max value of the `L-length` subarray; then update max value of the sum of the both;
  3. we need to swap `L` and `M` to scan twice, since either subarray can occur before the other.
  4. In private method, prefix sum difference `p[i - M] - p[i - M - L]` is `L-length` subarray from index `i - M - L` to `i - M - 1`, and `p[i] - p[i - M]` is `M-length` subarray from index `i - M` to `i - 1`.

 **Solution 1:**

    
    
        public  int maxSumTwoNoOverlap(int[] A, int L, int M) {
            int[] prefixSum = new int[A.length + 1];
            for (int i = 0; i < A.length; ++i) {
                prefixSum[i + 1] = prefixSum[i] + A[i];
            }
            return Math.max(maxSum(prefixSum, L, M), maxSum(prefixSum, M, L));
        }
        private int maxSum(int[] p, int L, int M) {
            int ans = 0;
            for (int i = L + M, maxL = 0; i < p.length; ++i) {
                maxL = Math.max(maxL, p[i - M] - p[i - M - L]); // update max of L-length subarray.
                ans = Math.max(ans, maxL + p[i] - p[i - M]); // update max of the sum of L-length & M-length subarrays.
            }
            return ans;
        }
    

**Analysis:**

Time & space: `O(n)`, where `n = A.length`.

**Solution 2:**

Based on **Solution 1** , we can further get rid of prefix sum array to
develop the following `O(1)` code.

**Unfortunately, the boundary conditions are headache and fallible, please let
me know if you can improve the readability, or at least make it concise.**

    
    
         public int maxSumTwoNoOverlap(int[] A, int L, int M) {
            return Math.max(maxSum(A, L, M), maxSum(A, M, L));
        }
        private int maxSum(int[] A, int L, int M) {
            int ans = 0; 
            for (int i = 0, maxL = 0, sumL = 0, sumM = 0; i < A.length; ++i) {
                if (i < L || i >= L + M) { sumL += i < L ? A[i] : A[i - M]; } // first L-length subarray at index [0...L - 1], no update between index [L...L + M - 1].
                if (i >= L) { sumM += A[i]; } // first M-length subarray starts from index L to L + M - 1.
                if (i >= L + M) { sumL -= A[i - L - M]; } // deduct first item from current L-length subarray.
                if (i >= L + M) { sumM -= A[i - M]; } // deduct first item from current  M-length subarray.
                if (i >= L + M - 1) { maxL = Math.max(maxL, sumL); } // update max of L-length subarray.
                if (i >= L + M - 1) { ans = Math.max(ans, maxL + sumM); } // update max of L-length & M-length subarrays.
            }
            return ans;
        }
    

**Analysis:**

Time: `O(n)`, space: `O(1)`, where `n = A.length`.



