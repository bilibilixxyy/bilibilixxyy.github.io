---
layout: post
title: 561. Array Partition I
---
### Question
Given an array of **2n** integers, your task is to group these integers into
**n** pairs of integer, say (a 1, b1), (a2, b2), ..., (an, bn) which makes sum
of min(ai, bi) for all i from 1 to n as large as possible.

 **Example 1:**  

    
    
     **Input:** [1,4,3,2]
    
    **Output:** 4
    **Explanation:** n is 2, and the maximum sum of pairs is 4 = min(1, 2) + min(3, 4).
    

**Note:**  

  1.  **n** is a positive integer, which is in the range of [1, 10000].
  2. All the integers in the array will be in the range of [-10000, 10000].

### Solution 1
The algorithm is first sort the input array and then the sum of 1st, 3rd,
5th..., is the answer.

    
    
    public class Solution {
        public int arrayPairSum(int[] nums) {
            Arrays.sort(nums);
            int result = 0;
            for (int i = 0; i < nums.length; i += 2) {
                result += nums[i];
            }
            return result;
        }
    }
    

Let me try to prove the algorithm...

  1. Assume in each pair `i`, `bi >= ai`.
  2. Denote `Sm = min(a1, b1) + min(a2, b2) + ... + min(an, bn)`. The biggest `Sm` is the answer of this problem. Given `1`, `Sm = a1 + a2 + ... + an`.
  3. Denote `Sa = a1 + b1 + a2 + b2 + ... + an + bn`. `Sa` is constant for a given input.
  4. Denote `di = |ai - bi|`. Given `1`, `di = bi - ai`. Denote `Sd = d1 + d2 + ... + dn`.
  5. So `Sa = a1 + a1 + d1 + a2 + a2 + d2 + ... + an + an + dn = 2Sm + Sd` => `Sm = (Sa - Sd) / 2`. To get the max `Sm`, given `Sa` is constant, we need to make `Sd` as small as possible.
  6. So this problem becomes finding pairs in an array that makes sum of `di` (distance between `ai` and `bi`) as small as possible. Apparently, sum of these distances of adjacent elements is the smallest. If that's not intuitive enough, see attached picture. Case 1 has the smallest `Sd`.  
![0_1492961937328_leetcode561.jpg](/uploads/files/1492961944408-leetcode561.jpg)


### Solution 2
So if we are given [1.2.3.4], is this a possibility?

(1,4) and (2,3)

In that case, won't the correct answer be 5. Our goal is to get the largest
possible sum from the above 4 integers. So, how can 4 be the answer?

Thanks


### Solution 3
inspired by [c++ code O(n),beats
100%](https://discuss.leetcode.com/topic/87483/c-code-o-n-beats-100)

    
    
    public class Solution {
    
    	public int arrayPairSum(int[] nums) {
    		int[] exist = new int[20001];
    		for (int i = 0; i < nums.length; i++) {
    			exist[nums[i] + 10000]++;
    		}
    		int sum = 0;
    		boolean odd = true;
    		for (int i = 0; i < exist.length; i++) {
    			while (exist[i] > 0) {
    				if (odd) {
    					sum += i - 10000;
    				}
    				odd = !odd;
    				exist[i]--;
    			}
    		}
    		return sum;
    	}
    	
    }
    



