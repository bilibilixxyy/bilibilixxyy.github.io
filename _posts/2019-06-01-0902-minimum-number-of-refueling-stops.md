---
layout: post
title: 902. Minimum Number of Refueling Stops
---
### Question
A car travels from a starting position to a destination which is `target`
miles east of the starting position.

Along the way, there are gas stations.  Each `station[i]` represents a gas
station that is `station[i][0]` miles east of the starting position, and has
`station[i][1]` liters of gas.

The car starts with an infinite tank of gas, which initially has `startFuel`
liters of fuel in it.  It uses 1 liter of gas per 1 mile that it drives.

When the car reaches a gas station, it may stop and refuel, transferring all
the gas from the station into the car.

What is the least number of refueling stops the car must make in order to
reach its destination?  If it cannot reach the destination, return `-1`.

Note that if the car reaches a gas station with 0 fuel left, the car can still
refuel there.  If the car reaches the destination with 0 fuel left, it is
still considered to have arrived.



 **Example 1:**

    
    
     **Input:** target = 1, startFuel = 1, stations = []
    **Output:** 0
    **Explanation:** We can reach the target without refueling.
    

**Example 2:**

    
    
    **Input:** target = 100, startFuel = 1, stations = [[10,100]]
    **Output:** -1
    **Explanation:** We can't reach the target (or even the first gas station).
    

**Example 3:**

    
    
    **Input:** target = 100, startFuel = 10, stations = [[10,60],[20,30],[30,30],[60,40]]
    **Output:** 2
    **Explanation:**
    We start with 10 liters of fuel.
    We drive to position 10, expending 10 liters of fuel.  We refuel from 0 liters to 60 liters of gas.
    Then, we drive from position 10 to position 60 (expending 50 liters of fuel),
    and refuel from 10 liters to 50 liters of gas.  We then drive to and reach the target.
    We made 2 refueling stops along the way, so we return 2.
    



 **Note:**

  1. `1 <= target, startFuel, stations[i][1] <= 10^9`
  2. `0 <= stations.length <= 500`
  3. `0 < stations[0][0] < stations[1][0] < ... < stations[stations.length-1][0] < target`

### Solution 1
## Approach 1: 1D DP, O(N^2)

`dp[t]` means the furthest distance that we can get with `t` times of
refueling.

So for every station `s[i]`,  
if the current distance `dp[t] >= s[i][0]`, we can refuel:  
`dp[t + 1] = max(dp[t + 1], dp[t] + s[i][1])`

In the end, we'll return the first `t` with `dp[i] >= target`,  
otherwise we'll return -1.

 **C++:**

    
    
         int minRefuelStops(int target, int startFuel, vector<vector<int>> s) {
            long dp[501] = {startFuel};
            for (int i = 0; i < s.size(); ++i)
                for (int t = i; t >= 0 && dp[t] >= s[i][0]; --t)
                    dp[t + 1] = max(dp[t + 1], dp[t] + s[i][1]);
            for (int t = 0; t <= s.size(); ++t)
                if (dp[t] >= target) return t;
            return -1;
        }
    

**Java:**

    
    
         public int minRefuelStops(int target, int startFuel, int[][] s) {
            long[] dp = new long[s.length + 1];
            dp[0] = startFuel;
            for (int i = 0; i < s.length; ++i)
                for (int t = i; t >= 0 && dp[t] >= s[i][0]; --t)
                    dp[t + 1] = Math.max(dp[t + 1], dp[t] + s[i][1]);
            for (int t = 0; t <= s.length; ++t)
                if (dp[t] >= target) return t;
            return -1;
        }
    

**Python:**

    
    
        def minRefuelStops(self, target, startFuel, s):
             dp = [startFuel] + [0] * len(s)
            for i in range(len(s)):
                for t in range(i + 1)[::-1]:
                    if dp[t] >= s[i][0]:
                        dp[t + 1] = max(dp[t + 1], dp[t] + s[i][1])
            for t, d in enumerate(dp):
                if d >= target: return t
            return -1
    

