---
layout: post
title: 152. Maximum Product Subarray
---
### Question
Given an integer array `nums`, find the contiguous subarray within an array
(containing at least one number) which has the largest product.

 **Example 1:**

    
    
     **Input:** [2,3,-2,4]
    **Output:** 6
    **Explanation:**  [2,3] has the largest product 6.
    

**Example 2:**

    
    
    **Input:** [-2,0,-1]
    **Output:** 0
    **Explanation:**  The result cannot be 2, because [-2,-1] is not a subarray.

### Solution 1
    
    
     int maxProduct(int A[], int n) {
        // store the result that is the max we have found so far
        int r = A[0];
    
        // imax/imin stores the max/min product of
        // subarray that ends with the current number A[i]
        for (int i = 1, imax = r, imin = r; i < n; i++) {
            // multiplied by a negative makes big number smaller, small number bigger
            // so we redefine the extremums by swapping them
            if (A[i] < 0)
                swap(imax, imin);
    
            // max/min product for the current number is either the current number itself
            // or the max/min by the previous number times the current one
            imax = max(A[i], imax * A[i]);
            imin = min(A[i], imin * A[i]);
    
            // the newly computed max value is a candidate for our global result
            r = max(r, imax);
        }
        return r;
    }
    


### Solution 2
    
    
    public int maxProduct(int[] A) {
        if (A.length == 0) {
            return 0;
        }
        
        int maxherepre = A[0];
        int minherepre = A[0];
        int maxsofar = A[0];
        int maxhere, minhere;
        
        for (int i = 1; i < A.length; i++) {
            maxhere = Math.max(Math.max(maxherepre * A[i], minherepre * A[i]), A[i]);
            minhere = Math.min(Math.min(maxherepre * A[i], minherepre * A[i]), A[i]);
            maxsofar = Math.max(maxhere, maxsofar);
            maxherepre = maxhere;
            minherepre = minhere;
        }
        return maxsofar;
    }
    

Note:  
There's no need to use O(n) space, as all that you need is a minhere and
maxhere. (local max and local min), then you can get maxsofar (which is global
max) from them.

There's a chapter in Programming Pearls 2 that discussed the MaxSubArray
problem, the idea is similar.


### Solution 3
Loop through the array, each time remember the max and min value for the
previous product, the most important thing is to update the max and min value:
we have to compare among max * A[i], min * A[i] as well as A[i], since this is
product, a negative * negative could be positive.

    
    
    public class Solution {
        public int maxProduct(int[] A) {
            if (A == null || A.length == 0) {
                return 0;
            }
            int max = A[0], min = A[0], result = A[0];
            for (int i = 1; i < A.length; i++) {
                int temp = max;
                max = Math.max(Math.max(max * A[i], min * A[i]), A[i]);
                min = Math.min(Math.min(temp * A[i], min * A[i]), A[i]);
                if (max > result) {
                    result = max;
                }
            }
            return result;
        }
    }



