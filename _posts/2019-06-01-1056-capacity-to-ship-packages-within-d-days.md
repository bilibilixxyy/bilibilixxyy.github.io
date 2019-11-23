---
layout: post
title: 1056. Capacity To Ship Packages Within D Days
---
### Question
A conveyor belt has packages that must be shipped from one port to another
within `D` days.

The `i`-th package on the conveyor belt has a weight of `weights[i]`.  Each
day, we load the ship with packages on the conveyor belt (in the order given
by `weights`). We may not load more weight than the maximum weight capacity of
the ship.

Return the least weight capacity of the ship that will result in all the
packages on the conveyor belt being shipped within `D` days.



 **Example 1:**

    
    
     **Input:** weights = [1,2,3,4,5,6,7,8,9,10], D = 5
    **Output:** 15
    **Explanation:**
    A ship capacity of 15 is the minimum to ship all the packages in 5 days like this:
    1st day: 1, 2, 3, 4, 5
    2nd day: 6, 7
    3rd day: 8
    4th day: 9
    5th day: 10
    
    Note that the cargo must be shipped in the order given, so using a ship of capacity 14 and splitting the packages into parts like (2, 3, 4, 5), (1, 6, 7), (8), (9), (10) is not allowed. 
    

**Example 2:**

    
    
    **Input:** weights = [3,2,2,4,1,4], D = 3
    **Output:** 6
    **Explanation:**
    A ship capacity of 6 is the minimum to ship all the packages in 3 days like this:
    1st day: 3, 2
    2nd day: 2, 4
    3rd day: 1, 4
    

**Example 3:**

    
    
    **Input:** weights = [1,2,3,1,1], D = 4
    **Output:** 3
    **Explanation:**
    1st day: 1
    2nd day: 2
    3rd day: 3
    4th day: 1, 1
    



 **Note:**

  1. `1 <= D <= weights.length <= 50000`
  2. `1 <= weights[i] <= 500`

### Solution 1
Given the number of bags,  
return the minimum capacity of each bag,  
so that we can put items one by one into all bags.

Similar as  
875\. Koko Eating Bananas  
774\. Minimize Max Distance to Gas Station

 **Java**

    
    
         public int shipWithinDays(int[] weights, int D) {
            int left = 0, right = 0;
            for (int w: weights) {
                left = Math.max(left, w);
                right += w；
            }
            while (left < right) {
                int mid = (left + right) / 2, need = 1, cur = 0;
                for (int w: weights) {
                    if (cur + w > mid) {
                        need += 1;
                        cur = 0;
                    }
                    cur += w;
                }
                if (need > D) left = mid + 1;
                else right = mid;
            }
            return left;
        }
    

**C++**

    
    
         int shipWithinDays(vector<int>& weights, int D) {
            int left = 0, right = 25000000;
            for (int w: weights)
                left = max(left, w);
            while (left < right) {
                int mid = (left + right) / 2, need = 1, cur = 0;
                for (int i = 0; i < weights.size() && need <= D; cur += weights[i++])
                    if (cur + weights[i] > mid)
                        cur = 0, need++;
                if (need > D) left = mid + 1;
                else right = mid;
            }
            return left;
        }
    

**Python:**

    
    
        def shipWithinDays(self, weights, D):
             left, right = max(weights), sum(weights)
            while left < right:
                mid, need, cur = (left + right) / 2, 1, 0
                for w in weights:
                    if cur + w > mid:
                        need += 1
                        cur = 0
                    cur += w
                if need > D: left = mid + 1
                else: right = mid
            return left
    


### Solution 2
## Intuition

  * The largest capacity (`r`) we may even need is the sum of weights of all packages.
  * The smallest capacity (`l`) is the weight of the largest package.
    * Optimization: the smallest capacity cannot be less than `r / D`, which reduces the search interval if we have a lot of small packages (and `D` is small).
  * Our result is within this interval.

## Linearithmic Solution

We use binary search to find the minimum capacity. For each capacity we
analyze, we count the number of days required to ship all packages.

We decrease the capacity if it takes less days than `D`, and increase
otherwise. Note that, when the number of days equals `D`, this algorithm keeps
decreasing the capacity while it can, therefore finding the smallest capacity
required.

    
    
    int countDays(vector<int>& ws, int tot_cap, int cur_cap = 0, int res = 1) {
      for (auto w : ws) {
        cur_cap += w;
        if (cur_cap > tot_cap) ++res, cur_cap = w;
      }
      return res;
    }
    int shipWithinDays(vector<int>& ws, int D) {
      auto r = accumulate(begin(ws), end(ws), 0);
      auto l = max(r / D, *max_element(begin(ws), end(ws)));
      while (l < r) {
        auto m = (l + r) / 2;
        if (countDays(ws, m) <= D) r = m;
        else l = m + 1;
      }
      return l;
    }
    

## Complexity Analysis

Runtime: _O(n * log n)_. We perform _log n_ analyses, and for each we process
_n_ packages.  
Memory: _O(1)_. The iterative solution does not require additional memory.


### Solution 3
The intuition for this problem, stems from the fact that

a) Without considering the limiting limiting days D, if we are to solve, the
answer is simply max(a)  
b) If max(a) is the answer, we can still spend O(n) time and greedily find out
how many partitions it will result in.

[1,2,3,4,5,6,7,8,9,10], D = 5

For this example, assuming the answer is max(a) = 10, disregarding D,  
we can get the following number of days:  
[1,2,3,4] [5] [6] [7] [8] [9] [10]

So by minimizing the cacpacity shipped on a day, we end up with 7 days, by
greedily chosing the packages for a day limited by 10.

To get to exactly D days and minimize the max sum of any partition, we do
binary search in the sum space which is bounded by [max(a), sum(a)]

Binary Search Update:  
One thing to note in Binary Search for this problem, is even if we end up
finding a weight, that gets us to D partitions, we still want to continue the
space on the minimum side, because, there could be a better minimum sum that
still passes <= D paritions.

In the code, this is achieved by:

    
    
    if res <= d:
         hi = mid
    

With this check in place, when we narrow down on one element, lo == hi, we
will end up with exactly the minimum sum that leads to <= D partitions.

    
    
    def shipWithinDays(self, a: List[int], d: int) -> int:
            lo, hi = max(a), sum(a)   
            while lo < hi:
                mid = (lo + hi) // 2
                tot, res = 0, 1
                for wt in a:
                    if tot + wt > mid:
                        res += 1
                        tot = wt
                    else:
                        tot += wt
                if res <= d:
                    hi = mid
                else:
                    lo = mid+1
            return lo
    



