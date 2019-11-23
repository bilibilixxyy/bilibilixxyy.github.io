---
layout: post
title: 442. Find All Duplicates in an Array
---
### Question
Given an array of integers, 1 ≤ a[i] ≤ _n_ ( _n_ = size of array), some
elements appear **twice** and others appear **once**.

Find all the elements that appear **twice** in this array.

Could you do it without extra space and in O( _n_ ) runtime?

 **Example:**  

    
    
     **Input:**
    [4,3,2,7,8,2,3,1]
    
    **Output:**
    [2,3]
    

### Solution 1
    
    
    public class Solution {
        // when find a number i, flip the number at position i-1 to negative. 
        // if the number at position i-1 is already negative, i is the number that occurs twice.
        
        public List<Integer> findDuplicates(int[] nums) {
            List<Integer> res = new ArrayList<>();
            for (int i = 0; i < nums.length; ++i) {
                int index = Math.abs(nums[i])-1;
                if (nums[index] < 0)
                    res.add(Math.abs(index+1));
                nums[index] = -nums[index];
            }
            return res;
        }
    }
    


### Solution 2
O(1) space not including the input and output variables

The idea is we do a linear pass using the input array itself as a hash to
store which numbers have been seen before. We do this by making elements at
certain indexes negative. See the full explanation here

<http://www.geeksforgeeks.org/find-duplicates-in-on-time-and-constant-extra-
space/>

    
    
    class Solution(object):
        def findDuplicates(self, nums):
            """
            :type nums: List[int]
            :rtype: List[int]
            """
            res = []
            for x in nums:
                if nums[abs(x)-1] < 0:
                    res.append(abs(x))
                else:
                    nums[abs(x)-1] *= -1
            return res
    


### Solution 3
Firstly, we put each element x in nums[x - 1]. Since x ranges from 1 to N,
then x - 1 ranges from 0 to N - 1, it won't exceed the bound of the array.  
Secondly, we check through the array. If a number x doesn't present in nums[x
- 1], then x is absent.

    
    
    class Solution {
    public:
        vector<int> findDuplicates(vector<int>& nums) {
            vector<int> res;
            int i = 0;
            while (i < nums.size()) {
                if (nums[i] != nums[nums[i]-1]) swap(nums[i], nums[nums[i]-1]);
                else i++;
            }
            for (i = 0; i < nums.size(); i++) {
                if (nums[i] != i + 1) res.push_back(nums[i]);
            }
            return res;
        }
    };
    



