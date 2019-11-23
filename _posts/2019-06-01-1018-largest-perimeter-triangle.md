---
layout: post
title: 1018. Largest Perimeter Triangle
---
### Question
Given an array `A` of positive lengths, return the largest perimeter of a
triangle with **non-zero area** , formed from 3 of these lengths.

If it is impossible to form any triangle of non-zero area, return `0`.



 **Example 1:**

    
    
     **Input:** [2,1,2]
    **Output:** 5
    

**Example 2:**

    
    
    **Input:** [1,2,1]
    **Output:** 0
    

**Example 3:**

    
    
    **Input:** [3,2,3,4]
    **Output:** 10
    

**Example 4:**

    
    
    **Input:** [3,6,2,3]
    **Output:** 8
    



 **Note:**

  1. `3 <= A.length <= 10000`
  2. `1 <= A[i] <= 10^6`

### Solution 1
For `a >= b >= c`, `a,b,c` can form a triangle if `a < b + c`.

  1. We sort the `A`
  2. Try to get a triangle with 3 biggest numbers.
  3. If `A[n-1] < A[n-2] + A[n-3]`, we get a triangle.  
If `A[n-1] >= A[n-2] + A[n-3] >= A[i] + A[j]`, we cannot get any triangle with
`A[n-1]`

  4. repeat step2 and step3 with the left numbers.

 **Java:**

    
    
         public int largestPerimeter(int[] A) {
            Arrays.sort(A);
            for (int i = A.length - 1; i > 1; --i)
                if (A[i] < A[i - 1] + A[i - 2])
                    return A[i] + A[i - 1] + A[i - 2];
            return 0;
        }
    

**C++:**

    
    
        int largestPerimeter(vector<int>& A) {
             sort(A.begin(), A.end());
            for (int i = A.size() - 1 ; i > 1; --i)
                if (A[i] < A[i - 1] + A[i - 2])
                    return A[i] + A[i - 1] + A[i - 2];
            return 0;
        }
    

**Python:**

    
    
        def largestPerimeter(self, A):
            A = sorted(A)[:: -1]
            for i in range(len(A) - 2):
                if A[i] < A[i + 1] + A[i + 2]:
                    return A[i] + A[i + 1] + A[i + 2]
            return 0
    


### Solution 2
**Hi fellas,  
My laptop is broken and i can't participate Leetcode. I am writing this on my
ipad and it's been more than 20 mins for two line code to write correctly,
such a pain in the ass LC mobile not responsive. Happy coding!**

    
    
    class Solution:
     def largestPerimeter(self, A):
      A.sort()
      return ([0] + [a + b + c for a, b, c in zip(A, A[1:], A[2:]) if c < a + b])[-1]
    


### Solution 3
The goal is to find the largest number n1, where two other numbers, n2 and n3,
exist, and `n1 > n2 && n1 > n3`, and `n1 < n2 + n2`.  
If we sort our sizes, we just need to find the largest A[i] such as `A[i] <
A[i - 1] + A[i - 2]`. So, we analyze triplets largest to smallest, and return
the perimeter for the first triplet that matches our criterion.

    
    
    int largestPerimeter(vector<int>& A) {
      sort(begin(A), end(A));
      for (auto i = A.size() - 1; i >= 2; --i)
        if (A[i] < A[i - 1] + A[i - 2]) return A[i] + A[i - 1] + A[i - 2];
      return 0;
    }
    



