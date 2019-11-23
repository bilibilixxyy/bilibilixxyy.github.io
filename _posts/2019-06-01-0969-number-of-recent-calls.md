---
layout: post
title: 969. Number of Recent Calls
---
### Question
Write a class `RecentCounter` to count recent requests.

It has only one method: `ping(int t)`, where t represents some time in
milliseconds.

Return the number of `ping`s that have been made from 3000 milliseconds ago
until now.

Any ping with time in `[t - 3000, t]` will count, including the current ping.

It is guaranteed that every call to `ping` uses a strictly larger value of `t`
than before.



 **Example 1:**

    
    
     **Input:** inputs = ["RecentCounter","ping","ping","ping","ping"], inputs = [[],[1],[100],[3001],[3002]]
    **Output:** [null,1,2,3,3]



 **Note:**

  1. Each test case will have at most `10000` calls to `ping`.
  2. Each test case will call `ping` with strictly increasing values of `t`.
  3. Each call to ping will have `1 <= t <= 10^9`.

### Solution 1
I am slightly confused by the problem statement, could anyone describe the
sample output? I get how 1 and 2 are counted, but not why 3001 and 3002 are
not.


### Solution 2
Can someone please explain this question to me?


### Solution 3
 **Update** : Method 4 added for those who are interested in space
optimization.

 **Mehod 1:** `TreeMap`.

Use current time and total number of calls as the key and value of `TreeMap`,
respectively.

 **Analysis**  
Time: O(logN), space: O(N), where N is the number of ping calls from first one
till now.

`TreeMap.tailMap()` and `put()` both cost time O(logN).

    
    
        TreeMap<Integer, Integer> tm;
    
        public RecentCounter() {
            tm = new TreeMap<>();
        }
        
        public int ping(int t) {
            tm.put(t, 1 + tm.size());
            return tm.tailMap(t - 3000).size();
        }
    

**Mehod 2:** `TreeSet`.

Or similarly use `TreeSet` instead.

`TreeSet.tailSet()` and `add()` both cost time O(logN).

    
    
        TreeSet<Integer> ts;
    
        public RecentCounter() {
            ts = new TreeSet<>();
        }
        
        public int ping(int t) {
            ts.add(t);
            return ts.tailSet(t - 3000).size();
        }
    

Since some complain that the above two methods cause `TLE`, I guess that
besides `tailMap` and `tailSet, TreeMap.put()` and `TreeSet.add()` also cost
`O(logN)`, therefore the total time cost is high.

The following method will use less time.

**Mehod 3:** Binary Search `ArrayList`.

Use binary search to find the index of the ceiling of `t - 3000`, then
`list.size() - index` is the answer.

`binarySearch()` cost `O(logN)`.

    
    
        List<Integer> list;
    
        public RecentCounter() {
            list = new ArrayList<>();
        }
        
        public int ping(int t) {
            list.add(t);
            int index = Collections.binarySearch(list, t - 3000); // search the index of t - 3000.
            if (index < 0) { index = -index - 1; } // if t - 3000 is not in list, use the index of the ceiling of t - 3000.
            return list.size() - index;
        }
    

**Mehod 4:** `Queue`.

Time: `O(N)`, space: `O(Math.min(N, 3000))`.

    
    
        Queue<Integer> q;
    
        public RecentCounter() {
            q = new LinkedList<>();
        }
        
        public int ping(int t) {
            q.offer(t);
            while (q.peek() < t - 3000) { q.poll(); }
            return q.size();
        }
    



