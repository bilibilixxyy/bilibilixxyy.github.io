---
layout: post
title: 1168. Duplicate Zeros
---
### Question
Given a fixed length array `arr` of integers, duplicate each occurrence of
zero, shifting the remaining elements to the right.

Note that elements beyond the length of the original array are not written.

Do the above modifications to the input array **in place** , do not return
anything from your function.



 **Example 1:**

    
    
     **Input:** [1,0,2,3,0,4,5,0]
    **Output:** null
    **Explanation:** After calling your function, the **input** array is modified to:  [1,0,0,2,3,0,0,4]
    

**Example 2:**

    
    
    **Input:** [1,2,3]
    **Output:** null
    **Explanation:** After calling your function, the **input** array is modified to:  [1,2,3]
    



 **Note:**

  1. `1 <= arr.length <= 10000`
  2. `0 <= arr[i] <= 9`

### Solution 1
#  **Intuition**

The problem can be easy solved:

  1. with a copy (extra space)
  2. by inserting zeros (extra time)  
  

 **Python, with extra space**

    
    
         def duplicateZeros(self, A):
            A[:] = [x for a in A for x in ([a] if a else [0, 0])][:len(A)]
    

  

# **Explanation**

We can improve it to `O(N)` time and `O(1)` space.  
Basically, we apply two pointers.  
`i` is the position in the original array,  
`j` is the position in the new array.  
(the original and the new are actually the same array.)

The first we pass forward and count the zeros.  
The second we pass backward and assign the value from original input to the
new array.  
so that the original value won't be overridden too early.  
  

**C++:**

    
    
        void duplicateZeros(vector<int>& A) {
            int n = A. size(), j = n + count(A.begin(), A.end(), 0);
            for (int i = n - 1; i >= 0; --i) {
                if (--j < n)
                    A[j] = A[i];
                if (A[i] == 0 && --j < n)
                    A[j] = 0;
            }
        }
    

**Java**  
Version suggested by @davidluoyes

    
    
         public void duplicateZeros(int[] arr) {
            int countZero = 0;
            for (int i = 0; i < arr.length; i++) {
                if (arr[i] == 0) countZero++;
            }
            int len = arr.length + countZero;
            //We just need O(1) space if we scan from back
            //i point to the original array, j point to the new location
            for (int i = arr.length - 1, j = len - 1; i >= 0 && j >= 0; i--, j--) {
                if (arr[i] != 0) {
                    if (j < arr.length) arr[j] = arr[i];
                } else {
                    if (j < arr.length) arr[j] = arr[i];
                    j--;
                    if (j < arr.length) arr[j] = arr[i]; //copy twice when hit '0'
                }
            }
        }
    


### Solution 2
First, go left to right and count how many shifts (`sh`) we can fit in our
array.  
Then, go right to left and move items; if it's zero - duplicate it and
decrement the shift.

> Note: `i + sh` can exceed the array size. We need a check for this case.

    
    
    void duplicateZeros(vector<int>& a, int i = 0, int sh = 0) {
      for (i = 0; sh + i < a.size(); ++i) sh += a[i] == 0;
      for (i = i - 1; sh > 0; --i) {
        if (i + sh < a.size()) a[i + sh] = a[i];
        if (a[i] == 0) a[i + --sh] = a[i];
      }
    }
    

Java version:

    
    
    public void duplicateZeros(int[] a) {
      int i = 0, sh = 0;
      for (i = 0; sh + i < a.length; ++i) sh += a[i] == 0 ? 1 : 0;
      for (i = i - 1; sh > 0; --i) {
        if (i + sh < a.length) a[i + sh] = a[i];
        if (a[i] == 0) a[i + --sh] = a[i];
      }
    }
    


### Solution 3
**First pass** to count the number of 0's.  
 **Second pass** is to write in the values in appropriate locations, moving
from right to left (backwards from the usual), in which the `write` pointer
initially extends outside of the length of the array (as if it was the full
sized array without erasing values in the shift).

    
    
       /*
      O(n) runtime
      O(1) space
      */
      public void duplicateZeros(int[] A) {
        
        int n = A.length, count = 0;
        
        for (int num : A) if (num == 0) count++;
        int i = n - 1;
        int write = n + count - 1;
        
        while (i >= 0 && write >= 0)  {
          
          if (A[i] != 0) { // Non-zero, just write it in
            if (write < n) A[write] = A[i];
            
          } else { // Zero found, write it in twice if we can
            if (write < n) A[write] = A[i];
            write--;
            if (write < n) A[write] = A[i];
          }
          
          i--;
          write--;
        }
      }
    



