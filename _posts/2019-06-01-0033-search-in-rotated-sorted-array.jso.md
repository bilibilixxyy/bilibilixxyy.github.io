---
layout: post
title: 33. Search in Rotated Sorted Array
---
### Question
Suppose an array sorted in ascending order is rotated at some pivot unknown to
you beforehand.

(i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`).

You are given a target value to search. If found in the array return its
index, otherwise return `-1`.

You may assume no duplicate exists in the array.

Your algorithm's runtime complexity must be in the order of  _O_ (log  _n_ ).

 **Example 1:**

    
    
     **Input:** nums = [4,5,6,7,0,1,2], target = 0
    **Output:** 4
    

**Example 2:**

    
    
    **Input:** nums = [4,5,6,7,0,1,2], target = 3
    **Output:** -1

### Solution 1
    
    
     class Solution {
    public:
        int search(int A[], int n, int target) {
            int lo=0,hi=n-1;
            // find the index of the smallest value using binary search.
            // Loop will terminate since mid < hi, and lo or hi will shrink by at least 1.
            // Proof by contradiction that mid < hi: if mid==hi, then lo==hi and loop would have been terminated.
            while(lo<hi){
                int mid=(lo+hi)/2;
                if(A[mid]>A[hi]) lo=mid+1;
                else hi=mid;
            }
            // lo==hi is the index of the smallest value and also the number of places rotated.
            int rot=lo;
            lo=0;hi=n-1;
            // The usual binary search and accounting for rotation.
            while(lo<=hi){
                int mid=(lo+hi)/2;
                int realmid=(mid+rot)%n;
                if(A[realmid]==target)return realmid;
                if(A[realmid]<target)lo=mid+1;
                else hi=mid-1;
            }
            return -1;
        }
    };


### Solution 2
This very nice idea is from [rantos22's
solution](https://leetcode.com/discuss/66853/c-4-lines-4ms) who sadly only
commented _"You are not expected to understand that :)"_ , which I guess is
the reason it's now it's hidden among the most downvoted solutions. I present
an explanation and a more usual implementation.

* * *

 **Explanation**

Let's say `nums` looks like this: [12, 13, 14, 15, 16, 17, 18, 19, 0, 1, 2, 3,
4, 5, 6, 7, 8, 9, 10, 11]

Because it's not fully sorted, we can't do normal binary search. But here
comes the trick:

  * If target is let's say 14, then we adjust `nums` to this, where "inf" means infinity:  
[12, 13, 14, 15, 16, 17, 18, 19, inf, inf, inf, inf, inf, inf, inf, inf, inf,
inf, inf, inf]

  * If target is let's say 7, then we adjust `nums` to this:  
[-inf, -inf, -inf, -inf, -inf, -inf, -inf, -inf, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9,
10, 11]

And then we can simply do ordinary binary search.

Of course we don't actually adjust the whole array but instead adjust only on
the fly only the elements we look at. And the adjustment is done by comparing
both the target and the actual element against nums[0].

* * *

 **Code**

If `nums[mid]` and `target` are _"on the same side"_ of `nums[0]`, we just
take `nums[mid]`. Otherwise we use -infinity or +infinity as needed.

    
    
     int search(vector<int>& nums, int target) {
        int lo = 0, hi = nums.size();
        while (lo < hi) {
            int mid = (lo + hi) / 2;
            
            double num = (nums[mid] < nums[0]) == (target < nums[0])
                       ? nums[mid]
                       : target < nums[0] ? -INFINITY : INFINITY;
                       
            if (num < target)
                lo = mid + 1;
            else if (num > target)
                hi = mid;
            else
                return mid;
        }
        return -1;
    }


### Solution 3
    
    
    public class Solution {
    public int search(int[] A, int target) {
        int lo = 0;
        int hi = A.length - 1;
        while (lo < hi) {
            int mid = (lo + hi) / 2;
            if (A[mid] == target) return mid;
            
            if (A[lo] <= A[mid]) {
                if (target >= A[lo] && target < A[mid]) {
                    hi = mid - 1;
                } else {
                    lo = mid + 1;
                }
            } else {
                if (target > A[mid] && target <= A[hi]) {
                    lo = mid + 1;
                } else {
                    hi = mid - 1;
                }
            }
        }
        return A[lo] == target ? lo : -1;
    }
    

}



