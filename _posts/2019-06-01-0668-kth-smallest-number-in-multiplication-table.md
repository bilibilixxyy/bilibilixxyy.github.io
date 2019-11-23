---
layout: post
title: 668. Kth Smallest Number in Multiplication Table
---
### Question
Nearly every one have used the [Multiplication
Table](https://en.wikipedia.org/wiki/Multiplication_table). But could you find
out the `k-th` smallest number quickly from the multiplication table?

Given the height `m` and the length `n` of a `m * n` Multiplication Table, and
a positive integer `k`, you need to return the `k-th` smallest number in this
table.

 **Example 1:**  

    
    
     **Input:** m = 3, n = 3, k = 5
    **Output:** 
    **Explanation:** 
    The Multiplication Table:
    1	2	3
    2	4	6
    3	6	9
    
    The 5-th smallest number is 3 (1, 2, 2, 3, 3).
    

**Example 2:**  

    
    
    **Input:** m = 2, n = 3, k = 6
    **Output:** 
    **Explanation:** 
    The Multiplication Table:
    1	2	3
    2	4	6
    
    The 6-th smallest number is 6 (1, 2, 2, 3, 4, 6).
    

**Note:**  

  1. The `m` and `n` will be in the range [1, 30000].
  2. The `k` will be in the range [1, m * n]

### Solution 1
    
    
     class Solution {
        public int findKthNumber(int m, int n, int k) {
        	int low = 1 , high = m * n + 1;
            
        	while (low < high) {
        	    int mid = low + (high - low) / 2;
        	    int c = count(mid, m, n);
        	    if (c >= k) high = mid;
                else low = mid + 1;
        	}
            
        	return high;
        }
        
        private int count(int v, int m, int n) {
    	int count = 0;
    	for (int i = 1; i <= m; i++) {
    	    int temp = Math.min(v / i , n);
    	    count += temp;
    	}
    	return count;
        }
    }
    


### Solution 2
Let's binary search for the answer `A`.

Say `enough(x)` is true if and only if there are `k` or more values in the
multiplication table that are less than or equal to `x`. Colloquially,
`enough` describes whether `x` is large enough to be the `k-th` value in the
multiplication table.

Then (for our answer `A`), whenever `x >= A`, `enough(x)` is `True`; and
whenever `x < A`, `enough(x)` is `False`.

In our binary search, our loop invariant is that `enough(hi) = True`. More
specifically, if we were to apply `enough` onto each argument in the interval
`[lo, hi]`, we would see 0 or more `False`, followed by 1 or more `True`. Once
`lo == hi`, we know that `enough(lo) = True`, and it must have been the
smallest such one, because `lo` must have been `hi-1` or `hi-2` at some point,
and `mi = hi-1` would have been checked.

This leaves us with the task of counting how many values are less than or
equal to `x`. For each of `m` rows, the `i`-th row looks like `[i, 2*i, 3*i,
..., n*i]`, and there are `min(x // i, n)` values in that row that are less
than or equal to `x`.

    
    
    def findKthNumber(self, m, n, k):
        def enough(x):
            return sum(min(x / i, n) for i in xrange(1, m+1)) >= k
    
        lo, hi = 1, m*n
        while lo < hi:
            mi = (lo + hi) / 2
            if not enough(mi):
                lo = mi + 1
            else:
                hi = mi
        return lo
    


### Solution 3
The name and description are very misleading. This should be the kth smallest
number.



