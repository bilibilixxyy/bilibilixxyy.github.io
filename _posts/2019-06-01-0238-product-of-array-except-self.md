---
layout: post
title: 238. Product of Array Except Self
---
### Question
Given an array `nums` of _n_ integers where _n_ > 1,  return an array `output`
such that `output[i]` is equal to the product of all the elements of `nums`
except `nums[i]`.

 **Example:**

    
    
     **Input:**  [1,2,3,4]
    **Output:** [24,12,8,6]
    

**Note:** Please solve it **without division** and in O( _n_ ).

 **Follow up:**  
Could you solve it with constant space complexity? (The output array **does
not** count as extra space for the purpose of space complexity analysis.)

### Solution 1
    
    
    public class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        res[0] = 1;
        for (int i = 1; i < n; i++) {
            res[i] = res[i - 1] * nums[i - 1];
        }
        int right = 1;
        for (int i = n - 1; i >= 0; i--) {
            res[i] *= right;
            right *= nums[i];
        }
        return res;
    }
    

}


### Solution 2
    
    
    class Solution:
        # @param {integer[]} nums
        # @return {integer[]}
        def productExceptSelf(self, nums):
            p = 1
            n = len(nums)
            output = []
            for i in range(0,n):
                output.append(p)
                p = p * nums[i]
            p = 1
            for i in range(n-1,-1,-1):
                output[i] = output[i] * p
                p = p * nums[i]
            return output


### Solution 3
Use `tmp` to store temporary multiply result by two directions. Then fill it
into `result`. Bingo!

    
    
    public int[] productExceptSelf(int[] nums) {
        int[] result = new int[nums.length];
        for (int i = 0, tmp = 1; i < nums.length; i++) {
            result[i] = tmp;
            tmp *= nums[i];
        }
        for (int i = nums.length - 1, tmp = 1; i >= 0; i--) {
            result[i] *= tmp;
            tmp *= nums[i];
        }
        return result;
    }



