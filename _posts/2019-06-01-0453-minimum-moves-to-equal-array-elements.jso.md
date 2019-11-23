---
layout: post
title: 453. Minimum Moves to Equal Array Elements
---
### Question
Given a **non-empty** integer array of size _n_ , find the minimum number of
moves required to make all array elements equal, where a move is incrementing
_n_ \- 1 elements by 1.

 **Example:**

    
    
     **Input:**
    [1,2,3]
    
    **Output:**
    3
    
    **Explanation:**
    Only three moves are needed (remember each move increments two elements):
    
    [1,2,3]  = >  [2,3,3]  =>  [3,4,3]  =>  [4,4,4]
    

### Solution 1
let's define sum as the sum of all the numbers, before any moves; minNum as
the min number int the list; n is the length of the list;

After, say m moves, we get all the numbers as x , and we will get the
following equation

    
    
     sum + m * (n - 1) = x * n
    

and actually,

    
    
      x = minNum + m
    

This part may be a little confusing, but @shijungg explained very well. let me
explain a little again. it comes from two observations:

  1. the minum number will always be minum until it reachs the final number, because every move, other numbers (besides the max) will be increamented too;
  2. from above, we can get, the minum number will be incremented in every move. So, if the final number is x, it would be minNum + moves;

and finally, we will get

    
    
      sum - minNum * n = m
    

This is just a math calculation.


### Solution 2
Adding `1` to `n - 1` elements is the same as subtracting `1` from one
element, w.r.t goal of making the elements in the array equal.  
So, best way to do this is make all the elements in the array equal to the
`min` element.  
`sum(array) - n * minimum`

    
    
    public class Solution {
        public int minMoves(int[] nums) {
            if (nums.length == 0) return 0;
            int min = nums[0];
            for (int n : nums) min = Math.min(min, n);
            int res = 0;
            for (int n : nums) res += n - min;
            return res;
        }
    }
    


### Solution 3
Incrementing all but one is equivalent to decrementing that one. So let's do
that instead. How many single-element decrements to make all equal? No point
to decrementing below the current minimum, so how many single-element
decrements to make all equal to the current minimum? Just take the difference
from what we currently have (the sum) to what we want (n times the minimum).

Ruby:

    
    
    def min_moves(nums)
      nums.sum - nums.size * nums.min
    end
    

Python:

    
    
    def minMoves(self, nums):
        return sum(nums) - len(nums) * min(nums)
    

Java (ugh :-):

    
    
    public int minMoves(int[] nums) {
        return IntStream.of(nums).sum() - nums.length * IntStream.of(nums).min().getAsInt();
    }
    

C++ (more ugh):

    
    
    int minMoves(vector<int>& nums) {
        return accumulate(begin(nums), end(nums), 0L) - nums.size() * *min_element(begin(nums), end(nums));
    }
    

(edit: I changed 0 to 0L because it failed the apparently added testcase
[1,2147483647])



