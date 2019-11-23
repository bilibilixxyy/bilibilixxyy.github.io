---
layout: post
title: 932. Monotonic Array
---
### Question
An array is _monotonic_ if it is either monotone increasing or monotone
decreasing.

An array `A` is monotone increasing if for all `i <= j`, `A[i] <= A[j]`.  An
array `A` is monotone decreasing if for all `i <= j`, `A[i] >= A[j]`.

Return `true` if and only if the given array `A` is monotonic.



 **Example 1:**

    
    
     **Input:** [1,2,2,3]
    **Output:** true
    

**Example 2:**

    
    
    **Input:** [6,5,4,4]
    **Output:** true
    

**Example 3:**

    
    
    **Input:** [1,3,2]
    **Output:** false
    

**Example 4:**

    
    
    **Input:** [1,2,4,5]
    **Output:** true
    

**Example 5:**

    
    
    **Input:** [1,1,1]
    **Output:** true
    



 **Note:**

  1. `1 <= A.length <= 50000`
  2. `-100000 <= A[i] <= 100000`

### Solution 1
We check if `A` is increasing and decreasing.

 **C++:**

    
    
         bool isMonotonic(vector<int> A) {
            bool inc = true, dec = true;
            for (int i = 1; i < A.size(); ++i)
                inc &= A[i - 1] <= A[i], dec &= A[i - 1] >= A[i];
            return inc || dec;
        }
    

**Java:**

    
    
         public boolean isMonotonic(int[] A) {
            boolean inc = true, dec = true;
            for (int i = 1; i < A.length; ++i) {
                inc &= A[i - 1] <= A[i];
                dec &= A[i - 1] >= A[i];
            }
            return inc || dec;
        }
    

**Python:**

    
    
         def isMonotonic(self, A):
            return not {cmp(i, j) for i, j in zip(A, A[1:])} >= {1, -1}
    


### Solution 2
    
    
    class Solution(object):
        def isMonotonic(self, A):
            """
            :type A: List[int]
            :rtype: bool
            """
    
            n = len(A)
            if n <= 2: return True
    				
            isGreat = False
            isLess = False
            for i in range(1, n):
                if A[i - 1] > A[i]:
                    isGreat = True
                if A[i - 1] < A[i]:
                    isLess = True
    
                if isGreat and isLess:
                    return False
    
            return True
    


### Solution 3
  1. Find the trend based on A[0] and A[n-1]
  2. Check if the consecutive neighbors are consistent with the trend.

    
    
    class Solution {
        public boolean isMonotonic(int[] A) {
            if (A.length==1) return true;
            int n=A.length;
            boolean up= (A[n-1]-A[0])>0;
            for (int i=0; i<n-1; i++)
                if (A[i+1]!=A[i] && (A[i+1]-A[i]>0)!=up) 
                    return false;
            return true;
        }
    }
    



