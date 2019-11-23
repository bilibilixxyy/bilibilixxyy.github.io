---
layout: post
title: 334. Increasing Triplet Subsequence
---
### Question
Given an unsorted array return whether an increasing subsequence of length 3
exists or not in the array.

Formally the function should:

> Return true if there exists _i, j, k_  
>  such that _arr[i]_ < _arr[j]_ < _arr[k]_ given 0 ≤ _i_ < _j_ < _k_ ≤ _n_ -1
else return false.

 **Note:** Your algorithm should run in O( _n_ ) time complexity and O( _1_ )
space complexity.

 **Example 1:**

    
    
     **Input:** [1,2,3,4,5]
    **Output:** true
    

**Example 2:**

    
    
    **Input:** [5,4,3,2,1]
    **Output:** false
    

### Solution 1
    
    
        public boolean increasingTriplet(int[] nums) {
            // start with two largest values, as soon as we find a number bigger than both, while both have been updated, return true.
            int small = Integer.MAX_VALUE, big = Integer.MAX_VALUE;
            for (int n : nums) {
                if (n <= small) { small = n; } // update small if n is smaller than both
                else if (n <= big) { big = n; } // update big only if greater than small but smaller than big
                else return true; // return if you find a number bigger than both
            }
            return false;
        }


### Solution 2
    
    
    bool increasingTriplet(vector<int>& nums) {
        int c1 = INT_MAX, c2 = INT_MAX;
        for (int x : nums) {
            if (x <= c1) {
                c1 = x;           // c1 is min seen so far (it's a candidate for 1st element)
            } else if (x <= c2) { // here when x > c1, i.e. x might be either c2 or c3
                c2 = x;           // x is better than the current c2, store it
            } else {              // here when we have/had c1 < c2 already and x > c2
                return true;      // the increasing subsequence of 3 elements exists
            }
        }
        return false;
    }


### Solution 3
Start with the maximum numbers for the first and second element. Then:  
(1) Find the first smallest number in the 3 subsequence  
(2) Find the second one greater than the first element, reset the first one if
it's smaller

    
    
    def increasingTriplet(nums):
        first = second = float('inf')
        for n in nums:
            if n <= first:
                first = n
            elif n <= second:
                second = n
            else:
                return True
        return False



