---
layout: post
title: 1025. Minimum Cost For Tickets
---
### Question
In a country popular for train travel, you have planned some train travelling
one year in advance.  The days of the year that you will travel is given as an
array `days`.  Each day is an integer from `1` to `365`.

Train tickets are sold in 3 different ways:

  * a 1-day pass is sold for `costs[0]` dollars;
  * a 7-day pass is sold for `costs[1]` dollars;
  * a 30-day pass is sold for `costs[2]` dollars.

The passes allow that many days of consecutive travel.  For example, if we get
a 7-day pass on day 2, then we can travel for 7 days: day 2, 3, 4, 5, 6, 7,
and 8.

Return the minimum number of dollars you need to travel every day in the given
list of `days`.



 **Example 1:**

    
    
     **Input:** days = [1,4,6,7,8,20], costs = [2,7,15]
    **Output:** 11
    **Explanation:**
    For example, here is one way to buy passes that lets you travel your travel plan:
    On day 1, you bought a 1-day pass for costs[0] = $2, which covered day 1.
    On day 3, you bought a 7-day pass for costs[1] = $7, which covered days 3, 4, ..., 9.
    On day 20, you bought a 1-day pass for costs[0] = $2, which covered day 20.
    In total you spent $11 and covered all the days of your travel.
    

**Example 2:**

    
    
    **Input:** days = [1,2,3,4,5,6,7,8,9,10,30,31], costs = [2,7,15]
    **Output:** 17
    **Explanation:**
    For example, here is one way to buy passes that lets you travel your travel plan:
    On day 1, you bought a 30-day pass for costs[2] = $15 which covered days 1, 2, ..., 30.
    On day 31, you bought a 1-day pass for costs[0] = $2 which covered day 31.
    In total you spent $17 and covered all the days of your travel.
    



 **Note:**

  1. `1 <= days.length <= 365`
  2. `1 <= days[i] <= 365`
  3. `days` is in strictly increasing order.
  4. `costs.length == 3`
  5. `1 <= costs[i] <= 1000`

### Solution 1
The higher is the bar, the more you're expected to use dynamic programming
(DP) during an interview. This technique requires a lot of practice to grasp;
if you've mastered the recursion, DP is the next level.

