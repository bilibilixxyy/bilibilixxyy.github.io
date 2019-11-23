---
layout: post
title: 944. Smallest Range I
---
### Question
Given an array `A` of integers, for each integer `A[i]` we may choose any `x`
with `-K <= x <= K`, and add `x` to `A[i]`.

After this process, we have some array `B`.

Return the smallest possible difference between the maximum value of `B` and
the minimum value of `B`.



 **Example 1:**

    
    
     **Input:** A = [1], K = 0
    **Output:** 0
    **Explanation** : B = [1]
    

**Example 2:**

    
    
    **Input:** A = [0,10], K = 2
    **Output:** 6
    **Explanation** : B = [2,8]
    

**Example 3:**

    
    
    **Input:** A = [1,3,6], K = 3
    **Output:** 0
    **Explanation** : B = [3,3,3] or B = [4,4,4]
    



 **Note:**

  1. `1 <= A.length <= 10000`
  2. `0 <= A[i] <= 10000`
  3. `0 <= K <= 10000`

### Solution 1
Sorry but I read the description 15 times and the solution also 2-3 times, and
one of the worst stated questions I have ever seen.


### Solution 2
 **Intuition** :  
If `min(A) + K < max(A) - K`, then return `max(A) - min(A) - 2 * K`  
If `min(A) + K >= max(A) - K`, then return `0`

 **Time Complexity** :  
O(N)

 **C++:**

    
    
         int smallestRangeI(vector<int> A, int K) {
            int mx = A[0], mn = A[0];
            for (int a : A) mx = max(mx, a), mn = min(mn, a);
            return max(0, mx - mn - 2 * K);
        }
    

**C#**

    
    
             return Math.Max(0, A.Max() - A.Min() - 2 * K);
    

**Java:**

    
    
         public int smallestRangeI(int[] A, int K) {
            int mx = A[0], mn = A[0];
            for (int a : A) {
                mx = Math.max(mx, a);
                mn = Math.min(mn, a);
            }
            return Math.max(0, mx - mn - 2 * K);
        }
    

**Python:**

    
    
             return max(0, max(A) - min(A) - 2 * K)
    


### Solution 3
First we try to find the min and max elements of A, because they contribute to
the largest possible difference and we want to minimize that. Then we check if
min+k and max-k have overlap, if they do we return 0, if not we return the
differnce of (min+k and max-k).

    
    
        public int smallestRangeI(int[] A, int k) {
            int n = A.length;
            int min = A[0];
            int max = A[0];
            
            for(int i = 0; i < n; i++){
                min = Math.min(min,A[i]);
                max = Math.max(max,A[i]);
            }
            
    
            return (min+k >= max-k) ? 0 : (max-k) - (min+k);
    
    
        }
    



