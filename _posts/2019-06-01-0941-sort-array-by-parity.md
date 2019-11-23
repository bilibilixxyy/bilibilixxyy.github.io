---
layout: post
title: 941. Sort Array By Parity
---
### Question
Given an array `A` of non-negative integers, return an array consisting of all
the even elements of `A`, followed by all the odd elements of `A`.

You may return any answer array that satisfies this condition.



 **Example 1:**

    
    
     **Input:** [3,1,2,4]
    **Output:** [2,4,3,1]
    The outputs [4,2,3,1], [2,4,1,3], and [4,2,1,3] would also be accepted.
    



 **Note:**

  1. `1 <= A.length <= 5000`
  2. `0 <= A[i] <= 5000`

### Solution 1
Return a new array is not difficult.  
Here I shared a solution if in-place swap asked.

 **Time Complexity** :  
O(N)

 **C++:**

    
    
        vector< int> sortArrayByParity(vector<int> &A) {
            for (int i = 0, j = 0; j < A.size(); j++)
                if (A[j] % 2 == 0) swap(A[i++], A[j]);
            return A;
        }
    

**Java:**

    
    
        public int[] sortArrayByParity(int[] A) {
             for (int i = 0, j = 0; j < A.length; j++)
                if (A[j] % 2 == 0) {
                    int tmp = A[i];
                    A[i++] = A[j];
                    A[j] = tmp;;
                }
            return A;
        }
    


### Solution 2
_**C++**_

    
    
    vector<int> sortArrayByParity(vector<int>& A) {
            int  size = A.size();
            vector<int> res(size, 0);
            int start = 0, end = size - 1;
            for (int i = 0; i < size; i++) {
                if (A[i] % 2 == 1) {
                    res[end--] = A[i];
                } else {
                    res[start++] = A[i];
                }
            }
            return res;
        }
    

_**Python**_

    
    
     class Solution(object):
        def sortArrayByParity(self, A):
            """
            :type A: List[int]
            :rtype: List[int]
            """
            size = len(A)
            res = [None] * size
            start = 0
            end = size - 1
            for val in A:
                if val % 2 == 1:
                    res[end] = val
                    end = end -1
                else:
                    res[start] = val
                    start = start + 1
            return res
    

_**Java**_

    
    
     class Solution {
        public int[] sortArrayByParity(int[] A) {
            int size = A.length;
            int start = 0, end = size - 1;
            int[] res = new int[size];
            for (int i = 0; i < size; i++) {
                if (A[i] % 2 == 1) {
                    res[end--] = A[i];
                } else {
                    res[start++] = A[i];
                }
            }
            return res;
        }
    }
    


### Solution 3
    
    
    class Solution {
        public int[] sortArrayByParity(int[] A) {
            int[] arr = new int[A.length];
            int begin = 0;
            int end = A.length - 1;
            for(int i = 0;i < A.length;i++){
                //[3,1,2,4]
                if(A[i] % 2 == 0){
                    arr[begin++] = A[i];
                }else{
                    arr[end--] = A[i];
                }
            }
            return arr;
        }
    }



