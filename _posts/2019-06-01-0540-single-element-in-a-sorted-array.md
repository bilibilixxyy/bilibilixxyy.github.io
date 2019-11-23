---
layout: post
title: 540. Single Element in a Sorted Array
---
### Question
Given a sorted array consisting of only integers where every element appears
exactly twice except for one element which appears exactly once. Find this
single element that appears only once.



 **Example 1:**

    
    
     **Input:** [1,1,2,3,3,4,4,8,8]
    **Output:** 2
    

**Example 2:**

    
    
    **Input:** [3,3,7,7,10,11,11]
    **Output:** 10
    



 **Note:** Your solution should run in O(log n) time and O(1) space.

### Solution 1
All credits go to [@Penghuan](https://discuss.leetcode.com/post/175763) who
thought of using the pairs wisely.

This method seems to be a bit simpler to understand, since it doesn't start
with the left half and stays a little bit closer to the conventional
solutions.

    
    
       public static int singleNonDuplicate(int[] nums) {
            int start = 0, end = nums.length - 1;
    
            while (start < end) {
                // We want the first element of the middle pair,
                // which should be at an even index if the left part is sorted.
                // Example:
                // Index: 0 1 2 3 4 5 6
                // Array: 1 1 3 3 4 8 8
                //            ^
                int mid = (start + end) / 2;
                if (mid % 2 == 1) mid--;
    
                // We didn't find a pair. The single element must be on the left.
                // (pipes mean start & end)
                // Example: |0 1 1 3 3 6 6|
                //               ^ ^
                // Next:    |0 1 1|3 3 6 6
                if (nums[mid] != nums[mid + 1]) end = mid;
    
                // We found a pair. The single element must be on the right.
                // Example: |1 1 3 3 5 6 6|
                //               ^ ^
                // Next:     1 1 3 3|5 6 6|
                else start = mid + 2;
            }
    
            // 'start' should always be at the beginning of a pair.
            // When 'start > end', start must be the single element.
            return nums[start];
        }
    


### Solution 2
My solution using binary search. lo and hi are not regular index, but the pair
index here. Basically you want to find the first even-index number not
followed by the same number.

    
    
    public class Solution {
        public int singleNonDuplicate(int[] nums) {
            // binary search
            int n=nums.length, lo=0, hi=n/2;
            while (lo < hi) {
                int m = (lo + hi) / 2;
                if (nums[2*m]!=nums[2*m+1]) hi = m;
                else lo = m+1;
            }
            return nums[2*lo];
        }
    }
    


### Solution 3
Simply find the first index whose "partner index" (the index xor 1) holds a
different value.

 **Ruby:**

    
    
     def single_non_duplicate(nums)
      nums[(0..nums.size).bsearch { |i| nums[i] != nums[i^1] }]
    end
    

**Python**

    
    
    def singleNonDuplicate(self, nums):
         lo, hi = 0, len(nums) - 1
        while lo < hi:
            mid = (lo + hi) / 2
            if nums[mid] == nums[mid ^ 1]:
                lo = mid + 1
            else:
                hi = mid
        return nums[lo]
    

**Java:**

    
    
     public int singleNonDuplicate(int[] nums) {
        int lo = 0, hi = nums.length - 1;
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (nums[mid] == nums[mid ^ 1])
                lo = mid + 1;
            else
                hi = mid;
        }
        return nums[lo];
    }



