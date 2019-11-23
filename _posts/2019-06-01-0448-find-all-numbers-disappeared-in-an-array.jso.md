---
layout: post
title: 448. Find All Numbers Disappeared in an Array
---
### Question
Given an array of integers where 1 ≤ a[i] ≤ _n_ ( _n_ = size of array), some
elements appear twice and others appear once.

Find all the elements of [1, _n_ ] inclusive that do not appear in this array.

Could you do it without extra space and in O( _n_ ) runtime? You may assume
the returned list does not count as extra space.

 **Example:**

    
    
     **Input:**
    [4,3,2,7,8,2,3,1]
    
    **Output:**
    [5,6]
    

### Solution 1
The basic idea is that we iterate through the input array and mark elements as
negative using `nums[nums[i] -1] = -nums[nums[i]-1]`. In this way all the
numbers that we have seen will be marked as negative. In the second iteration,
if a value is not marked as negative, it implies we have never seen that index
before, so just add it to the return list.

    
    
        public List<Integer> findDisappearedNumbers(int[] nums) {
            List<Integer> ret = new ArrayList<Integer>();
            
            for(int i = 0; i < nums.length; i++) {
                int val = Math.abs(nums[i]) - 1;
                if(nums[val] > 0) {
                    nums[val] = -nums[val];
                }
            }
            
            for(int i = 0; i < nums.length; i++) {
                if(nums[i] > 0) {
                    ret.add(i+1);
                }
            }
            return ret;
        }
    


### Solution 2
For each number i in nums,  
we mark the number that i points as negative.  
Then we filter the list, get all the indexes  
who points to a positive number.  
Since those indexes are not visited.

    
    
    class Solution(object):
        def findDisappearedNumbers(self, nums):
            """
            :type nums: List[int]
            :rtype: List[int]
            """
            # For each number i in nums,
            # we mark the number that i points as negative.
            # Then we filter the list, get all the indexes
            # who points to a positive number
            for i in xrange(len(nums)):
                index = abs(nums[i]) - 1
                nums[index] = - abs(nums[index])
    
            return [i + 1 for i in range(len(nums)) if nums[i] > 0]
    
    


### Solution 3
    
    
    public List<Integer> findDisappearedNumbers(int[] nums) {
            List<Integer> res = new ArrayList<>();
            int n = nums.length;
            for (int i = 0; i < nums.length; i ++) nums[(nums[i]-1) % n] += n;
            for (int i = 0; i < nums.length; i ++) if (nums[i] <= n) res.add(i+1);
            return res;
        }
    



