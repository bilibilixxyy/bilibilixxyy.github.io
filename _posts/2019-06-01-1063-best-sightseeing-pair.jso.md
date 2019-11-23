---
layout: post
title: 1063. Best Sightseeing Pair
---
### Question
Given an array `A` of positive integers, `A[i]` represents the value of the
`i`-th sightseeing spot, and two sightseeing spots `i` and `j` have distance
`j - i` between them.

The _score_  of a pair (`i < j`) of sightseeing spots is (`A[i] + A[j] + i -
j)` : the sum of the values of the sightseeing spots, **minus** the distance
between them.

Return the maximum score of a pair of sightseeing spots.



 **Example 1:**

    
    
     **Input:** [8,1,5,2,6]
    **Output:** 11
    **Explanation:** i = 0, j = 2, A[i] + A[j] + i - j = 8 + 5 + 0 - 2 = 11
    



 **Note:**

  1. `2 <= A.length <= 50000`
  2. `1 <= A[i] <= 1000`

### Solution 1
Hope some explanation in this
[video](https://www.youtube.com/watch?v=AxVUCzee-XI) (chinese) can help.  
The link is good, preview is somehow broken on the Leetcode.

##  **Intuition** :

Count the current best score in all previous sightseeing spot.  
Note that, as we go further, the score of previous spot decrement.

##  **Explanation**

`cur` will record the best score that we have met.  
We iterate each value `a` in the array `A`,  
update `res` by `max(res, cur + a)`

Also we can update `cur` by `max(cur, a)`.  
Note that when we move forward,  
all sightseeing spot we have seen will be 1 distance further.

So for the next sightseeing spot `cur = Math.max(cur, a) **- 1**`

It's kinds of like, "A near neighbor is better than a distant cousin."

##  **Time Complexity** :

One pass, `O(N)` time, `O(1)` space

  

 **Java:**

    
    
         public int maxScoreSightseeingPair(int[] A) {
            int res = 0, cur = 0;
            for (int a: A) {
                res = Math.max(res, cur + a);
                cur = Math.max(cur, a) - 1;
            }
            return res;
        }
    

**C++:**

    
    
         int maxScoreSightseeingPair(vector<int>& A) {
            int res = 0, cur = 0;
            for (int a: A) {
                res = max(res, cur + a);
                cur = max(cur, a) - 1;
            }
            return res;
        }
    

**Python:**

    
    
        def maxScoreSightseeingPair(self, A):
            cur = res =  0
            for a in A:
                res = max(res, cur + a)
                cur = max(cur, a) - 1
            return res
    

**Python:**

    
    
        def maxScoreSightseeingPair(self, A):
            cur = res =  0
            for a in A:
                res = max(res, cur + a)
                cur = max(cur, a) - 1
            return res
    

**Python 1-line:**

    
    
        def maxScoreSightseeingPair( self, A):
            return reduce(lambda (r, c), a: [max(r, c + a), max(c, a) - 1], A, [0, 0])[0]
    


### Solution 2
## Intuition

It's similar to [Best Time to Buy and Sell
Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/), but
instead of min price, we track max value, and our max value decays every step
due to the distance penalty.

## Solution

  * Track the maximum value of `A[i]` as `max_i`.
  * Every turn, decrement `max_i`to account for `j - i`.
  * Track and return the maximum score.

    
    
    int maxScoreSightseeingPair(vector<int>& A, int res = 0) {
      for (auto j = 1, max_i = A[0] - 1; j < A.size(); ++j, --max_i) {
        res = max(res, A[j] + max_i);
        max_i = max(max_i, A[j]);
      }
      return res;
    }
    

## Complexity Analysis

Runtime: _O(n)_  
Memory: _O(1)_


### Solution 3
 **Intuition**

  * Suppose we choose the site `[i,...j]`. The score can be broken up into 2 parts.
  * The first part is the **startGain** which you gain while starting at a certain point `i`. Notice that `startGain[i] = a[i] + i`.
  * The second part is the **endGain** which is the amount you gain while ending at a certain point `j`. Notice that `endGain[i] = a[j] - j`.
  * Notice that endGain can be negative
  * The overall gain for `[i,...j]` is nothing but `startGain[i] + endGain[j]`. (This can be easily verified by the definitions).

* * *

 **Constraints**

  * You cannot start at the last position
  * You cannot go left at any stage, i.e if you started at `i`, you have to end your trip at a number **strictly** bigger than `i`.

* * *

 **Reduction**

  * We need to maximize the overall Gain.

* * *

  * What are the possible positions for starting the trip? Clearly we can start at all except the last element. So, the optimal trip has to start at one of these elements.
  * Suppose, we are only allowed to start a trip at `i`. What is the maximum amount we can gain in this case? Well, since the `startGain` is fixed, we need to maximize the  
`endGain`. We can do it by stopping at an element which has the maximum
`endGain` with the condition that it appears to the right of `i`.

* * *

 **Setting up the DP definition**

  * As discussed above, for each i, we need to find the maximum `endGain` to the right of it.
  * `maxEndRight[i] = max(maxEndRight[i+1], endGain[i+1])` = `max(maxEndRight[i+1], a[i+1] - (i+1))`
  * `maxEndRight[i]` represent the highest `endGain` that you can get while stopping at any point **strictly** to the right of `i`. Since by definition, we already know `endGain[i+1]` (the highest gain possible by ending at any point to the right of `i+1`) we only need to check the possibility whether stopping at `i+1` would be beneficial or not. Hence, the DP definition.
  * For each valid `i`, `overallGain[i] = startGain[i] + maxEndRight[i]` = `a[i] + i + maxEndRight[i]`

* * *

 **Reducing the Space complexity**

  * Notice that `maxEndRight[i]` only depends on `maxEndRight[i+1]`. Hence, we can use 2 variables to track the previous values.

* * *

 **Miscellaneous**

  * Since we need the value of `maxEndRight[i+1]` to compute the value of `maxEndRight[i]`, therefore we start the iterations at the back.
  * As argued, trips cannot start at the last element, hence the `for` loop starts at `i=n-2`. For this value, `maxEndingRight` is initialized to `endGain[lastIndex]` because this is the only possible way to end the trip.

    
    
    class Solution
    {
    public:
        int maxScoreSightseeingPair(vector<int>& a);
    };
    
    int Solution :: maxScoreSightseeingPair(vector<int>& a)
    {
        int n = a.size();
        int maxOverallGain = INT_MIN;
        int maxEndRight = a[n-1] - (n-1);
        for(int i=n-2; i>=0; i--)
        {
            maxEndRight = max(maxEndRight, a[i+1] - (i+1));
            maxOverallGain = max(maxOverallGain, a[i] + i + maxEndRight);
        }
        return maxOverallGain;
        
    }
    



