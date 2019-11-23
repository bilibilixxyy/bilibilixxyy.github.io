---
layout: post
title: 643. Maximum Average Subarray I
---
### Question
Given an array consisting of `n` integers, find the contiguous subarray of
given length `k` that has the maximum average value. And you need to output
the maximum average value.

 **Example 1:**

    
    
     **Input:** [1,12,-5,-6,50,3], k = 4
    **Output:** 12.75
    **Explanation:** Maximum average is (12-5-6+50)/4 = 51/4 = 12.75
    



 **Note:**

  1. 1 <= `k` <= `n` <= 30,000.
  2. Elements of the given array will be in the range [-10,000, 10,000].

### Solution 1
    
    
    public class Solution {
        public double findMaxAverage(int[] nums, int k) {
            long sum = 0;
            for (int i = 0; i < k; i++) sum += nums[i];
            long max = sum;
            
            for (int i = k; i < nums.length; i++) {
                sum += nums[i] - nums[i - k];
                max = Math.max(max, sum);
            }
            
            return max / 1.0 / k;
        }
    }
    


### Solution 2
We want to find the maximum K-length sum. After, we can divide by K to get the
average.  
We have two techniques for getting these sums efficiently: prefix sums, or
sliding window.

In the first approach, we calculate `P[i] = A[0] + A[1] + ... + A[i-1]` in
linear time. Then, `A[i] + A[i+1] + ... + A[i+K-1] = P[i+K] - P[i]`, and we
should find the max of these.

    
    
    def findMaxAverage(self, A, K):
        P = [0]
        for x in A:
            P.append(P[-1] + x)
    
        ma = max(P[i+K] - P[i] 
                 for i in xrange(len(A) - K + 1))
        return ma / float(K)
    

* * *

In the second approach, we maintain `su = the sum of A[i-K+1] + A[i-K+2] + ...
+ A[i]`. Then, when we have K elements in this sum (`if i >= K-1`), it is a
candidate to be the maximum sum `ma`.

    
    
    def findMaxAverage(self, A, K):
        su = 0
        ma = float('-inf')
        for i, x in enumerate(A):
            su += x
            if i >= K:
                su -= A[i-K]
            if i >= K - 1:
                ma = max(ma, su)
        return ma / float(K)
    


### Solution 3
Using prefix sums (where `sums[i]` is the sum of the first `i` numbers) to
compute subarray sums.

    
    
    def findMaxAverage(self, nums, k):
        sums = [0] + list(itertools.accumulate(nums))
        return max(map(operator.sub, sums[k:], sums)) / k
    

NumPy version (requires `import numpy as np`):

    
    
    def findMaxAverage(self, nums, k):
        sums = np.cumsum([0] + nums)
        return int(max(sums[k:] - sums[:-k])) / k



