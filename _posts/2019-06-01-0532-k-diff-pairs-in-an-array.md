---
layout: post
title: 532. K-diff Pairs in an Array
---
### Question
Given an array of integers and an integer **k** , you need to find the number
of **unique** k-diff pairs in the array. Here a **k-diff** pair is defined as
an integer pair (i, j), where **i** and **j** are both numbers in the array
and their [absolute
difference](https://en.wikipedia.org/wiki/Absolute_difference) is **k**.

 **Example 1:**  

    
    
     **Input:** [3, 1, 4, 1, 5], k = 2
    **Output:** 2
    **Explanation:** There are two 2-diff pairs in the array, (1, 3) and (3, 5).  
    Although we have two 1s in the input, we should only return the number of **unique** pairs.
    

**Example 2:**  

    
    
    **Input:** [1, 2, 3, 4, 5], k = 1
    **Output:** 4
    **Explanation:** There are four 1-diff pairs in the array, (1, 2), (2, 3), (3, 4) and (4, 5).
    

**Example 3:**  

    
    
    **Input:** [1, 3, 1, 5, 4], k = 0
    **Output:** 1
    **Explanation:** There is one 0-diff pair in the array, (1, 1).
    

**Note:**  

  1. The pairs (i, j) and (j, i) count as the same pair.
  2. The length of the array won't exceed 10,000.
  3. All the integers in the given input belong to the range: [-1e7, 1e7].

### Solution 1
    
    
    public class Solution {
        public int findPairs(int[] nums, int k) {
            if (nums == null || nums.length == 0 || k < 0)   return 0;
            
            Map<Integer, Integer> map = new HashMap<>();
            int count = 0;
            for (int i : nums) {
                map.put(i, map.getOrDefault(i, 0) + 1);
            }
            
            for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
                if (k == 0) {
                    //count how many elements in the array that appear more than twice.
                    if (entry.getValue() >= 2) {
                        count++;
                    } 
                } else {
                    if (map.containsKey(entry.getKey() + k)) {
                        count++;
                    }
                }
            }
            
            return count;
        }
    }
    


### Solution 2
    
    
    def findPairs(self, nums, k):
            res = 0
            c = collections.Counter(nums)
            for i in c:
                if k > 0 and i + k in c or k == 0 and c[i] > 1:
                    res += 1
            return res
    

which equals to:

    
    
    def findPairs(self, nums, k):
            c = collections.Counter(nums)
            return  sum(k > 0 and i + k in c or k == 0 and c[i] > 1 for i in c)


### Solution 3
    
    
        def findPairs(self, nums, k):
           return len(set(nums)&{n+k for n in nums}) if k>0 else sum(v>1 for v in collections.Counter(nums).values()) if k==0 else 0
    

which is equivalent to:

    
    
        def findPairs(self, nums, k):
            if k>0:
                return len(set(nums)&set(n+k for n in nums))
            elif k==0:
                sum(v>1 for v in collections.Counter(nums).values())
            else:
                return 0
    



