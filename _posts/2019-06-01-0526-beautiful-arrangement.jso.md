---
layout: post
title: 526. Beautiful Arrangement
---
### Question
Suppose you have **N** integers from 1 to N. We define a beautiful arrangement
as an array that is constructed by these **N** numbers successfully if one of
the following is true for the i th position (1 <= i <= N) in this array:

  1. The number at the ith position is divisible by **i**.
  2. **i** is divisible by the number at the i th position.



Now given N, how many beautiful arrangements can you construct?

 **Example 1:**

    
    
     **Input:** 2
    **Output:** 2
    **Explanation:** 
    
    The first beautiful arrangement is [1, 2]:
    
    Number at the 1st position (i=1) is 1, and 1 is divisible by i (i=1).
    
    Number at the 2nd position (i=2) is 2, and 2 is divisible by i (i=2).
    
    The second beautiful arrangement is [2, 1]:
    
    Number at the 1st position (i=1) is 2, and 2 is divisible by i (i=1).
    
    Number at the 2nd position (i=2) is 1, and i (i=2) is divisible by 1.
    



 **Note:**

  1. **N** is a positive integer and will not exceed 15.

### Solution 1
Just try every possible number at each position...

    
    
    public class Solution {
        int count = 0;
        
        public int countArrangement(int N) {
            if (N == 0) return 0;
            helper(N, 1, new int[N + 1]);
            return count;
        }
        
        private void helper(int N, int pos, int[] used) {
            if (pos > N) {
                count++;
                return;
            }
            
            for (int i = 1; i <= N; i++) {
                if (used[i] == 0 && (i % pos == 0 || pos % i == 0)) {
                    used[i] = 1;
                    helper(N, pos + 1, used);
                    used[i] = 0;
                }
            }
        }
    }
    


### Solution 2
The back tracking start from the back so that each search won't go too deep
before it fails because smaller numbers have higher chance to be divisible
among themselves. Also I don't use "visited" boolean array but use swap of an
array of 1~N to avoid duplication.

    
    
        private int count = 0;
        private void swap(int[] nums, int i, int j) {
            int tmp = nums[i];
            nums[i] = nums[j];
            nums[j] = tmp;
        }
        private void helper(int[] nums, int start) {
            if (start == 0) {
                count++;
                return;
            }
            for (int i = start; i > 0; i--) {
                swap(nums, start, i);
                if (nums[start] % start == 0 || start % nums[start] == 0) helper(nums, start-1);
                swap(nums,i, start);
            }
        }
        public int countArrangement(int N) {
            if (N == 0) return 0;
            int[] nums = new int[N+1];
            for (int i = 0; i <= N; i++) nums[i] = i;
            helper(nums, N);
            return count;
        }
    


### Solution 3
My `X` is the set of still available numbers. Gets accepted in about 230 ms:

    
    
    def countArrangement(self, N):
        def count(i, X):
            if i == 1:
                return 1
            return sum(count(i - 1, X - {x})
                       for x in X
                       if x % i == 0 or i % x == 0)
        return count(N, set(range(1, N + 1)))
    

Note that my `i` goes **downwards** , from N to 1. Because position `i = 1`
can hold **any** number, so I don't even have to check whether the last
remaining number fits there. Also, position `i = 2` happily holds every second
number and `i = 3` happily holds every third number, so filling the lowest
positions **last** has a relatively high chance of success. In other words,
it's relatively hard to end up with dead ends this way.

If I go **upwards** (from 1 to N), it takes about 2300 ms:

    
    
    def countArrangement(self, N):
        def count(i,  X):
            if i > N:
                return 1
            return sum(count(i + 1, X - {x})
                       for x in X
                       if x % i == 0 or i % x == 0)
        return count(1, set(range(1, N + 1)))



