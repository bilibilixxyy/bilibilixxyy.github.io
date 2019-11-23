---
layout: post
title: 882. Peak Index in a Mountain Array
---
### Question
Let's call an array `A` a _mountain_  if the following properties hold:

  * `A.length >= 3`
  * There exists some `0 < i < A.length - 1` such that `A[0] < A[1] < ... A[i-1] < A[i] > A[i+1] > ... > A[A.length - 1]`

Given an array that is definitely a mountain, return any `i` such that `A[0] <
A[1] < ... A[i-1] < A[i] > A[i+1] > ... > A[A.length - 1]`.

 **Example 1:**

    
    
     **Input:** [0,1,0]
    **Output:** 1
    

**Example 2:**

    
    
    **Input:** [0,2,1,0]
    **Output:** 1

 **Note:**

  1. `3 <= A.length <= 10000`
  2. ` 0 <= A[i] <= 10^6`
  3. A is a mountain, as defined above.

### Solution 1
## Approach 1, index of max, O(N)

 **C++:**

    
    
         int peakIndexInMountainArray(vector<int> A) {
           return max_element(A.begin(), A.end()) - A.begin();
        }
    

**Python:**

    
    
         def peakIndexInMountainArray(self, A):
            return A.index(max(A))
    

## Approach 2, For loop to find the first `A[i] > A[i + i]`, O(N)

**Java:**

    
    
         public int peakIndexInMountainArray(int[] A) {
            for (int i = 1; i + 1 < A.length; ++i) if (A[i] > A[i + 1]) return i;
            // for (int i = A.length - 1; i > 0; --i) if (A[i] > A[i - 1]) return i;
            return 0;
        }
    

**C++:**

    
    
        int peakIndexInMountainArray2(vector<int> A) {
             for (int i = 1; i + 1 < A.size(); ++i) if (A[i] > A[i + 1]) return i;
        }
    

## Approach 3, Binary search, O(logN)

**C++:**

    
    
         int peakIndexInMountainArray(vector<int> A) {
            int l = 0, r = A.size() - 1, mid;
            while (l < r) {
                mid = (l + r) / 2;
                if (A[mid] < A[mid + 1])
                    l = mid + 1;
                else
                    r = mid;
            }
            return l;
        }
    

**Java:**

    
    
         public int peakIndexInMountainArray(int[] A) {
            int l = 0, r = A.length - 1, m;
            while (l < r) {
                m = (l + r) / 2;
                if (A[m] < A[m + 1])
                    l = m + 1;
                else
                    r = m;
            }
            return l;
        }
    

**Python:**

    
    
        def peakIndexInMountainArray(self, A):
            l, r =  0, len(A) - 1
            while l < r:
                m = (l + r) / 2
                if A[m] < A[m + 1]:
                    l = m + 1
                else:
                    r = m
            return l
    

**Follow-up:**  
Can you do faster than binary search?

## Approach 4, Golden-section search

It's gurentee only one peak, we can apply golden-section search.

    
    
        def peakIndexInMountainArray(self, A):
            def gold1(l, r):
                return l + int(round((r - l) * 0.382))
    
            def gold2(l, r):
                return l + int(round((r - l) * 0.618))
            l, r = 0, len(A) - 1
            x1, x2 = gold1(l, r), gold2(l, r)
            while x1 < x2:
                if A[x1] < A[x2]:
                    l = x1
                    x1 = x2
                    x2 = gold1(x1, r)
                else:
                    r = x2
                    x2 = x1
                    x1 = gold2(l, x2)
            return A.index(max(A[l:r + 1]), l)
    


### Solution 2
Method 1: Straightforwad O(n) code:

    
    
        public int peakIndexInMountainArray(int[] A) {
            for (int i = 1; i < A.length; ++i) {
                if (A[i - 1] < A[i] && A[i] > A[i + 1]) { return i; }
            }
            return -1; // no peak.
        }
    

Method 2: Based on the judge condition in O(n) code, using binary search we
can implement O(log(n)) code:

    
    
        public int peakIndexInMountainArray(int[] A) {
            int lo = 0, hi = A.length - 1;
            while (lo < hi) {
                int mid = lo + (hi - lo) / 2;
                if (A[mid] < A[mid + 1]) { // peak index is after mid.
                    lo = mid + 1;
                }else if (A[mid -1] > A[mid]) { // peak index is before mid.
                    hi = mid;
                }else { // peak index is mid.
                    return mid;
                }
            }
            return -1; // no peak.
        }
    

Since the problem is guaranteed to have one and only one peak, we can simplify
the above O(n) and O(log(n)) codes:  
Method 1: O(n)

    
    
        public int peakIndexInMountainArray(int[] A) {
            for (int i = 1; i < A.length; ++i) {
                if A[i] > A[i + 1]) { return i; }
            }
            return -1; // no peak.
        }
    

Method 2: O(log(n))

    
    
        public int peakIndexInMountainArray(int[] A) {
            int lo = 0, hi = A.length - 1;
            while (lo < hi) {
                int mid = lo + (hi - lo) / 2;
                if (A[mid] < A[mid + 1]) { lo = mid + 1; } // peak index is after mid.
                else{ hi = mid; } // peak index <= mid.
            }
            return lo; // found peak index.
        }
    


### Solution 3
  * Python

    
    
    class Solution:
        def peakIndexInMountainArray(self, A):
            return A.index(max(A))
    

  * Javascript

    
    
    var peakIndexInMountainArray = function(A) {
        return A.indexOf(Math.max(...A));
    };
    



