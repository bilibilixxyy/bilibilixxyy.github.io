---
layout: post
title: 57. Insert Interval
---
### Question
Given a set of _non-overlapping_ intervals, insert a new interval into the
intervals (merge if necessary).

You may assume that the intervals were initially sorted according to their
start times.

 **Example 1:**

    
    
     **Input:** intervals = [[1,3],[6,9]], newInterval = [2,5]
    **Output:** [[1,5],[6,9]]
    

**Example 2:**

    
    
    **Input:** intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
    **Output:** [[1,2],[3,10],[12,16]]
    **Explanation:** Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].

 **NOTE:**  input types have been changed on April 15, 2019. Please reset to
default code definition to get new method signature.

### Solution 1
Hi guys!

Here's a pretty straight-forward and concise solution below.

    
    
    public List<Interval> insert(List<Interval> intervals, Interval newInterval) {
        List<Interval> result = new LinkedList<>();
        int i = 0;
        // add all the intervals ending before newInterval starts
        while (i < intervals.size() && intervals.get(i).end < newInterval.start)
            result.add(intervals.get(i++));
        // merge all overlapping intervals to one considering newInterval
        while (i < intervals.size() && intervals.get(i).start <= newInterval.end) {
            newInterval = new Interval( // we could mutate newInterval here also
                    Math.min(newInterval.start, intervals.get(i).start),
                    Math.max(newInterval.end, intervals.get(i).end));
            i++;
        }
        result.add(newInterval); // add the union of intervals we got
        // add all the rest
        while (i < intervals.size()) result.add(intervals.get(i++)); 
        return result;
    }
    

Hope it helps.


### Solution 2
 **Solution 1:** (7 lines, 88 ms)

Collect the intervals strictly left or right of the new interval, then merge
the new one with the middle ones (if any) before inserting it between left and
right ones.

    
    
    def insert(self, intervals, newInterval):
        s, e = newInterval.start, newInterval.end
        left = [i for i in intervals if i.end < s]
        right = [i for i in intervals if i.start > e]
        if left + right != intervals:
            s = min(s, intervals[len(left)].start)
            e = max(e, intervals[~len(right)].end)
        return left + [Interval(s, e)] + right
    

* * *

**Solution 2:** (8 lines, 84 ms)

Same algorithm as solution 1, but different implementation with only one pass
and explicitly collecting the to-be-merged intervals.

    
    
    def insert(self, intervals, newInterval):
        s, e = newInterval.start, newInterval.end
        parts = merge, left, right = [], [], []
        for i in intervals:
            parts[(i.end < s) - (i.start > e)].append(i)
        if merge:
            s = min(s, merge[0].start)
            e = max(e, merge[-1].end)
        return left + [Interval(s, e)] + right
    

* * *

**Solution 3:** (11 lines, 80 ms)

Same again, but collect and merge while going over the intervals once.

    
    
    def insert(self, intervals, newInterval):
        s, e = newInterval.start, newInterval.end
        left, right = [], []
        for i in intervals:
            if i.end < s:
                left += i,
            elif i.start > e:
                right += i,
            else:
                s = min(s, i.start)
                e = max(e, i.end)
        return left + [Interval(s, e)] + right


### Solution 3
    
    
    public List<Interval> insert(List<Interval> intervals, Interval newInterval) {
        List<Interval> result = new ArrayList<Interval>();
        for (Interval i : intervals) {
            if (newInterval == null || i.end < newInterval.start)
                result.add(i);
            else if (i.start > newInterval.end) {
                result.add(newInterval);
                result.add(i);
                newInterval = null;
            } else {
                newInterval.start = Math.min(newInterval.start, i.start);
                newInterval.end = Math.max(newInterval.end, i.end);
            }
        }
        if (newInterval != null)
            result.add(newInterval);
        return result;
    }



