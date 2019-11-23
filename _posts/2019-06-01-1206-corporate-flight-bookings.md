---
layout: post
title: 1206. Corporate Flight Bookings
---
### Question
There are `n` flights, and they are labeled from `1` to `n`.

We have a list of flight bookings.  The `i`-th booking `bookings[i] = [i, j,
k]` means that we booked `k` seats from flights labeled `i` to `j` inclusive.

Return an array `answer` of length `n`, representing the number of seats
booked on each flight in order of their label.



 **Example 1:**

    
    
     **Input:** bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5
    **Output:** [10,55,45,25,25]
    



 **Constraints:**

  * `1 <= bookings.length <= 20000`
  * `1 <= bookings[i][0] <= bookings[i][1] <= n <= 20000`
  * `1 <= bookings[i][2] <= 10000`

### Solution 1
# Intuition

Since ranges are continuous, what if we add reservations to the first flight
in the range, and remove them after the last flight in range? We can then use
the running sum to update reservations for all flights.

This picture shows the logic for this test case:
`[[1,2,10],[2,3,20],[3,5,25]]`.  
![image](https://assets.leetcode.com/users/votrubac/image_1562473681.png)

## C++ Solution

    
    
    vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
      vector<int> res(n);
      for (auto &v : bookings) {
        res[v[0] - 1] += v[2];
        if (v[1] < n) res[v[1]] -= v[2];
      }
      for (auto i = 1; i < n; ++i) res[i] += res[i - 1];
      return res;
    }
    

## Java Solution

    
    
    public int[] corpFlightBookings(int[][] bookings, int n) {
      int[] res = new int[n];
      for (int[] v : bookings) {
        res[v[0] - 1] += v[2];
        if (v[1] < n) res[v[1]] -= v[2];
      }
      for (int i = 1; i < n; ++i) res[i] += res[i - 1];
      return res;
    }
    

## Complexity Analysis

Runtime: _O(n)_ , where n is the number of flights (or bookings).  
Memory: _O(n)_


### Solution 2
##  **Explanation**

Set the change of seats for each day.  
If `booking = [i, j, k]`,  
it needs `k` more seat on `i`th day,  
and we don't need these seats on `j+1`th day.  
We accumulate these changes then we have the result that we want.  
  

##  **Complexity**

Time `O(booking + N)` for one pass on `bookings`  
Space `O(N)` for the result  
  

 **Java:**

    
    
         public int[] corpFlightBookings(int[][] bookings, int n) {
            int[] res = new int[n];
            for (int[] b : bookings) {
                res[b[0] - 1] += b[2];
                if (b[1] < n) res[b[1]] -= b[2];
            }
            for (int i = 1; i < n; ++i)
                res[i] += res[i - 1];
            return res;
        }
    

**C++:**

    
    
         vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
            vector<int> res(n + 1, 0);
            for (auto & b : bookings) {
                res[b[0] - 1] += b[2];
                res[b[1]] -= b[2];
            }
            for (int i = 1; i < n; ++i)
                res[i] += res[i - 1];
            return {res.begin(), res.end() - 1};
        }
    

**Python:**

    
    
         def corpFlightBookings(self, bookings, n):
            res = [0] * (n + 1)
            for i, j, k in bookings:
                res[i - 1] += k
                res[j] -= k
            for i in xrange(1, n):
                res[i] += res[i - 1]
            return res[:-1]
    


### Solution 3
题意：一条指令的含义是区间加上指定值，有很多指令，求最终区间每个位置的值。

思路：最原始的方法是线段树区间操作。

但是这道题不存在查询，只是求最终结果。  
那就可以使用标记的方法，区间的开始位置加上指定值，区间结束的下个位置减去指定值。  
然后从左到右累加即可。

* * *

增加几句解释：

只有那些v[0] <= i <= v[1] 的区间操作才能影响位置i的值，即位置i的值是这些区间操作的和。

如果一个区间在位置i之前，那加到i后完全不受影响。  
如果一个区间在位置i字后，那不会有加减操作。  
如果一个位置在区间内，那么只有加操作，没有减操作，这样累计和就是需要的答案。

    
    
    vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
        vector<int> dp;
        dp.resize(n+2, 0);
        for(auto& v: bookings){
            dp[v[0]] += v[2];
            dp[v[1]+1] -= v[2];
        }
        vector<int> ans;
        int now = 0;
        for(int i=1;i<=n;i++){
            now += dp[i];
            ans.push_back(now);
        }
        return ans;
    }
    



