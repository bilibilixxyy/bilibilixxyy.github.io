---
layout: post
title: 713. Subarray Product Less Than K
---
### Question
Your are given an array of positive integers `nums`.

Count and print the number of (contiguous) subarrays where the product of all
the elements in the subarray is less than `k`.

 **Example 1:**  

    
    
     **Input:** nums = [10, 5, 2, 6], k = 100
    **Output:** 8
    **Explanation:** The 8 subarrays that have product less than 100 are: [10], [5], [2], [6], [10, 5], [5, 2], [2, 6], [5, 2, 6].
    Note that [10, 5, 2] is not included as the product of 100 is not strictly less than k.
    

**Note:**

* `0 < nums.length <= 50000`.
* `0 < nums[i] < 1000`.
* `0 <= k < 10^6`.

### Solution 1
  1. The idea is always keep an `max-product-window` less than `K`;
  2. Every time shift window by adding a new number on the right(`j`), if the product is greater than k, then try to reduce numbers on the left(`i`), until the subarray product fit less than `k` again, (subarray could be empty);
  3. Each step introduces `x` new subarrays, where x is the size of the current window `(j + 1 - i)`;  
example:  
for window (5, 2), when 6 is introduced, it add 3 new subarray: (5, (2, (6)))

    
    
            (6)
         (2, 6)
      (5, 2, 6)
    

**Java**

    
    
     class Solution {
        public int numSubarrayProductLessThanK(int[] nums, int k) {
            if (k == 0) return 0;
            int cnt = 0;
            int pro = 1;
            for (int i = 0, j = 0; j < nums.length; j++) {
                pro *= nums[j];
                while (i <= j && pro >= k) {
                    pro /= nums[i++];
                }
                cnt += j - i + 1;
            }
            return cnt;        
        }
    }
    

**C++**

    
    
     /**
     * The idea is always keep an max-product-window less than K;
     * Every time add a new number on the right(j), reduce numbers on the left(i), until the subarray product fit less than k again, (subarray could be empty);
     * Each step introduces x new subarrays, where x is the size of the current window (j + 1 - i);
     * example:
     * for window (5, 2, 6), when 6 is introduced, it add 3 new subarray:
     *       (6)
     *    (2, 6)
     * (5, 2, 6)
     */
    class Solution {
    public:
        int numSubarrayProductLessThanK(vector<int>& nums, int k) {
            if (k == 0) return 0;
            int cnt = 0;
            int pro = 1;
            for (int i = 0, j = 0; j < nums.size(); j++) {
                pro *= nums[j];
                while (i <= j && pro >= k) {
                    pro /= nums[i++];
                }
                cnt += j - i + 1;
            }
            return cnt;
        }
    };
    


### Solution 2
    
    
    class Solution {
        public int numSubarrayProductLessThanK(int[] nums, int k) {
            int n = nums.length;
            long p = 1l;
            int i = 0;
            int j = 0;
            int total = 0;
            while(j < n){
                p *= nums[j];
                while(i <= j&&p >= k){
                    p /= nums[i];
                    i++;
                }
                total += (j - i + 1);
                j++;
            }
            return total;
        }
    }


### Solution 3
 **Subarray Product Less Than K** <https://leetcode.com/problems/subarray-
product-less-than-k/description/>

**Two Pointer Solution**

  * Initialize start and end to index 0. Initialize prod to 1. Iterate end from 0 to len(nums)-1. Now if prod * nums[end] is less than k, then all subarray between start and end contribute to the solution. Since we are moving from left to right, we would have already counted all valid subarrays from start to end-1. How many new subarrays with nums[end]? Answer: end-start+1. What will be the updated prod? Answer: prod * nums[end].
  * What if prod * nums[end] >= k? We need to contract the subarray by advancing start until we get a valid solution again. Now what do we do when start > end? Answer: prod=1.
  * Special case: k=0.
  * Time is O(N) and space is O(1).
  * Issue: Overflow with multiplication.

    
    
    class Solution(object):
        def numSubarrayProductLessThanK(self, nums, k):
            """
            :type nums: List[int]
            :type k: int
            :rtype: int
            """
            if k == 0:
                return 0
            start, prod, cnt = 0, 1, 0
            for end in range(len(nums)):
                while start <= end and prod*nums[end] >= k:
                    prod = prod/nums[start]
                    start += 1
                prod = 1 if start > end else prod*nums[end]
                cnt = cnt if start > end else cnt+(end-start+1)
            return cnt
    

**Transform prod to sum problem using log transform**

  * Say we have a subarray [x1,x2,x3] such that its product is less than k.
  * x1*x2*x3 < k. Take log of both sides. log(x1) + log(x2) +log(x3) < log(k).
  * Lets transform original nums as nums = [log(x) for x in nums]. Next transform it into a cdf array called cums. Also, k = log(k).
  * Now if a subarray from index i to index j is a solution, then we can say: cums[j]-cums[i]+nums[i] < k.
  * cums[j]-cums[i]+nums[i]-k < 0 or cums[j]-cums[i]+nums[i]-k <= -1e-12. Say target = k+cums[i]-nums[i]-1e-12, then we need to find the largest j such that cums[j] <= target.
  * We can use bisect_right to find the rightmost index j such that cums[j] <= target. There are strong reasons why we use bisect_right and also what happens when target is in cums or not.
  * Example: nums=[1,2,2,2,2,3]. If target is 2.5, bisect_right will return j=5. We would need to adjust and make j=5. If target is 2, bisect_right will return 4 and no adjustment is required.
  * Time is Nlog(N) and Space is O(N).

    
    
    from math import log
    from bisect import bisect_right
    class Solution(object):
        def transform(self, nums, k):
            k, nums = log(k), [log(x) for x in nums]
            cums = []
            for x in nums:
                if cums == []:
                    cums.append(x)
                else:
                    cums.append(x+cums[-1])
            return cums, nums, k
        
        
        def numSubarrayProductLessThanK(self, nums, k):
            """
            :type nums: List[int]
            :type k: int
            :rtype: int
            """
            if k <= 1:
                return 0
            cums, nums, k = self.transform(nums, k)
            cnt = 0
            for i in range(len(nums)):
                target = k+cums[i]-nums[i]-1e-12
                j = bisect_right(cums, target, i)
                # Adjust index j  when cums[j] != target (i.e. larger). 
                if j == len(cums) or cums[j] > target:
                    j = j-1
                cnt += (j-i+1)
            return cnt
    



