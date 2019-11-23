---
layout: post
title: 414. Third Maximum Number
---
### Question
Given a **non-empty** array of integers, return the **third** maximum number
in this array. If it does not exist, return the maximum number. The time
complexity must be in O(n).

 **Example 1:**  

    
    
     **Input:** [3, 2, 1]
    
    **Output:** 1
    
    **Explanation:** The third maximum is 1.
    

**Example 2:**  

    
    
    **Input:** [1, 2]
    
    **Output:** 2
    
    **Explanation:** The third maximum does not exist, so the maximum (2) is returned instead.
    

**Example 3:**  

    
    
    **Input:** [2, 2, 3, 1]
    
    **Output:** 1
    
    **Explanation:** Note that the third maximum here means the third maximum distinct number.
    Both numbers with value 2 are both considered as second maximum.
    

### Solution 1
    
    
        public int thirdMax(int[] nums) {
            Integer  max1 = null;
            Integer max2 = null;
            Integer max3 = null;
            for (Integer n : nums) {
                if (n.equals(max1) || n.equals(max2) || n.equals(max3)) continue;
                if (max1 == null || n > max1) {
                    max3 = max2;
                    max2 = max1;
                    max1 = n;
                } else if (max2 == null || n > max2) {
                    max3 = max2;
                    max2 = n;
                } else if (max3 == null || n > max3) {
                    max3 = n;
                }
            }
            return max3 == null ? max1 : max3;
        }
    


### Solution 2
    
    
    class Solution(object):
        def thirdMax(self, nums):
            v = [float('-inf'), float('-inf'), float('-inf')]
            for num in nums:
                if num not in v:
                    if num > v[0]:   v = [num, v[0], v[1]]
                    elif num > v[1]: v = [v[0], num, v[1]]
                    elif num > v[2]: v = [v[0], v[1], num]
            return max(nums) if float('-inf') in v else v[2]
    

Time complexity is O(n), space complexity is O(1).


### Solution 3
Track the largest three values in a set.

    
    
    int thirdMax(vector<int>& nums) {
        set<int> top3;
        for (int num : nums) {
            top3.insert(num);
            if (top3.size() > 3)
                top3.erase(top3.begin());
        }
        return top3.size() == 3 ? *top3.begin() : *top3.rbegin();
    }
    

Alternatively (not sure which one I like better):

    
    
    int thirdMax(vector<int>& nums) {
        set<int> top3;
        for (int num : nums)
            if (top3.insert(num).second && top3.size() > 3)
                top3.erase(top3.begin());
        return top3.size() == 3 ? *top3.begin() : *top3.rbegin();
    }



