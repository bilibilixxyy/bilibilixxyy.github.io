---
layout: post
title: 628. Maximum Product of Three Numbers
---
### Question
Given an integer array, find three numbers whose product is maximum and output
the maximum product.

 **Example 1:**

    
    
     **Input:** [1,2,3]
    **Output:** 6
    



**Example 2:**

    
    
    **Input:** [1,2,3,4]
    **Output:** 24
    



 **Note:**

  1. The length of the given array will be in range [3,104] and all elements are in the range [-1000, 1000].
  2. Multiplication of any three numbers in the input won't exceed the range of 32-bit signed integer.

### Solution 1
Simply find out the three largest numbers and the two smallest numbers using
one pass.

    
    
        public int maximumProduct(int[] nums) {
            int max1 = Integer.MIN_VALUE, max2 = Integer.MIN_VALUE, max3 = Integer.MIN_VALUE, min1 = Integer.MAX_VALUE, min2 = Integer.MAX_VALUE;
            for (int n : nums) {
                if (n > max1) {
                    max3 = max2;
                    max2 = max1;
                    max1 = n;
                } else if (n > max2) {
                    max3 = max2;
                    max2 = n;
                } else if (n > max3) {
                    max3 = n;
                }
    
                if (n < min1) {
                    min2 = min1;
                    min1 = n;
                } else if (n < min2) {
                    min2 = n;
                }
            }
            return Math.max(max1*max2*max3, max1*min1*min2);
        }
    


### Solution 2
My first solution using `sort`

    
    
    def maximumProduct(self, nums):
            nums.sort()
            return max(nums[-1] * nums[-2] * nums[-3], nums[0] * nums[1] * nums[-1])
    

I found a exactly same solution in discuss. Anyway, O(NlogN) is not adorable
and O(N) is possible.

    
    
    def maximumProduct(self, nums):
            a, b = heapq.nlargest(3, nums), heapq.nsmallest(2, nums)
            return max(a[0] * a[1] * a[2], b[0] * b[1] * a[0])
    

Make it 1 line if you like:

    
    
        def maximumProduct(self, nums):
            return max(nums) * max(a * b for a, b in [heapq.nsmallest(2, nums), heapq.nlargest(3, nums)[1:]])
    

**Update:**  
 **Q:** Time complexity?  
 **A:** Let me make it clear.  
My solution is `O(NlogK)`, where K = 3.  
So I said it's `O(N)`  
And the best solution can be `O(N)`.

@macheret help explained the following:

For heapq.nlargest / heapq.nsmallest, looking at the (source
code)[<https://github.com/python/cpython/blob/e22072fb11246f125aa9ff7629c832b9e2407ef0/Lib/heapq.py#L545-L559>]

First, k items are heapified - that's an O(k*log(k)) operation.  
Then, n-k items are added into the heap with heapreplace - that's n-k
O(log(k)) operations, or O((n-k) _log(k)).  
Add those up, you get O(n_log(k)).  
In this case k is constant and doesn't scale with n, so this usage is O(n).


### Solution 3
    
    
        public int maximumProduct(int[] nums) {
            
             Arrays.sort(nums);
             //One of the Three Numbers is the maximum value in the array.
    
             int a = nums[nums.length - 1] * nums[nums.length - 2] * nums[nums.length - 3];
             int b = nums[0] * nums[1] * nums[nums.length - 1];
             return a > b ? a : b;
        }
    

python3

    
    
     def maximumProduct(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            nums.sort()
            a = nums[-1] * nums[-2] * nums[-3]
            b = nums[0] * nums[1] * nums[-1]
            return max(a,b)
    