This problem appeared on LeetCode [weekly contest
#121](https://leetcode.com/contest/weekly-contest-121), and it's a good
problem to practice the DP thinking.

# Intuition

For each travel day, we can buy a one-day ticket, or use 7-day or 30-day pass
as if we would have purchased it 7 or 30 days ago. We need to track rolling
costs for at least 30 days back, and use them to pick the cheapest option for
the next travel day.

Here, we can use two approaches: track cost for all calendar days, or process
only travel days. The first approach is simpler to implement, but it's slower.
Since the problem is limited to one calendar year, it does not make much of a
difference; for a generalized problem I would recommend the second approach.

# 1\. Track calendar days

We track the minimum cost for all calendar days in `dp`. For non-travel days,
the cost stays the same as for the previous day. For travel days, it's a
minimum of yesterday's cost plus single-day ticket, or cost for 8 days ago
plus 7-day pass, or cost 31 days ago plus 30-day pass.

![image](https://assets.leetcode.com/users/votrubac/image_1548621855.png)

    
    
    int mincostTickets(vector<int>& days, vector<int>& costs) {
      unordered_set<int> travel(begin(days), end(days));
      int dp[366] = {};
      for (int i = 1; i < 366; ++i) {
        if (travel.find(i) == travel.end()) dp[i] = dp[i - 1];
        else dp[i] = min({ dp[i - 1] + costs[0], dp[max(0, i - 7)] + costs[1], dp[max(0, i - 30)] + costs[2]});
      }
      return dp[365];
    }
    

# Optimizations

In the previous solution, we store cost for all calendar days. However, since
we only look 30 days back, we can just store the cost for last 30 days in a
rolling array.

In addition, we can only look at calendar days within our first and last
travel dates, as [@zengxinhai](https://leetcode.com/zengxinhai) suggested.

    
    
    int mincostTickets(vector<int>& days, vector<int>& costs) {
      unordered_set<int> travel(begin(days), end(days));
      int dp[30] = {};
      for (int i = days.front(); i <= days.back(); ++i) {
        if (travel.find(i) == travel.end()) dp[i % 30] = dp[(i - 1) % 30];
        else dp[i % 30] = min({ dp[(i - 1) % 30] + costs[0],
            dp[max(0, i - 7) % 30] + costs[1], dp[max(0, i - 30) % 30] + costs[2] });
      }
      return dp[days.back() % 30];
    }
    

# Complexity analysis

  * Time Complexity: O(N), where N is the number of calendar days.
  * Space Complexity: O(N) or O(31) for the optimized solution. Stricter, it's a maximum duration among all pass types.

# 2\. Track travel days

We track the minimum `cost` for each travel day. We process only travel days
and store {day, cost} for 7-and 30-day passes in the `last7` and `last30`
queues. After a pass 'expires', we remove it from the queue. This way, our
queues only contains travel days for the last 7 and 30 days, and the cheapest
pass prices are in the front of the queues.

![image](https://assets.leetcode.com/users/votrubac/image_1548617861.png)

    
    
    int mincostTickets(vector<int>& days, vector<int>& costs, int cost = 0) {
      queue<pair<int, int>> last7, last30;
      for (auto d : days) {
        while (!last7.empty() && last7.front().first + 7 <= d) last7.pop();
        while (!last30.empty() && last30.front().first + 30 <= d) last30.pop();
        last7.push({ d, cost + costs[1] });
        last30.push({ d, cost + costs[2] });
        cost = min({ cost + costs[0], last7.front().second, last30.front().second });
      }
      return cost;
    }
    

# Complexity analysis

  * Time Complexity: O(n), where n is the number of travel days.
  * Space Complexity: O(38). Stricter, it's a sum of duration for all pass types (1 + 7 + 30 in our case).


### Solution 2
Let minCost(i) denote the minimum cost that will be payed for all trips on
days 1 to day 365.  
The desired answer is then minCost(365).

Calculation minCost(i):

If no trip on day i, then minCost(i) = minCost(i-1).  
minCost(i)=0 for all i ≤ 0.  
Otherwise:  
If a 1-day pass on day i. In this case, minCost(i) = minCost(i) + costs[0].  
If a 7-day pass ending on day i. then : In this case, minCost(i) =
min(minCost(i − 7), minCost(i − 6), …, minCost(i − 1)) + costs[1].  
But since since minCost is increasing (adding a day never reduces the minCost)
hence:  
minCost(i) = minCost(i − 7) + costs[2]

Same case for 30-day pass also.

    
    
    public int mincostTickets(int[] days, int[] costs) {
            boolean[] dayIncluded = new boolean[366];
            for (int day : days) {
                dayIncluded[day] = true;
            }
            int[] minCost = new int[366];
            minCost[0] = 0;
            for (int day = 1; day <= 365; ++day) {
                if (!dayIncluded[day]) {
                    minCost[day] = minCost[day-1];
                    continue;
                }
                int min;
                min = minCost[day-1] + costs[0];
                min =Math.min(min, minCost[Math.max(0, day-7)] + costs[1]);
                min = Math.min(min, minCost[Math.max(0, day-30)] + costs[2]);
                minCost[day] = min;
            }
    
            return minCost[365];
    
        }
    


### Solution 3
    
    
    class Solution(object):
        def mincostTickets(self, days, costs):
            """
            :type days: List[int]
            :type costs: List[int]
            :rtype: int
            """
            dp=[0 for i in range(days[-1]+1)]
            for i in range(days[-1]+1):
                 if i not in days:
                    dp[i]=dp[i-1]
                 else:
                    dp[i]=min(dp[max(0,i-7)]+costs[1],dp[max(0,i-1)]+costs[0],dp[max(0,i-30)]+costs[2])
            return dp[-1]
    
    
    
    