## Approach 2: Priority Queue, O(NlogN)

`i` is the index of next stops to refuel.  
`res` is the times that we have refeuled.  
`pq` is a priority queue that we store all available gas.

We initial `res = 0` and in every loop:

  1. We add all reachable stop to priority queue.
  2. We pop out the largest gas from `pq` and refeul once.
  3. If we can't refuel, means that we can not go forward and return `-1`

**C++:**

    
    
         int minRefuelStops(int target, int cur, vector<vector<int>> s) {
            int i = 0, res;
            priority_queue<int>pq;
            for (res = 0; cur < target; res++) {
                while (i < s.size() && s[i][0] <= cur)
                    pq.push(s[i++][1]);
                if (pq.empty()) return -1;
                cur += pq.top(), pq.pop();
            }
            return res;
        }
    

**Java:**

    
    
         public int minRefuelStops(int target, int cur, int[][] s) {
            Queue<Integer> pq = new PriorityQueue<>();
            int i = 0, res;
            for (res = 0; cur < target; res++) {
                while (i < s.length && s[i][0] <= cur)
                    pq.offer(-s[i++][1]);
                if (pq.isEmpty()) return -1;
                cur += -pq.poll();
            }
            return res;
        }
    

**Python:**

    
    
        def minRefuelStops(self, target, cur, s):
            pq = []
            res = i =  0
            while cur < target:
                while i < len(s) and s[i][0] <= cur:
                    heapq.heappush(pq, -s[i][1])
                    i += 1
                if not pq: return -1
                cur += -heapq.heappop(pq)
                res += 1
            return res
    
    

Approach 2 inspired by @laiden.


### Solution 2
    
    
    class Solution {
        public int minRefuelStops(int target, int startFuel, int[][] stations) {
            Queue<Integer> queue = new PriorityQueue<>();
            long dist = startFuel;
            int res = 0;
            int idx = 0;
            while (true) {
                while (idx < stations.length && stations[idx][0] <= dist) {
                    queue.offer(-stations[idx][1]);
                    idx++;
                }
                
                if (dist >= target) return res;
                if (queue.isEmpty()) return -1;
                dist += -queue.poll();
                res++;
            }
            
        }
    }
    


### Solution 3
Denote by dp[i][j] the farthest location we can get to using exactly j
refueling stops among the first i refueling stops, for j <= i.

Initially dp[i][0] = startFuel, for i = 0, ..., n.

dp[][] is updated as follows. For the gas station i, one can either refuel the
car at this station if dp[i-1][j-1] >= stations[i][0], and dp[i][j] =
max(dp[i][j], dp[i-1][j-1] + stations[i][1]). Or one can choose to not refuel
the car, and dp[i][j] = max(dp[i][j], dp[i-1][j]) if j <= i-1.

Here is the code for O(n^2) space complexity dynamic programming.

    
    
    for (int i = 0; i <= n; i++) dp[i][0] = startFuel;
    
    for (int i = 0; i < n; i++)
    	for (int j = 0; j <= i; j++) {
    		if (i >= j+1)
    			dp[i+1][j+1] = max(dp[i+1][j+1], dp[i][j+1]);
    		if (dp[i][j] >= stations[i][0])
    			dp[i+1][j+1] = max(dp[i+1][j+1], dp[i][j] + stations[i][1]);
    	}
    

It is worth pointing out that the (i+1)-th raws depends only on the values on
the i-th raws. Hence, we can reduce the space complexity.  
The previous code is equivalent to

    
    
    dp[0] = startFuel;
    for (int i = 0; i < n; i++)
    	for (int j = i; j >= 0; j--) {
    		if (dp[j] >= stations[i][0])
    			dp[j+1] = max(dp[j+1], dp[j] + stations[i][1]);
    	}
    



