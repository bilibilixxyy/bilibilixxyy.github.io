---
layout: post
title: 611. Valid Triangle Number
---
### Question
Given an array consists of non-negative integers, your task is to count the
number of triplets chosen from the array that can make triangles if we take
them as side lengths of a triangle.

 **Example 1:**  

    
    
     **Input:** [2,2,3,4]
    **Output:** 3
    **Explanation:**
    Valid combinations are: 
    2,3,4 (using the first 2)
    2,3,4 (using the second 2)
    2,2,3
    

**Note:**  

  1. The length of the given array won't exceed 1000.
  2. The integers in the given array are in the range of [0, 1000].

### Solution 1
    
    
     public static int triangleNumber(int[] A) {
        Arrays.sort(A);
        int count = 0, n = A.length;
        for (int i=n-1;i>=2;i--) {
            int l = 0, r = i-1;
            while (l < r) {
                if (A[l] + A[r] > A[i]) {
                    count += r-l;
                    r--;
                }
                else l++;
            }
        }
        return count;
    }
    


### Solution 2
This problem is very similar to 3-Sum, in 3-Sum, we can use three pointers (i,
j, k and i < j < k) to solve the problem in O(n^2) time for a sorted array,
the way we do in 3-Sum is that we first lock pointer i and then scan j and k,
if nums[j] + nums[k] is too large, k--, otherwise j++, once we complete the
scan, increase pointer i and repeat.

For this problem, once we sort the input array nums, the key to solve the
problem is that given nums[k], count the combination of i and j where nums[i]
+ nums[j] > nums[k] (so that they can form a triangle). If nums[i] + nums[j]
is larger than nums[k], we know that there will be j - i combination.

Let's take the following array for example, let's mark the three pointers:

    
    
     i                  j   k
    [3, 19, 22, 24, 35, 82, 84]
    

because 3 + 82 > 84 and the numbers between 3 and 82 are always larger than 3,
so we can quickly tell that there will be j - i combination which can form the
triangle, and they are:

    
    
    3,  82, 84
    19, 82, 84
    22, 82, 84
    24, 82, 84
    35, 82, 84
    

Now let's lock k and point to 35:

    
    
     i          j   k
    [3, 19, 22, 24, 35, 82, 84]
    

because 3 + 24 < 35, if we move j to the left, the sum will become even
smaller, so we have to move pointer i to the next number 19, and now we found
that 19 + 24 > 35, and we don't need to scan 22, we know that 22 must be ok!

Following is the JavaScript solution:

    
    
    const triangleNumber = nums => {
      nums.sort((a, b) => a - b);
    
      let count = 0;
    
      for (let k = nums.length - 1; k > 1; k--) {
        for (let i = 0, j = k - 1; i < j;) {
          if (nums[i] + nums[j] > nums[k]) {
            count += j - i;
            j--;
          } else {
            i++;
          }
        }
      }
    
      return count;
    };
    


### Solution 3
Same as <https://leetcode.com/problems/3sum-closest>

Assume `a` is the longest edge, `b` and `c` are shorter ones, to form a
triangle, they need to satisfy `len(b) + len(c) > len(a)`.

    
    
    public class Solution {
        public int triangleNumber(int[] nums) {
            int result = 0;
            if (nums.length < 3) return result;
            
            Arrays.sort(nums);
    
            for (int i = 2; i < nums.length; i++) {
                int left = 0, right = i - 1;
                while (left < right) {
                    if (nums[left] + nums[right] > nums[i]) {
                        result += (right - left);
                        right--;
                    }
                    else {
                        left++;
                    }
                }
            }
            
            return result;
        }
    }
    



