---
layout: post
title: 1185. Find in Mountain Array
---
### Question
 _(This problem is an   **interactive problem**.)_

You may recall that an array `A` is a _mountain array_ if and only if:

  * `A.length >= 3`
  * There exists some `i` with `0 < i < A.length - 1` such that: 
    * `A[0] < A[1] < ... A[i-1] < A[i]`
    * `A[i] > A[i+1] > ... > A[A.length - 1]`

Given a mountain array `mountainArr`, return the **minimum**  `index` such
that `mountainArr.get(index) == target`.  If such an `index` doesn't exist,
return `-1`.

 **You can't access the mountain array directly.**   You may only access the
array using a `MountainArray` interface:

  * `MountainArray.get(k)` returns the element of the array at index `k` (0-indexed).
  * `MountainArray.length()` returns the length of the array.

Submissions making more than `100` calls to `MountainArray.get` will be judged
_Wrong Answer_.   Also, any solutions that attempt to circumvent the judge
will result in disqualification.



 **Example 1:**

    
    
     **Input:** array = [1,2,3,4,5,3,1], target = 3
    **Output:** 2
    **Explanation:** 3 exists in the array, at index=2 and index=5. Return the minimum index, which is 2.

**Example 2:**

    
    
    **Input:** array = [0,1,2,4,2,1], target = 3
    **Output:** -1
    **Explanation:** 3 does not exist in the array, so we return -1.
    



 **Constraints:**

  1. `3 <= mountain_arr.length() <= 10000`
  2. `0 <= target <= 10^9`
  3. `0 <= mountain_arr.get(index) <= 10^9`

### Solution 1
Triple Binary Search, Triple Happiness.

##  **Intuition**

  1. Binary find peak in the mountain.  
[852\. Peak Index in a Mountain Array](https://leetcode.com/problems/peak-
index-in-a-mountain-array/discuss/139848/C%2B%2BJavaPython-Better-than-Binary-
Search/294082)

  2. Binary find the target in strict increasing array
  3. Binary find the target in strict decreasing array  
  
  
Personally, (just a tip)  
If I want find the index, I always use `while (left < right)`  
If I may return the index during the search, I'll use `while (left <= right)`  
  

##  **Complexity**

Time `O(logN)` Space `O(1)`  
  

##  **Some Improvement**

  1. Cache the result of `get`, in case we make the same calls.  
In sacrifice of `O(logN)` space for the benefit of less calls.

  2. Binary search of peak is unnecessary, just easy to write.  
  

 **C++/Java**

    
    
         int findInMountainArray(int target, MountainArray A) {
            int n = A.length(), l, r, m, peak = 0;
            // find index of peak
            l  = 0;
            r = n - 1;
            while (l < r) {
                m = (l + r) / 2;
                if (A.get(m) < A.get(m + 1))
                    l = peak = m + 1;
                else
                    r = m;
            }
            // find target in the left of peak
            l = 0;
            r = peak;
            while (l <= r) {
                m = (l + r) / 2;
                if (A.get(m) < target)
                    l = m + 1;
                else if (A.get(m) > target)
                    r = m - 1;
                else
                    return m;
            }
            // find target in the right of peak
            l = peak;
            r = n - 1;
            while (l <= r) {
                m = (l + r) / 2;
                if (A.get(m) > target)
                    l = m + 1;
                else if (A.get(m) < target)
                    r = m - 1;
                else
                    return m;
            }
            return -1;
        }
    

**Python:**

    
    
        def find InMountainArray(self, target, A):
            n = len(A)
            # find index of peak
            l, r = 0, n - 1
            while l < r:
                m = (l + r) / 2
                if A.get(m) < A.get(m + 1):
                    l = peak = m + 1
                else:
                    r = m
            # find target in the left of peak
            l, r = 0, peak
            while l <= r:
                m = (l + r) / 2
                if A.get(m) < target:
                    l = m + 1
                elif A.get(m) > target:
                    r = m - 1
                else:
                    return m
            # find target in the right of peak
            l, r = peak, n - 1
            while l <= r:
                m = (l + r) / 2
                if A.get(m) > target:
                    l = m + 1
                elif A.get(m) < target:
                    r = m - 1
                else:
                    return m
            return -1
    


### Solution 2
First, find the peak as described in [162\. Find Peak
Element](https://leetcode.com/problems/find-peak-element/).

Then, do the binary search for the first range. If the element is not found,
do the binary search again for the second range (note that the second range is
descending).

    
    
    int findInMountainArray(int t, MountainArray& arr) {
      auto peak = 0, r = arr.length() - 1;
      while (peak < r) {
        int m = (peak + r) / 2;
        if (arr.get(m) > arr.get(m + 1)) r = m;
        else peak = m + 1;
      }
      auto i = bSearch(arr, t, 0, peak);
      return i != -1 ? i : bSearch(arr, t, peak + 1, arr.length() - 1, false);
    }
    int bSearch(MountainArray& arr, int t, int l, int r, bool asc = true) {
      while (l <= r) {
        int m = (l + r) / 2;
        auto val = arr.get(m);
        if (val == t) return m;
        if (asc == val < t) l = m + 1;
        else r = m - 1;
      }
      return -1;
    }
    


### Solution 3
    
    
    /**
     * // This is the MountainArray's API interface.
     * // You should not implement it, or speculate about its implementation
     * function MountainArray() {
     *
     *     @param {integer} index
     *     @return {integer}
     *     this.get = function(index) {
     *         ...
     *     };
     *
     *     @return {integer}
     *     this.length = function() {
     *         ...
     *     };
     * };
     */
    /**
     * @param {number} target
     * @param {MountainArray} mountainArr
     * @return {number}
     */
    var findInMountainArray = function(target, mountainArr) {
    	let start = 0, end = mountainArr.length();
    	let mid = 0;
    
    	while (start < end) {
    		mid = start + ((end - start) >> 1);
    		let tmp = mountainArr.get(mid);
    		let tmp1 = mountainArr.get(mid+1);
    		let tmp0 = mountainArr.get(mid-1)
    		if (mid > 0 && tmp > tmp1 && tmp > tmp0) {
    			break;
    		}
    		if (tmp < tmp1) {
    			start = mid+1;
    		} else {
    			end = mid;
    		}
    	}
    
    	start = 0, end = mid;
    
    	while (start < end) {
    		let m = start + ((end - start) >> 1);
    		let tmp = mountainArr.get(m);
    		if (tmp === target) return m;
    		if (tmp < target) {
    			start = m+1;
    		} else {
    			end = m;
    		}
    	}
    
    	start = mid, end = mountainArr.length()-1;
    	while (start < end) {
    		let m = start + ((end - start) >> 1);
    		let tmp = mountainArr.get(m);
    		if (tmp === target) return m;
    		if (tmp < target) {
    			end = m;
    		} else {
    			start = m + 1;
    		}
    	}
    
    	return -1;
    };



