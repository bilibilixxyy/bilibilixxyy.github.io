---
layout: post
title: 219. Contains Duplicate II
---
### Question
Given an array of integers and an integer _k_ , find out whether there are two
distinct indices _i_ and _j_ in the array such that **nums[i] = nums[j]** and
the **absolute** difference between _i_ and _j_ is at most _k_.

 **Example 1:**

    
    
     **Input:** nums = [1,2,3,1], k = 3
    **Output:** true
    

**Example 2:**

    
    
    **Input:** nums = [1,0,1,1], k = 1
    **Output:** true
    

**Example 3:**

    
    
    **Input:** nums = [1,2,3,1,2,3], k = 2
    **Output:** false
    

### Solution 1
    
    
    public boolean containsNearbyDuplicate(int[] nums, int k) {
            Set<Integer> set = new HashSet<Integer>();
            for(int i = 0; i < nums.length; i++){
                if(i > k) set.remove(nums[i-k-1]);
                if(!set.add(nums[i])) return true;
            }
            return false;
     }


### Solution 2
    
    
    def containsNearbyDuplicate(self, nums, k):
        dic = {}
        for i, v in enumerate(nums):
            if v in dic and i - dic[v] <= k:
                return True
            dic[v] = i
        return False


### Solution 3
    
    
    class Solution {
    public:
        bool containsNearbyDuplicate(vector<int>& nums, int k)
        {
           unordered_set<int> s;
           
           if (k <= 0) return false;
           if (k >= nums.size()) k = nums.size() - 1;
           
           for (int i = 0; i < nums.size(); i++)
           {
               if (i > k) s.erase(nums[i - k - 1]);
               if (s.find(nums[i]) != s.end()) return true;
               s.insert(nums[i]);
           }
           
           return false;
        }
    };
    

The basic idea is to maintain a set s which contain unique values from nums[i
- k] to nums[i - 1],  
if nums[i] is in set s then return true else update the set.



