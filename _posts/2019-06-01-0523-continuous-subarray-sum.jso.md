---
layout: post
title: 523. Continuous Subarray Sum
---
### Question
Given a list of **non-negative** numbers and a target **integer** k, write a
function to check if the array has a continuous subarray of size at least 2
that sums up to a multiple of **k** , that is, sums up to n*k where n is also
an **integer**.



 **Example 1:**

    
    
     **Input:** [23, 2, 4, 6, 7],  k=6
    **Output:** True
    **Explanation:** Because [2, 4] is a continuous subarray of size 2 and sums up to 6.
    

**Example 2:**

    
    
    **Input:** [23, 2, 6, 4, 7],  k=6
    **Output:** True
    **Explanation:** Because [23, 2, 6, 4, 7] is an continuous subarray of size 5 and sums up to 42.
    



 **Note:**

  1. The length of the array won't exceed 10,000.
  2. You may assume the sum of all the numbers is in the range of a signed 32-bit integer.

### Solution 1
We iterate through the input array exactly once, keeping track of the running
sum mod k of the elements in the process. If we find that a running sum value
at index j has been previously seen before in some earlier index i in the
array, then we know that the sub-array (i,j] contains a desired sum.

    
    
    public boolean checkSubarraySum(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<Integer, Integer>(){{put(0,-1);}};;
        int runningSum = 0;
        for (int i=0;i<nums.length;i++) {
            runningSum += nums[i];
            if (k != 0) runningSum %= k; 
            Integer prev = map.get(runningSum);
            if (prev != null) {
                if (i - prev > 1) return true;
            }
            else map.put(runningSum, i);
        }
        return false;
    }
    


### Solution 2
This problem contributed a lot of bugs to my contest score... Let's read the
description again, pay attention to `red` sections:

Given a list of `non-negative` numbers and a target integer k, write a
function to check if the array has a `continuous subarray` of size `at least
2` that sums up to the `multiple` of k, that is, sums up to n*k where n is
also an `integer`.

Some `damn it!` test cases:

  1. [0], 0 -> false;
  2. [5, 2, 4], 5 -> false;
  3. [0, 0], 100 -> true;
  4. [1,5], -6 -> true;  
etc...

    
    
    public class Solution {
        public boolean checkSubarraySum(int[] nums, int k) {
            // Since the size of subarray is at least 2.
            if (nums.length <= 1) return false;
            // Two continuous "0" will form a subarray which has sum = 0. 0 * k == 0 will always be true.
            for (int i = 0; i < nums.length - 1; i++) {
                if (nums[i] == 0 && nums[i + 1] == 0) return true;
            }
    
            // At this point, k can't be "0" any longer.
            if (k == 0) return false;
            // Let's only check positive k. Because if there is a n makes n * k = sum, it is always true -n * -k = sum.
            if (k < 0) k = -k;
    
            Map<Integer, Integer> sumToIndex = new HashMap<>();
            int sum = 0;
            sumToIndex.put(0, -1);
    
            for (int i = 0; i < nums.length; i++) {
                sum += nums[i];
                // Validate from the biggest possible n * k to k
                for (int j = (sum / k) * k; j >= k; j -= k) {
                    if (sumToIndex.containsKey(sum - j) && (i - sumToIndex.get(sum - j) > 1)) return true;
                }
                if (!sumToIndex.containsKey(sum)) sumToIndex.put(sum, i);
            }
    
            return false;
        }
    }
    


### Solution 3
There is really no need to use map, the required length is at least 2, so we
just need to insert the mod one iteration later.

    
    
    class Solution {
    public:
        bool checkSubarraySum(vector<int>& nums, int k) {
            int n = nums.size(), sum = 0, pre = 0;
            unordered_set<int> modk;
            for (int i = 0; i < n; ++i) {
                sum += nums[i];
                int mod = k == 0 ? sum : sum % k;
                if (modk.count(mod)) return true;
                modk.insert(pre);
                pre = mod;
            }
            return false;
        }
    };



