---
layout: post
title: 978. Valid Mountain Array
---
### Question
Given an array `A` of integers, return `true` if and only if it is a _valid
mountain array_.

Recall that A is a mountain array if and only if:

  * `A.length >= 3`
  * There exists some `i` with `0 < i < A.length - 1` such that: 
    * `A[0] < A[1] < ... A[i-1] < A[i] `
    * `A[i] > A[i+1] > ... > A[A.length - 1]`



 **Example 1:**

    
    
     **Input:** [2,1]
    **Output:** false
    

**Example 2:**

    
    
    **Input:** [3,5,5]
    **Output:** false
    

**Example 3:**

    
    
    **Input:** [0,3,2,1]
    **Output:** true



 **Note:**

  1. `0 <= A.length <= 10000`
  2. `0 <= A[i] <= 10000 `

### Solution 1
Two people climb from left and from right separately.  
If they are climbing the same mountain,  
they will meet at the same point.

 **C++:**

    
    
        bool validMountainArray(vector<int>& A) {
            int n = A. size(), i = 0, j = n - 1;
            while (i + 1 < n && A[i] < A[i + 1]) i++;
            while (j > 0 && A[j - 1] > A[j]) j--;
            return i > 0 && i == j && j < n - 1;
        }
    

**Java:**

    
    
        public boolean validMountainArray(int[] A) {
            int n = A. length, i = 0, j = n - 1;
            while (i + 1 < n && A[i] < A[i + 1]) i++;
            while (j > 0 && A[j - 1] > A[j]) j--;
            return i > 0 && i == j && j < n - 1;
        }
    

**Python:**

    
    
        def validMountainArray(self, A):
            i, j, n =  0, len(A) - 1, len(A)
            while i + 1 < n and A[i] < A[i + 1]: i += 1
            while j > 0 and A[j - 1] > A[j]: j -= 1
            return 0 < i == j < n - 1
    


### Solution 2
We are ascending until we find the peak, then descending. If we start
ascending again after we found the peak, the mountain array is invalid.

The `&` operation will produce `true` only when the peak has been reached and
there is an ascend (both conditions are true).

    
    
    bool validMountainArray(vector<int>& A) {
        if (A.size() < 3 || A[0] > A[1]) return false;
        auto peak = false;
        for (auto i = 1; i < A.size(); ++i) {
            if (peak & A[i - 1] < A[i] || A[i - 1] == A[i]) return false;
            peak = A[i - 1] > A[i];
        }
        return peak;
    }
    


### Solution 3
    
    
    class Solution:
        def validMountainArray(self, A):
            """
            :type A: List[int]
            :rtype: bool
            """
            if len(A)<3 or A == sorted(set(A)) or A[::-1] == sorted(set(A)):
                return False
    
            l = A.index(max(A))+1
    
            return A[:l] == sorted(set(A[:l])) and A[l-1:][::-1] == sorted(set(A[l-1:]))
    



