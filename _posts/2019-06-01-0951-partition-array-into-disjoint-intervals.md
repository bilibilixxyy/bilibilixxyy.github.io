---
layout: post
title: 951. Partition Array into Disjoint Intervals
---
### Question
Given an array `A`, partition it into two (contiguous) subarrays `left` and
`right` so that:

  * Every element in `left` is less than or equal to every element in `right`.
  * `left` and `right` are non-empty.
  * `left` has the smallest possible size.

Return the **length** of `left` after such a partitioning.  It is guaranteed
that such a partitioning exists.



 **Example 1:**

    
    
     **Input:** [5,0,3,8,6]
    **Output:** 3
    **Explanation:** left = [5,0,3], right = [8,6]
    

**Example 2:**

    
    
    **Input:** [1,1,1,0,6,12]
    **Output:** 4
    **Explanation:** left = [1,1,1,0], right = [6,12]
    



 **Note:**

  1. `2 <= A.length <= 30000`
  2. `0 <= A[i] <= 10^6`
  3. It is guaranteed there is at least one way to partition `A` as described.

### Solution 1
    
    
    public int partitionDisjoint(int[] a) {
            int localMax = a[0], partitionIdx = 0, max = localMax;
            for (int i = 1; i < a.length; i++)
                if (localMax > a[i]) {
                    localMax = max;
                    partitionIdx = i;
                } else max = Math.max(max, a[i]);
            return partitionIdx + 1;
        }


### Solution 2
`B[i]` stands for the minimum value in subarray `A[i], A[i+1], ..., A[n-1]`  
`pmax` stands for the prefix maximum of `i` first values in `A`.  
return the smallest that `i` that `pmax <= B[i]`

 **C++:**

    
    
        int partitionDisjoint(vector<int>& A) {
            int n = A. size(), pmax = 0;
            vector<int> B(n);
            B[n - 1] = A[n - 1];
            for (int i = n - 2; i > 0; --i)
                B[i] = min(A[i], B[i + 1]);
            for (int i = 1; i < n; ++i) {
                pmax = max(pmax, A[i - 1]);
                if (pmax <= B[i]) return i;
            }
        }
    

**Java:**

    
    
         public int partitionDisjoint(int[] A) {
            int n = A.length, pmax = 0, B[] = new int[n];
            B[n - 1] = A[n - 1];
            for (int i = n - 2; i > 0; --i)
                B[i] = Math.min(A[i], B[i + 1]);
            for (int i = 1; i < n; ++i) {
                pmax = Math.max(pmax, A[i - 1]);
                if (pmax <= B[i]) return i;
            }
            return n;
        }
    

**Python:**

    
    
        def partitionDisjoint(self, A):
            B = A[:]
            n = len(A)
             for i in range(n - 1)[::-1]:
                B[i] = min(A[i], B[i + 1])
            pmax = 0
            for i in range(1,n):
                pmax = max(pmax, A[i-1])
                if pmax <= B[i]:
                    return i
    


### Solution 3
Looping through each element `A[i]` we will keep track of the `max_so_far` and
`disjoint` index. If we see a value `A[i] < max_so_far` we know that value
must be in the left partition, so we update the `disjoint` location and check
if we have a new `max_so_far` in the left partition. Once we go through the
list, every element on the right side of `disjoint` is guarenteed to be larger
than elements left of `disjoint`

    
    
    def partitionDisjoint(self, A):
            disjoint = 0
            v = A[disjoint]
            max_so_far = v
            for i in range(len(A)):
                max_so_far = max(max_so_far, A[i])
                if A[i] < v: 
                    disjoint = i
                    v = max_so_far
            return disjoint + 1
    



