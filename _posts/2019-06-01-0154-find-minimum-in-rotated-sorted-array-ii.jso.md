---
layout: post
title: 154. Find Minimum in Rotated Sorted Array II
---
### Question
Suppose an array sorted in ascending order is rotated at some pivot unknown to
you beforehand.

(i.e.,  `[0,1,2,4,5,6,7]` might become  `[4,5,6,7,0,1,2]`).

Find the minimum element.

The array may contain duplicates.

 **Example 1:**

    
    
     **Input:** [1,3,5]
    **Output:** 1

**Example 2:**

    
    
    **Input:** [2,2,2,0,1]
    **Output:** 0

 **Note:**

  * This is a follow up problem to [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/).
  * Would allow duplicates affect the run-time complexity? How and why?

### Solution 1
    
    
    class Solution {
    public:
        int findMin(vector<int> &num) {
            int lo = 0;
            int hi = num.size() - 1;
            int mid = 0;
            
            while(lo < hi) {
                mid = lo + (hi - lo) / 2;
                
                if (num[mid] > num[hi]) {
                    lo = mid + 1;
                }
                else if (num[mid] < num[hi]) {
                    hi = mid;
                }
                else { // when num[mid] and num[hi] are same
                    hi--;
                }
            }
            return num[lo];
        }
    };
    

When num[mid] == num[hi], we couldn't sure the position of minimum in mid's
left or right, so just let upper bound reduce one.


### Solution 2
There is no faster way than O(n) to solve an input like "1 1 1 1 1 0 1 1 1 1 1
1 1 1". Binary search won't work in this case as your nums[start] == nums[mid]
== nums[end], which half would you discard then? In other words, you have to
examine all elements. With that being said, this is probably the only way to
solve it. Run time: 6ms. Not bad at all.

    
    
    class Solution {
    public:
        int findMin(vector& nums) {
            int min = nums[0];
            for (int val : nums)
            {
                if (min > val)
                {
                    min = val;
                }
            }
            
            return min;
        }
    };
    


### Solution 3
**Logical Thought**  
We assert the loop invariant is the index of the minimum, `min`, is within the
range `[lo, hi]`.

  1. Before the loop, `min` is in `[0, nums.length - 1]`. To satisfy the invariant, `lo = 0, hi = nums.length - 1`
  2. If we guess `mi`,  
if `nums[mi] > nums[hi]`, `min` should be always in `[mi + 1, hi]` (explained
in **Essence** ). To satisfy the invariant, `lo = mi + 1`;  
else if `nums[mi] < nums[lo]`, min should be always in `[lo + 1, mi]`
(explained in **Essence** ), to satisfy the assertion, `hi = mi, lo = lo + 1`;  
else (`nums[lo] <= nums[mi] <= nums[hi]`) min should be always nums[lo].

  3. After the loop, lo = hi, min should be in `[lo, lo]`, to satisfy the assertion, `min = lo`.

 **Essence**  
If we split the array with mi into [lo, mi] and [mi, hi]. If [lo, mi] is not
sorted, since we detect [lo, mi] is not sorted by nums[lo] > nums[mi] so
nums[lo] cannot be min, `min` must be within `(lo, mi]`. If [mi, hi] is not
sorted, `min` must be within `(mi, hi]` \- since we detect [mi, hi] is not
sorted by nums[mi] > nums[hi], nums[mi] cannot be min. If they are both
sorted, `nums[lo]` is the min.  
There are 4 kinds of relationship among num[lo], nums[mi], nums[hi]

    
    
    nums[lo] <= nums[mi] <= nums[hi], min is nums[lo]
    nums[lo] > nums[mi] <= nums[hi], (lo, mi] is not sorted, min is inside
    nums[lo] <= nums[mi] > nums[hi], (mi, hi] is not sorted, min is inside
    nums[lo] > nums[mi] > nums[hi], impossible
    

**Code**

    
    
         public int findMin(int[] nums) {
            
            int lo = 0, hi = nums.length - 1;
            while (lo < hi) {
                int mi = lo + (hi - lo) / 2;
                if (nums[mi] > nums[hi]) { 
                    lo = mi + 1;
                }
                else if (nums[mi] < nums[lo]) { 
                    hi = mi;
                    lo++;
                }
                else { // nums[lo] <= nums[mi] <= nums[hi] 
                    hi--;
                }
            }
            
            return nums[lo];
        }
    

I appreciate your VOTE UP (▰╹◡╹▰)



