---
layout: post
title: 56. Merge Intervals
---
### Question
Given a collection of intervals, merge all overlapping intervals.

 **Example 1:**

    
    
     **Input:** [[1,3],[2,6],[8,10],[15,18]]
    **Output:** [[1,6],[8,10],[15,18]]
    **Explanation:** Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
    

**Example 2:**

    
    
    **Input:** [[1,4],[4,5]]
    **Output:** [[1,5]]
    **Explanation:** Intervals [1,4] and [4,5] are considered overlapping.

 **NOTE:**  input types have been changed on April 15, 2019. Please reset to
default code definition to get new method signature.

### Solution 1
The idea is to sort the intervals by their starting points. Then, we take the
first interval and compare its end with the next intervals starts. As long as
they overlap, we update the end to be the max end of the overlapping
intervals. Once we find a non overlapping interval, we can add the previous
"extended" interval and start over.

Sorting takes O(n log(n)) and merging the intervals takes O(n). So, the
resulting algorithm takes O(n log(n)).

I used a lambda comparator (Java 8) and a for-each loop to try to keep the
code clean and simple.

EDIT: The function signature changed in april 2019.  
Here is a new version of the algorithm with arrays. To make more memory
efficient, I reused the initial array (sort of "in-place") but it would be
easy to create new subarrays if you wanted to keep the initial data.  
It takes less memory than 99% of the other solutions (sometimes 90% depending
on the run) and is more than 10 times faster than the previous version with
lists.

    
    
    class Solution {
    	public int[][] merge(int[][] intervals) {
    		if (intervals.length <= 1)
    			return intervals;
    
    		// Sort by ascending starting point
    		Arrays.sort(intervals, (i1, i2) -> Integer.compare(i1[0], i2[0]));
    
    		List<int[]> result = new ArrayList<>();
    		int[] newInterval = intervals[0];
    		result.add(newInterval);
    		for (int[] interval : intervals) {
    			if (interval[0] <= newInterval[1]) // Overlapping intervals, move the end if needed
    				newInterval[1] = Math.max(newInterval[1], interval[1]);
    			else {                             // Disjoint intervals, add the new interval to the list
    				newInterval = interval;
    				result.add(newInterval);
    			}
    		}
    
    		return result.toArray(new int[result.size()][]);
    	}
    }
    

Previous version with lists.

    
    
    public List<Interval> merge(List<Interval> intervals) {
        if (intervals.size() <= 1)
            return intervals;
        
        // Sort by ascending starting point using an anonymous Comparator
        intervals.sort((i1, i2) -> Integer.compare(i1.start, i2.start));
        
        List<Interval> result = new LinkedList<Interval>();
        int start = intervals.get(0).start;
        int end = intervals.get(0).end;
        
        for (Interval interval : intervals) {
            if (interval.start <= end) // Overlapping intervals, move the end if needed
                end = Math.max(end, interval.end);
            else {                     // Disjoint intervals, add the previous one and reset bounds
                result.add(new Interval(start, end));
                start = interval.start;
                end = interval.end;
            }
        }
        
        // Add the last interval
        result.add(new Interval(start, end));
        return result;
    }
    

EDIT: Updated with Java 8 lambda comparator.  
EDIT 25/05/2019: Updated for new method signature.


### Solution 2
Just go through the intervals sorted by start coordinate and either combine
the current interval with the previous one if they overlap, or add it to the
output by itself if they don't.

    
    
    def merge(self, intervals):
        out = []
        for i in sorted(intervals, key=lambda i: i.start):
            if out and i.start <= out[-1].end:
                out[-1].end = max(out[-1].end, i.end)
            else:
                out += i,
        return out


### Solution 3
    
    
    vector<Interval> merge(vector<Interval>& ins) {
        if (ins.empty()) return vector<Interval>{};
        vector<Interval> res;
        sort(ins.begin(), ins.end(), [](Interval a, Interval b){return a.start < b.start;});
        res.push_back(ins[0]);
        for (int i = 1; i < ins.size(); i++) {
            if (res.back().end < ins[i].start) res.push_back(ins[i]);
            else
                res.back().end = max(res.back().end, ins[i].end);
        }
        return res;
    }



