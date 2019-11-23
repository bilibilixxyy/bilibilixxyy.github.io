---
layout: post
title: 811. Number of Subarrays with Bounded Maximum
---
### Question
We are given an array `A` of positive integers, and two positive integers `L`
and `R` (`L <= R`).

Return the number of (contiguous, non-empty) subarrays such that the value of
the maximum array element in that subarray is at least `L` and at most `R`.

    
    
     **Example :**
    **Input:** 
    A = [2, 1, 4, 3]
    L = 2
    R = 3
    **Output:** 3
    **Explanation:** There are three subarrays that meet the requirements: [2], [2, 1], [3].
    

**Note:**

  * L, R  and `A[i]` will be an integer in the range `[0, 10^9]`.
  * The length of `A` will be in the range of `[1, 50000]`.

### Solution 1
    
    
     class Solution {
        public int numSubarrayBoundedMax(int[] A, int L, int R) {
            int j=0,count=0,res=0;
            
            for(int i=0;i<A.length;i++){
                if(A[i]>=L && A[i]<=R){
                    res+=i-j+1;count=i-j+1;
                }
                else if(A[i]<L){
                    res+=count;
                }
                else{
                    j=i+1;
                    count=0;
                }
            }
            return res;
        }
    }
    


### Solution 2
Suppose **dp[i]** denotes the max number of valid sub-array ending with
**A[i]**. We use following example to illustrate the idea:

 _A = [2, 1, 4, 2, 3], L = 2, R = 3_

  1. if A[i] < L

For example, i = 1. We can only append A[i] to a valid sub-array ending with
A[i-1] to create new sub-array. So we have **dp[i] = dp[i-1] (for i > 0)**

  2. if A[i] > R:

For example, i = 2. No valid sub-array ending with A[i] exist. So we have
**dp[i] = 0**.  
We also record the position of the invalid number 4 here as **prev**.

  3. if L <= A[i] <= R

For example, i = 4. In this case any sub-array starts after the previous
invalid number to A[i] (A[prev+1..i], A[prev+2..i]) is a new valid sub-array.
So **dp[i] += i - prev**

Finally the sum of the dp array is the solution. Meanwhile, notice dp[i] only
relies on dp[i-1] (and also **prev** ), we can reduce the space complexity to
O(1)

    
    
    class Solution(object):
        def numSubarrayBoundedMax(self, A, L, R):
            """
            :type A: List[int]
            :type L: int
            :type R: int
            :rtype: int
            """
            res, dp = 0, 0
            prev = -1
            for i in range(len(A)):
                if A[i] < L and i > 0:
                    res += dp
                if A[i] > R:
                    dp = 0
                    prev = i
                if L <= A[i] <= R:
                    dp = i - prev
                    res += dp
            return res
    


### Solution 3
    
    
    class Solution {
    public:
        int numSubarrayBoundedMax(vector<int>& A, int L, int R) {
            int result=0, left=-1, right=-1;
            for (int i=0; i<A.size(); i++) {
                if (A[i]>R) left=i;
                if (A[i]>=L) right=i;
                result+=right-left;
            }
            return result;
        }
    };
    



