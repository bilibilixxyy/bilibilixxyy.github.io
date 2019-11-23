---
layout: post
title: 748. Largest Number At Least Twice of Others
---
### Question
In a given integer array `nums`, there is always exactly one largest element.

Find whether the largest element in the array is at least twice as much as
every other number in the array.

If it is, return the **index** of the largest element, otherwise return -1.

 **Example 1:**

    
    
     **Input:** nums = [3, 6, 1, 0]
    **Output:** 1
    **Explanation:** 6 is the largest integer, and for every other number in the array x,
    6 is more than twice as big as x.  The index of value 6 is 1, so we return 1.
    



**Example 2:**

    
    
    **Input:** nums = [1, 2, 3, 4]
    **Output:** -1
    **Explanation:** 4 isn't at least as big as twice the value of 3, so we return -1.
    



 **Note:**

  1. `nums` will have a length in the range `[1, 50]`.
  2. Every `nums[i]` will be an integer in the range `[0, 99]`.

### Solution 1
Just iterate through the array and note the highest and second highest
numbers. Might as well take note of the index at the same time.

One slightly clever idea was to shuffle the highest to the second-highest
whenever a new highest was found. That was a way to handle the case where
there are two (or more) value tied for highest.

    
    
    class Solution:
        def dominantIndex(self, nums):
            if len(nums) == 0: return -1
    
            highest = -1
            secondHighest = -1
            highestIndex = 0
            
            for i,n in enumerate(nums):
                if n >= highest:
                    secondHighest = highest
                    highest = n
                    highestIndex = i
                elif n > secondHighest:
                    secondHighest = n
    
            if highest < secondHighest*2:
                highestIndex = -1
            
            return highestIndex
    
    


### Solution 2
    
    
    class Solution {
        public int dominantIndex(int[] nums) {
            int max = -1, index = -1, second = -1;
            for (int i = 0; i < nums.length; i++) {
                if (nums[i] > max) {
                    second = max;
                    max = nums[i];
                    index = i;
                } else if (nums[i] > second)
                    second = nums[i];
            }
            return second * 2 <= max ? index : -1;
        }
    }
    


### Solution 3
    
    
    class Solution {
        public int dominantIndex(int[] nums) {
            if(nums == null || nums.length == 0){
                return -1;
            }
            
            if(nums.length == 1){
                return 0;
            }
            int max = Integer.MIN_VALUE + 1;
            int secondMax = Integer.MIN_VALUE;
            int index = 0;
            
            for(int i = 0; i < nums.length; i++){
                if(nums[i] > max){
                    secondMax = max;
                    max = nums[i];
                    index = i;
                } else if(nums[i] != max && nums[i] > secondMax){
                    secondMax = nums[i];
                }
            }
            if(secondMax * 2 <= max){
                return index;
            }
            return -1;
        }
    }
    



