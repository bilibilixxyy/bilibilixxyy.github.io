---
layout: post
title: 1020. Longest Turbulent Subarray
---
### Question
A subarray `A[i], A[i+1], ..., A[j]` of `A` is said to be _turbulent_ if and
only if:

  * For `i <= k < j`, `A[k] > A[k+1]` when `k` is odd, and `A[k] < A[k+1]` when `k` is even;
  * **OR** , for `i <= k < j`, `A[k] > A[k+1]` when `k` is even, and `A[k] < A[k+1]` when `k` is odd.

That is, the subarray is turbulent if the comparison sign flips between each
adjacent pair of elements in the subarray.

Return the **length** of a  maximum size turbulent subarray of A.



 **Example 1:**

    
    
     **Input:** [9,4,2,10,7,8,8,1,9]
    **Output:** 5
    **Explanation:** (A[1] > A[2] < A[3] > A[4] < A[5])
    

**Example 2:**

    
    
    **Input:** [4,8,12,16]
    **Output:** 2
    

**Example 3:**

    
    
    **Input:** [100]
    **Output:** 1
    



 **Note:**

  1. `1 <= A.length <= 40000`
  2. `0 <= A[i] <= 10^9`

### Solution 1
For each `A[i]`  
`inc`: The length of current valid sequence which ends with two **increasing**
numbers  
`dec`: The length of current valid sequence which ends with two **decreasing**
numbers

    
    
     class Solution {
        public int maxTurbulenceSize(int[] A) {
            int inc = 1, dec = 1, result = 1;
            for (int i = 1; i < A.length; i++) {
                if (A[i] < A[i - 1]) {
                    dec = inc + 1;
                    inc = 1;
                } else if (A[i] > A[i - 1]) {
                    inc = dec + 1;
                    dec = 1;
                } else {
                    inc = 1;
                    dec = 1;
                }
                result = Math.max(result, Math.max(dec, inc));
            }
            return result;
        }
    }
    


### Solution 2
We use the counter (`cnt`) to track subarray size. Our counter is positive if
we expect '>', and '<' otherwise. Obviously, for each turn we flip the sign.

If the comparison matches the counter sign (e.g. `A[i] > A[i + 1]` and `cnt >
0` ), we increment (or decrement the negative) counter. Otherwise, we reset
the counter to 1 (or -1). One edge case here is when two numbers are equal. We
set the counter to zero in this case.

    
    
    int maxTurbulenceSize(vector<int>& A, int res = 0) {
      for (auto i = 0, cnt = 0; i + 1 < A.size(); ++i, cnt *= -1) {
        if (A[i] > A[i + 1]) cnt = cnt > 0 ? cnt + 1 : 1;
        else if (A[i] < A[i + 1])  cnt = cnt < 0 ? cnt - 1 : -1;
        else cnt = 0;
        res = max(res, abs(cnt));
      }
      return res + 1;
    }
    

Java version:

    
    
    public int maxTurbulenceSize(int[] A) {
      int res = 0;
      for (int i = 0, cnt = 0; i + 1 < A.length; ++i, cnt *= -1) {
        if (A[i] > A[i + 1]) cnt = cnt > 0 ? cnt + 1 : 1;
        else if (A[i] < A[i + 1])  cnt = cnt < 0 ? cnt - 1 : -1;
        else cnt = 0;
        res = Math.max(res, Math.abs(cnt));
      }
      return res + 1;
    }
    


### Solution 3
A subarray is turbulent if the comparison sign alternates between consecutive
elements (ex. `nums[0] < nums[1] > nums[2] < nums[3] > ...` ). Looking at the
structure of the array, this means every element of a turbulent subarray must
belong to either a peak `(A[i-2] < A[i-1] > A[i])` or a valley `(A[i-2] >
A[i-1] < A[i])` structure.

The algorithm works as follows. Keep track of the length of the longest run
ending at index `i`. This is tracked in a variable named `clen`. If the last
three elements form a peak or a valley, we can extend the previous run length
by 1 (meaning `clen += 1`). Otherwise, we can no longer extend this run and
need to reset `clen` to the length of the longest run ending at index `i`.
This run length will be `1` if the previous and current elements are the same
(Ex: `[2,2,2]`), or `2` if the previous and current elements differ (Ex:
`[2,4,6]`). The answer is the length of the best run found.

## Python

    
    
    def maxTurbulenceSize(self, A):
        best = clen = 0
    
        for i in range(len(A)):
            if i >= 2 and (A[i-2] > A[i-1] < A[i] or A[i-2] < A[i-1] > A[i]):
                clen += 1
            elif i >= 1 and A[i-1] != A[i]:
                clen = 2
            else:
                clen = 1
            best = max(best, clen)
        return best
    

## Java

    
    
    public int maxTurbulenceSize(int[] A) {
        int best = 0, clen = 0;
    
        for(int i = 0; i < A.length; i++) {
            if(i >= 2 && ((A[i-2] > A[i-1] && A[i-1] < A[i]) ||
                          (A[i-2] < A[i-1] && A[i-1] > A[i])) ) {
                // If the last three elements are turbulent, increment run length by 1.
                clen++;
            } else if(i >= 1 && A[i-1] != A[i]) {
                // The last three elements are not turbulent, so we'll reset the run length.
                // If the previous and current elements are not equal, the new run length is 2.
                clen = 2;
            } else {
                // Otherwise, the new run length is 1.
                clen = 1;
            }
            best = Math.max(best, clen);
        }
        return best;    
    }
    

## C++

    
    
    int maxTurbulenceSize(vector<int>& A) {
        int best = 0, clen = 0;
    
        for(int i = 0; i < A.size(); ++i) {
            if (i >= 2 && ((A[i-2] > A[i-1] && A[i-1] < A[i]) ||
                           (A[i-2] < A[i-1] && A[i-1] > A[i])) ) {
                clen++;
            } else if (i >= 1 && A[i-1] != A[i]) {
                clen = 2;
            } else {
                clen = 1;
            }
            best = max(best, clen);
        }
        return best;
    }
    



