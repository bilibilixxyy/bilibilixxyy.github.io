---
layout: post
title: 435. Non-overlapping Intervals
---
### Question
Given a collection of intervals, find the minimum number of intervals you need
to remove to make the rest of the intervals non-overlapping.

 **Note:**

  1. You may assume the interval's end point is always bigger than its start point.
  2. Intervals like [1,2] and [2,3] have borders "touching" but they don't overlap each other.



 **Example 1:**

    
    
     **Input:** [ [1,2], [2,3], [3,4], [1,3] ]
    
    **Output:** 1
    
    **Explanation:** [1,3] can be removed and the rest of intervals are non-overlapping.
    



**Example 2:**

    
    
    **Input:** [ [1,2], [1,2], [1,2] ]
    
    **Output:** 2
    
    **Explanation:** You need to remove two [1,2] to make the rest of intervals non-overlapping.
    



**Example 3:**

    
    
    **Input:** [ [1,2], [2,3] ]
    
    **Output:** 0
    
    **Explanation:** You don't need to remove any of the intervals since they're already non-overlapping.
    

**NOTE:**  input types have been changed on April 15, 2019. Please reset to
default code definition to get new method signature.

### Solution 1
Actually, the problem is the same as "Given a collection of intervals, find
the maximum number of intervals that are non-overlapping." (the classic Greedy
problem: [Interval
Scheduling](https://en.wikipedia.org/wiki/Interval_scheduling#Interval_Scheduling_Maximization)).
With the solution to that problem, guess how do we get the minimum number of
intervals to remove? : )

Sorting Interval.end in ascending order is O(nlogn), then traverse intervals
array to get the maximum number of non-overlapping intervals is O(n). Total is
O(nlogn).

    
    
        public int eraseOverlapIntervals(Interval[] intervals) {
            if (intervals.length == 0)  return 0;
    
            Arrays.sort(intervals, new myComparator());
            int end = intervals[0].end;
            int count = 1;        
    
            for (int i = 1; i < intervals.length; i++) {
                if (intervals[i].start >= end) {
                    end = intervals[i].end;
                    count++;
                }
            }
            return intervals.length - count;
        }
        
        class myComparator implements Comparator<Interval> {
            public int compare(Interval a, Interval b) {
                return a.end - b.end;
            }
        }
    


### Solution 2
Which interval would be the best **first** (leftmost) interval to keep? One
that ends first, as it leaves the most room for the rest. So take one with
smallest `end`, remove all the bad ones overlapping it, and repeat (taking the
one with smallest `end` of the remaining ones). For the overlap test, just
keep track of the current end, initialized with negative infinity.

## Ruby

Take out intervals as described above, so what's left is the bad overlapping
ones, so just return their number.

    
    
    def erase_overlap_intervals(intervals)
      end_ = -1.0 / 0
      intervals.sort_by(&:end).reject { |i|
        end_ = i.end if i.start >= end_
      }.size
    end
    

Alternatively, `i.start >= end_ and end_ = i.end` works, too.

## Python

    
    
    def eraseOverlapIntervals(self, intervals):
        end = float('-inf')
        erased = 0
        for i in sorted(intervals, key=lambda i: i.end):
            if i.start >= end:
                end = i.end
            else:
                erased += 1
        return erased


### Solution 3
    
    
    class Solution {
    public:
        int eraseOverlapIntervals(vector<Interval>& intervals) {
            auto comp = [](const Interval& i1, const Interval& i2){ return i1.start < i2.start; };
            sort(intervals.begin(), intervals.end(), comp);
            int res = 0, pre = 0;
            for (int i = 1; i < intervals.size(); i++) {
                if (intervals[i].start < intervals[pre].end) {
                    res++;
                    if (intervals[i].end < intervals[pre].end) pre = i;
                }
                else pre = i;
            }
            return res;
        }
    };
    



