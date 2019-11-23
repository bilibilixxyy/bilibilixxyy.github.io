---
layout: post
title: 300. Longest Increasing Subsequence
---
### Question
Given an unsorted array of integers, find the length of longest increasing
subsequence.

 **Example:**

    
    
     **Input:** [10,9,2,5,3,7,101,18]
    **Output:** 4 
    **Explanation:** The longest increasing subsequence is [2,3,7,101], therefore the length is 4. 

**Note:**

  * There may be more than one LIS combination, it is only necessary for you to return the length.
  * Your algorithm should run in O( _n 2_) complexity.

 **Follow up:** Could you improve it to O( _n_ log _n_ ) time complexity?

### Solution 1
`tails` is an array storing the smallest tail of all increasing subsequences
with length `i+1` in `tails[i]`.  
For example, say we have `nums = [4,5,6,3]`, then all the available increasing
subsequences are:

    
    
    len = 1   :      [4], [5], [6], [3]   => tails[0] = 3
    len = 2   :      [4, 5], [5, 6]       => tails[1] = 5
    len = 3   :      [4, 5, 6]            => tails[2] = 6
    

We can easily prove that tails is a increasing array. Therefore it is possible
to do a binary search in tails array to find the one needs update.

Each time we only do one of the two:

    
    
    (1) if x is larger than all tails, append it, increase the size by 1
    (2) if tails[i-1] < x <= tails[i], update tails[i]
    

Doing so will maintain the tails invariant. The the final answer is just the
size.

**Java**

    
    
     public int lengthOfLIS(int[] nums) {
        int[] tails = new int[nums.length];
        int size = 0;
        for (int x : nums) {
            int i = 0, j = size;
            while (i != j) {
                int m = (i + j) / 2;
                if (tails[m] < x)
                    i = m + 1;
                else
                    j = m;
            }
            tails[i] = x;
            if (i == size) ++size;
        }
        return size;
    }
    // Runtime: 2 ms
    

**Python**

    
    
    def lengthOfLIS(self, nums):
        tails = [ 0] * len(nums)
        size = 0
        for x in nums:
            i, j = 0, size
            while i != j:
                m = (i + j) / 2
                if tails[m] < x:
                    i = m + 1
                else:
                    j = m
            tails[i] = x
            size = max(i + 1, size)
        return size
    
    # Runtime: 48 ms


### Solution 2
    
    
    public class Solution {
        public int lengthOfLIS(int[] nums) {            
            int[] dp = new int[nums.length];
            int len = 0;
    
            for(int x : nums) {
                int i = Arrays.binarySearch(dp, 0, len, x);
                if(i < 0) i = -(i + 1);
                dp[i] = x;
                if(i == len) len++;
            }
    
            return len;
        }
    }


### Solution 3
Inspired by <http://www.geeksforgeeks.org/longest-monotonically-increasing-
subsequence-size-n-log-n/>

    
    
    int lengthOfLIS(vector<int>& nums) {
        vector<int> res;
        for(int i=0; i<nums.size(); i++) {
            auto it = std::lower_bound(res.begin(), res.end(), nums[i]);
            if(it==res.end()) res.push_back(nums[i]);
            else *it = nums[i];
        }
        return res.size();
    }



