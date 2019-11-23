---
layout: post
title: 907. Koko Eating Bananas
---
### Question
Koko loves to eat bananas.  There are `N` piles of bananas, the `i`-th pile
has `piles[i]` bananas.  The guards have gone and will come back in `H` hours.

Koko can decide her bananas-per-hour eating speed of `K`.  Each hour, she
chooses some pile of bananas, and eats K bananas from that pile.  If the pile
has less than `K` bananas, she eats all of them instead, and won't eat any
more bananas during this hour.

Koko likes to eat slowly, but still wants to finish eating all the bananas
before the guards come back.

Return the minimum integer `K` such that she can eat all the bananas within
`H` hours.



 **Example 1:**

    
    
     **Input:** piles = [3,6,7,11], H = 8
    **Output:** 4
    

**Example 2:**

    
    
    **Input:** piles = [30,11,23,4,20], H = 5
    **Output:** 30
    

**Example 3:**

    
    
    **Input:** piles = [30,11,23,4,20], H = 6
    **Output:** 23
    



 **Note:**

  * `1 <= piles.length <= 10^4`
  * `piles.length <= H <= 10^9`
  * `1 <= piles[i] <= 10^9`

### Solution 1
Each hour, Koko chooses some pile of bananas, and eats `K` bananas from that
pile.  
There is a limited range of `K`'s to enable her to eat all the bananas within
`H` hours.  
We ought to reduce the searching space and to return the minimum valid `K`.  
 **Binary Search** is born for that.  
Initially, we know that K belongs to [1, the largest element in `piles[]`].
And we follow the pattern of lower-bound Binary Search except that `if (K ==
target)` is replaced with `if (canEatAll(piles, K, H))`.

* * *

 **Java**

    
    
         public int minEatingSpeed(int[] piles, int H) {
            int lo = 1, hi = getMaxPile(piles);
            
            // Binary search to find the smallest valid K.
            while (lo <= hi) {
                int K = lo + ((hi - lo) >> 1);
                if (canEatAll(piles, K, H)) {
                    hi = K - 1;
                } else {
                    lo = K + 1;
                }
            }
            
            return lo;
        }
        
        private boolean canEatAll(int[] piles, int K, int H) {
            int countHour = 0; // Hours take to eat all bananas at speed K.
            
            for (int pile : piles) {
                countHour += pile / K;
                if (pile % K != 0)
                    countHour++;
            }
            return countHour <= H;
        }
        
        private int getMaxPile(int[] piles) {
            int maxPile = Integer.MIN_VALUE;
            for (int pile : piles) {
                maxPile = Math.max(pile, maxPile);
            }
            return maxPile;
        }
    

**Python**

    
    
        def minEatingSpeed(self, piles, H):
             lo, hi = 1, max(piles)
            
            while lo <= hi:
                K = lo + ((hi - lo) >> 1)
                if self.countTimeEatAllAtSpeed(
                        K, piles) <= H:  # count time to eat all bananas at speed K
                    hi = K - 1
                else:
                    lo = K + 1
            return lo
        
        def countTimeEatAllAtSpeed(self, K, piles):
            countHours = 0  # hours take to eat all bananas
            
            for pile in piles:
                countHours += pile / K
                if pile % K != 0:
                    countHours += 1
            return countHours
    

**(人 •͈ᴗ•͈)** Thanks for voting!


### Solution 2
Binary search between `[1, 10^9]` or `[1, max(piles)]` to find the result.  
Time complexity: `O(NlogM)`

`(p + m - 1) / m` equal to `ceil(p / m)` (just personal behavior)

Here you find another similar problem.  
[774\. Minimize Max Distance to Gas
Station](https://leetcode.com/problems/minimize-max-distance-to-gas-
station/discuss/113633/C++JavaPython-Binary-Search)

 **C++:**

    
    
         int minEatingSpeed(vector<int>& piles, int H) {
            int l = 1, r = 1000000000;
            while (l < r) {
                int m = (l + r) / 2, total = 0;
                for (int p : piles) total += (p + m - 1) / m;
                if (total > H) l = m + 1; else r = m;
            }
            return l;
        }
    

**Java:**

    
    
         public int minEatingSpeed(int[] piles, int H) {
            int l = 1, r = 1000000000;
            while (l < r) {
                int m = (l + r) / 2, total = 0;
                for (int p : piles) total += (p + m - 1) / m;
                if (total > H) l = m + 1; else r = m;
            }
            return l;
        }
    

**Python:**

    
    
        def minEatingSpeed(self, piles, H):
            l, r =  1, max(piles)
            while l < r:
                m = (l + r) / 2
                if sum((p + m - 1) / m for p in piles) > H: l = m + 1
                else: r = m
            return l
    

**I would appreciate your VOTE UP :)**


### Solution 3
The search range is from 0 to the largest pile.  
We count total hours it needs to eat all bananas with speed=`mid`, and use
`cnt` to decide whether to move `lo` or `hi`.

    
    
    class Solution {
        public int minEatingSpeed(int[] piles, int H) {
            Arrays.sort(piles);
            int lo=0, hi=piles[piles.length-1];
            while (lo<hi){
                int mid= (lo+hi)/2, cnt=0;
                for (int p: piles) 
                    cnt+=Math.ceil((double)p/mid);
                if (cnt>H) lo=mid+1;
                else hi=mid;
            }
            return lo;
        }
    }
    

Happy Coding.



