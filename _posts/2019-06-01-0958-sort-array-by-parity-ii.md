---
layout: post
title: 958. Sort Array By Parity II
---
### Question
Given an array `A` of non-negative integers, half of the integers in A are
odd, and half of the integers are even.

Sort the array so that whenever `A[i]` is odd, `i` is odd; and whenever `A[i]`
is even, `i` is even.

You may return any answer array that satisfies this condition.



 **Example 1:**

    
    
     **Input:** [4,2,5,7]
    **Output:** [4,5,2,7]
    **Explanation:** [4,7,2,5], [2,5,4,7], [2,7,4,5] would also have been accepted.
    



 **Note:**

  1. `2 <= A.length <= 20000`
  2. `A.length % 2 == 0`
  3. `0 <= A[i] <= 1000`

### Solution 1
    
    
    class Solution {
        public int[] sortArrayByParityII(int[] A) {
            int  i = 0, j = 1, n = A.length;
            while (i < n && j < n) {
                while (i < n && A[i] % 2 == 0) {
                    i += 2;
                }
                while (j < n && A[j] % 2 == 1) {
                    j += 2;
                }
                if (i < n && j < n) {
                    swap(A, i, j);
                }
            }
            return A;
        }
        private void swap(int[] A, int i, int j) {
            int temp = A[i];
            A[i] = A[j];
            A[j] = temp;
        }
    }
    
    


### Solution 2
    
    
    class Solution:
        def sortArrayByParityII(self, a):
            i = 0 # pointer for even misplaced
            j = 1 # pointer for odd misplaced
            sz = len(a)
            
            # invariant: for every misplaced odd there is misplaced even
            # since there is just enough space for odds and evens
    
            while i < sz and j < sz:
                if a[i] % 2 == 0:
                    i += 2
                elif a[j] % 2 == 1:
                    j += 2
                else:
                    # a[i] % 2 == 1 AND a[j] % 2 == 0
                    a[i],a[j] = a[j],a[i]
                    i += 2
                    j += 2
    
            return a
    


### Solution 3
Use two pointers to search for missplaced odd and even elements, and swap
them.

    
    
    vector<int> sortArrayByParityII(vector<int>& A) {
        for (int i = 0, j = 1; i < A.size(); i += 2, j += 2) {
            while (i < A.size() && A[i] % 2 == 0) i += 2;
            while (j < A.size() && A[j] % 2 == 1) j += 2;
            if (i < A.size()) swap(A[i], A[j]);
        }
        return A;
    }
    

Now, some fun for for my minimalistic functional friends. It's techically a
two-liner, though I split `swap` into 3 lines for readability :)  
It actually may even look a bit cleaner. You do not have to do "plus 2", and
swap seems to work for `end` pointers.

    
    
    vector<int> sortArrayByParityII(vector<int>& A) {
      for (int i = 0, j = 0; i < A.size(); ) swap(
          *find_if(begin(A) + i, end(A), [&i] (int v) { return i++ % 2 == 0 && v % 2 != 0; }),
          *find_if(begin(A) + j, end(A), [&j] (int v) { return j++ % 2 != 0 && v % 2 == 0; }));
      return A;
    } 
    



