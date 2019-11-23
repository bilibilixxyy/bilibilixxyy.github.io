---
layout: post
title: 1062. Partition Array Into Three Parts With Equal Sum
---
### Question
Given an array `A` of integers, return `true` if and only if we can partition
the array into three **non-empty** parts with equal sums.

Formally, we can partition the array if we can find indexes `i+1 < j` with
`(A[0] + A[1] + ... + A[i] == A[i+1] + A[i+2] + ... + A[j-1] == A[j] + A[j-1]
+ ... + A[A.length - 1])`



 **Example 1:**

    
    
     **Input:** [0,2,1,-6,6,-7,9,1,2,0,1]
    **Output:** true
    **Explanation:** 0 + 2 + 1 = -6 + 6 - 7 + 9 + 1 = 2 + 0 + 1
    

**Example 2:**

    
    
    **Input:** [0,2,1,-6,6,7,9,-1,2,0,1]
    **Output:** false
    

**Example 3:**

    
    
    **Input:** [3,3,6,5,-2,2,5,1,-9,4]
    **Output:** true
    **Explanation:** 3 + 3 = 6 = 5 - 2 + 2 + 5 + 1 - 9 + 4
    



 **Note:**

  1. `3 <= A.length <= 50000`
  2. `-10000 <= A[i] <= 10000`

### Solution 1
## Intuition

We can find our target sum by aggregating all numbers and dividing the result
by 3.  
Then we sum numbers again and track how many times we get the target sum.

> Note 1: if the target sum is zero, we can reach it more than 3 times (thanks
[@prateek_123](https://leetcode.com/prateek_123/) for the find!)

> Note 2: it's enough to find the target sum two times (thanks
[@abdalonimbus](https://leetcode.com/abdalonimbus/) for the optimization!)

> Note 3: note 2 is valid only if the target sum is not zero (good catch,
[@DYR90](https://leetcode.com/dyr90/)!)

## Solution

    
    
    bool canThreePartsEqualSum(vector<int>& A, int parts = 0) {
      auto total = accumulate(begin(A), end(A), 0);
      if (total % 3 != 0) return false;
      for (auto i = 0, sum = 0; i < A.size() && parts < (total != 0 ? 2 : 3); ++i) {
        sum += A[i];
        if (sum == total / 3) ++parts, sum = 0;
      }
      return parts == (total != 0 ? 2 : 3);
    }
    

## Complexity Analysis

Runtime: _O(n)_. We process each element in A twice.  
Memory: _O(1)_.


### Solution 2
  1. Check if the total sum is divisible by 3;
  2. Loop through the array A, and compute part of sum; if the average value is found, reset the part to 0, and increase the counter;
  3. By the end if the average can be seen for at least 3 times, return true; otherwise return false.

 **note: if the average (sum / 3) found 2 times before the end of the array,
then the remaining part is also equals to the average. Therefore, no need to
continue after the counter reaches 3.**

    
    
         public boolean canThreePartsEqualSum(int[] A) {
            int sum = Arrays.stream(A).sum(), part = 0, cnt = 0;
            if (sum % 3 != 0) { return false; }
            for (int a : A) {
                part += a;
                if (part != sum / 3) { continue; } // not the average: sum / 3
                if (++cnt == 3) { return true; } // find an average, increase the counter.
                part = 0; // reset part.
            }
            return false;
        }
    


### Solution 3
**Intuition**

  * Suppose the array was indeed breakable into 3 parts with equal sum. Then the array would look like `S S S`, where `S` represents the sum  
of each segment of the array. Hence, the entire sum would be `S+S+S`=`3S`.

  * Assume that the array is breakable into such segments. Let us compute the prefix sum of the array. Since the array resembles `S S S`,  
therefore the prefix sum would resemble `S 2S 3S`.

  * So we just need to check if the prefix sum contains `S` `2S` & `3S` (in the same order). Since the sum is already `3S`, we do not need to  
worry about `3S`. All that remains is to check whether the prefix sum contains
`S` and `2S` such that `2S` is to the right of `S`.  
[ **Update** \---- We do have to check for `3S`.]

* * *

 **Observation**

  * If the accumulated sum is not a multiple of 3, then the array cannot be partitioned in such a way. This is fairly obvious from the above remark.

* * *

 **Algorithm**

  * First, calculate the entire sum. If it is not a multiple of 3, return false.
  * Intialise 3 variable, `firstFound`, `secondFound` and `thirdFound` which capture if `S`, `2S` and `3S` have appeared in the desired order or not.
  * Start calculating the prefix sum. As soon as you see `S`, set `firstFound` to true and start searching for `2S`. As soon as you see `2S`, set `secondFound` to true and start searching for `3S`.
  * In the end, if all are true, it means that `S` was found before `2S` (and `2S` was found before `3S`). Therefore, we can cut our array at the point `S` and `2S` to give us 3 parts with equal sum.

* * *

 **Update**

  * Looks like I was wrong. You do have to check the for `3S` as the subarrays may overlap if `S==2S==3S` which happens if `S==0`. So, we need to check if there exists 3 disjoint sets or not. Thanks [rock](https://leetcode.com/rock/) for pointing this out.

* * *
    
    
    class Solution
    {
    public:
        bool canThreePartsEqualSum(vector<int>& A);
    };
    
    bool Solution :: canThreePartsEqualSum(vector<int>& a)
    {
        int sum = accumulate(a.begin(), a.end(), 0);
        
        if(sum%3!=0) return false;
        
        int subSum = sum/3;
        
        bool firstFound=false, secondFound = false, thirdFound = false;
        
        int prefixSum=0;
        for(auto ele : a)
        {
            prefixSum += ele;
            if(!firstFound && prefixSum==subSum) firstFound = true;
            else if(firstFound && !secondFound && prefixSum==subSum*2) secondFound = true;
            else if(firstFound && secondFound && prefixSum==subSum*3) thirdFound = true;
        }
        
        return (firstFound && secondFound && thirdFound);
            
    }
    



