---
layout: post
title: 954. Maximum Sum Circular Subarray
---
### Question
Given a **circular  array**  **C** of integers represented by  `A`, find the
maximum possible sum of a non-empty subarray of **C**.

Here, a  _circular  array_ means the end of the array connects to the
beginning of the array.  (Formally, `C[i] = A[i]` when `0 <= i < A.length`,
and `C[i+A.length] = C[i]` when `i >= 0`.)

Also, a subarray may only include each element of the fixed buffer `A` at most
once.  (Formally, for a subarray `C[i], C[i+1], ..., C[j]`, there does not
exist `i <= k1, k2 <= j` with `k1 % A.length = k2 % A.length`.)



 **Example 1:**

    
    
     **Input:** [1,-2,3,-2]
    **Output:** 3
    **Explanation:** Subarray [3] has maximum sum 3
    

**Example 2:**

    
    
    **Input:** [5,-3,5]
    **Output:** 10
    **Explanation:**  Subarray [5,5] has maximum sum 5 + 5 = 10
    

**Example 3:**

    
    
    **Input:** [3,-1,2,-1]
    **Output:** 4
    **Explanation:**  Subarray [2,-1,3] has maximum sum 2 + (-1) + 3 = 4
    

**Example 4:**

    
    
    **Input:** [3,-2,2,-3]
    **Output:** 3
    **Explanation:**  Subarray [3] and [3,-2,2] both have maximum sum 3
    

**Example 5:**

    
    
    **Input:** [-2,-3,-1]
    **Output:** -1
    **Explanation:**  Subarray [-1] has maximum sum -1
    



 **Note:**

  1. `-30000 <= A[i] <= 30000`
  2. `1 <= A.length <= 30000`

### Solution 1
##  **Intuition**

I guess you know how to solve max subarray sum (without circular).  
If not, you can have a reference here: 53. Maximum Subarray

  

##  **Explanation**

So there are two case.

  1. The first is that the subarray take only a middle part, and we know how to find the max subarray sum.
  2. The second is that the subarray take a part of head array and a part of tail array.  
We can transfer this case to the first one.  
The maximum result equals to the total sum minus the minimum subarray sum.

  

Here is a diagram by @motorix:  
![image](https://assets.leetcode.com/users/motorix/image_1538888300.png)

So the max subarray circular sum equals to  
`max(the max subarray sum, the total sum - the min subarray sum)`

  

##  **Corner case**

Just one to pay attention:  
If all numbers are negative, `maxSum = max(A)` and `minSum = sum(A)`.  
In this case, `max(maxSum, total - minSum) = 0`, which means the sum of an
empty subarray.  
According to the deacription, We need to return the `max(A)`, instead of sum
of am empty subarray.  
So we return the `maxSum` to handle this corner case.  
  

##  **Complexity**

One pass, time `O(N)`  
No extra space, space `O(1)`  
  

 **C++:**

    
    
        int  maxSubarraySumCircular(vector<int>& A) {
            int total = 0, maxSum = -30000, curMax = 0, minSum = 30000, curMin = 0;
            for (int a : A) {
                curMax = max(curMax + a, a);
                maxSum = max(maxSum, curMax);
                curMin = min(curMin + a, a);
                minSum = min(minSum, curMin);
                total += a;
            }
            return maxSum > 0 ? max(maxSum, total - minSum) : maxSum;
        }
    

**Java:**

    
    
        public int  maxSubarraySumCircular(int[] A) {
            int total = 0, maxSum = -30000, curMax = 0, minSum = 30000, curMin = 0;
            for (int a : A) {
                curMax = Math.max(curMax + a, a);
                maxSum = Math.max(maxSum, curMax);
                curMin = Math.min(curMin + a, a);
                minSum = Math.min(minSum, curMin);
                total += a;
            }
            return maxSum > 0 ? Math.max(maxSum, total - minSum) : maxSum;
        }
    

**Python:**

    
    
        def  maxSubarraySumCircular(self, A):
            total, maxSum, curMax, minSum, curMin = 0, -float('inf'), 0, float('inf'), 0
            for a in A:
                curMax = max(curMax + a, a)
                maxSum = max(maxSum, curMax)
                curMin = min(curMin + a, a)
                minSum = min(minSum, curMin)
                total += a
            return max(maxSum, total - minSum) if maxSum > 0 else maxSum
    


### Solution 2
There are two possible cases for the subarray:

  1. The subarray is inside the original array `{x x x (x x x x) x x x}`
  2. The subarray spans the end of the original array `{x x x x x x x (x x x} {x x) x x x x x x x x}`

The second case can be considered as the sum of two subarrays in the original
array:  
`{(x x) x x x x x (x x x)}`  
or the sum of the original array minus the sum of the subarray in the middle:  
`{x x (x x x x x) x x x}`  
Therefore, the max subarray sum in this case = `sum(A) - minSubarraySum(A)`

I write a function `maxSubarraySum`, to calculate the max subarray sum of an
array. The function can also be used to calculate the minSubarraySum after
inverting each element in the array (let A[i] = -A[i]).

Here is my code, note that the subarray in the second case cannot be the
entire array:

    
    
    class Solution {
    public:
        int maxSubarraySumCircular(vector<int>& A) {
            int sum = 0;
            for (int i : A) sum += i;
            
            int sum1 = maxSubarraySum(A.begin(), A.end());
            for (int& i : A) i *= -1;
            int sum2 = sum + maxSubarraySum(A.begin(), A.end() - 1);
            int sum3 = sum + maxSubarraySum(A.begin() + 1, A.end());
            return max(sum1, max(sum2, sum3));
        }
        
        int maxSubarraySum(vector<int>::iterator p, vector<int>::iterator q) {
            int result = INT_MIN;
            int f = 0;
            for (auto i = p; i != q; i++) {
                f += *i;
                result = max(result, f);
                if (f < 0) {
                    f = 0;
                }
            }
            return result;
        }
    };
    


### Solution 3
think about the max sum can be in the middle of the array, or be in the both
ends of the array;  
so we just need to compare the two value and pick the larger one;  
if in the middle, it's exactly same as the [53\. Maximum
Subarray](https://leetcode.com/problems/maximum-subarray/);  
if in both ends, then there must be a smallest one in the middle, so we just
need to find the smallest one in the middle, the use `totalSum - minSum` can
the the possible answer.

    
    
    class Solution {
        public int maxSubarraySumCircular(int[] A) {
            int maxSum = Integer.MIN_VALUE, minSum = Integer.MAX_VALUE;
            int totalSum = 0, curSum1 = 0, curSum2 = 0;
            for (int a : A) {
                totalSum += a;
                curSum1 += a;
                maxSum = Math.max(curSum1, maxSum);
                if (curSum1 < 0) curSum1 = 0;
                curSum2 += a;
                minSum = Math.min(curSum2, minSum);
                if (curSum2 > 0) curSum2 = 0;
            }
            if (maxSum < 0) return maxSum;
            return Math.max(maxSum, totalSum - minSum);
        }
    }
    



