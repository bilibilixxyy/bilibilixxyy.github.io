---
layout: post
title: 732. My Calendar III
---
### Question
Implement a `MyCalendarThree` class to store your events. A new event can
**always** be added.

Your class will have one method, `book(int start, int end)`. Formally, this
represents a booking on the half open interval `[start, end)`, the range of
real numbers `x` such that `start <= x < end`.

A _K-booking_ happens when **K** events have some non-empty intersection (ie.,
there is some time that is common to all K events.)

For each call to the method `MyCalendar.book`, return an integer `K`
representing the largest integer such that there exists a `K`-booking in the
calendar.

Your class will be called like this: `MyCalendarThree cal = new
MyCalendarThree();` `MyCalendarThree.book(start, end)`

 **Example 1:**

    
    
    MyCalendarThree();
    MyCalendarThree.book(10, 20); // returns 1
    MyCalendarThree.book(50, 60); // returns 1
    MyCalendarThree.book(10, 40); // returns 2
    MyCalendarThree.book(5, 15); // returns 3
    MyCalendarThree.book(5, 10); // returns 3
    MyCalendarThree.book(25, 55); // returns 3
    **Explanation:** 
    The first two events can be booked and are disjoint, so the maximum K-booking is a 1-booking.
    The third event [10, 40) intersects the first event, and the maximum K-booking is a 2-booking.
    The remaining events cause the maximum K-booking to be only a 3-booking.
    Note that the last event locally causes a 2-booking, but the answer is still 3 because
    eg. [10, 20), [10, 40), and [5, 15) are still triple booked.
    



 **Note:**

  * The number of calls to `MyCalendarThree.book` per test case will be at most `400`.
  * In calls to `MyCalendarThree.book(start, end)`, `start` and `end` are integers in the range `[0, 10^9]`.

### Solution 1
 **Summarize**  
`This is to find the maximum number of concurrent ongoing event at any time.`

We can log the `start` & `end` of each event on the timeline, each `start` add
a new ongoing event at that time, each `end` terminate an ongoing event. Then
we can scan the timeline to figure out the maximum number of ongoing event at
any time.

The most intuitive data structure for `timeline` would be `array`, but the
time spot we have could be very `sparse`, so we can use `sorted map` to
simulate the time line to save space.

 **Java**

    
    
     class MyCalendarThree {
        private TreeMap<Integer, Integer> timeline = new TreeMap<>();
        public int book(int s, int e) {
            timeline.put(s, timeline.getOrDefault(s, 0) + 1); // 1 new event will be starting at [s]
            timeline.put(e, timeline.getOrDefault(e, 0) - 1); // 1 new event will be ending at [e];
            int ongoing = 0, k = 0;
            for (int v : timeline.values())
                k = Math.max(k, ongoing += v);
            return k;
        }
    }
    

**C++**

    
    
     class MyCalendarThree {
        map<int, int> timeline;
    public:
        int book(int s, int e) {
            timeline[s]++; // 1 new event will be starting at [s]
            timeline[e]--; // 1 new event will be ending at [e];
            int ongoing = 0, k = 0;
            for (pair<int, int> t : timeline)
                k = max(k, ongoing += t.second);
            return k;
        }
    };
    


### Solution 2
Iterate and increment only the time point in the time window.  
Runtime 40ms, beats 95%+  
Only half compared with iteration on whole map.

If `count` has already the key, it won't change anything.  
`upper_bound` and `lower_bound` both work.

`emplace` returns a pair of an iterator to the newly inserted element and a
value of true.  
Otherwise, it returns an iterator to the equivalent element within the
container and a value of false.

    
    
        map<int, int> count = {{-1,0}};
        int res = 0;
        int book(int s, int e) {
            auto start = count.emplace(s, (--count.lower_bound(s))->second).first;
            auto end = count.emplace(e, (--count.lower_bound(e))->second).first;
            for (auto i = start; i != end; ++i) res = max(res, ++(i->second));
            return res;
        }
    


### Solution 3
Can some one explain me,

  1. Why would _MyCalendarThree.book(5, 10);_ return 3 graphing it shows only one other overlap so it should be 2
  2. And this sentence _"Note that the last event locally causes a 2-booking, but the answer is still 3 because  
eg. [10, 20), [10, 40), and [5, 15) are still triple booked."_ the last event
refers to MyCalendarThree.book(25, 55); why would it locally return 2



