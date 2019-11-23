---
layout: post
title: 352. Data Stream as Disjoint Intervals
---
### Question
Given a data stream input of non-negative integers a1, a2, ..., an, ...,
summarize the numbers seen so far as a list of disjoint intervals.

For example, suppose the integers from the data stream are 1, 3, 7, 2, 6, ...,
then the summary will be:

    
    
    [1, 1]
    [1, 1], [3, 3]
    [1, 1], [3, 3], [7, 7]
    [1, 3], [7, 7]
    [1, 3], [6, 7]
    

**Follow up:**  
What if there are lots of merges and the number of disjoint intervals are
small compared to the data stream's size?

 **NOTE:**  input types have been changed on April 15, 2019. Please reset to
default code definition to get new method signature.

### Solution 1
Use TreeMap to easily find the lower and higher keys, the key is the start of
the interval.  
Merge the lower and higher intervals when necessary. The time complexity for
adding is O(logN) since lowerKey(), higherKey(), put() and remove() are all
O(logN). It would be O(N) if you use an ArrayList and remove an interval from
it.

    
    
    public class SummaryRanges {
        TreeMap<Integer, Interval> tree;
    
        public SummaryRanges() {
            tree = new TreeMap<>();
        }
    
        public void addNum(int val) {
            if(tree.containsKey(val)) return;
            Integer l = tree.lowerKey(val);
            Integer h = tree.higherKey(val);
            if(l != null && h != null && tree.get(l).end + 1 == val && h == val + 1) {
                tree.get(l).end = tree.get(h).end;
                tree.remove(h);
            } else if(l != null && tree.get(l).end + 1 >= val) {
                tree.get(l).end = Math.max(tree.get(l).end, val);
            } else if(h != null && h == val + 1) {
                tree.put(val, new Interval(val, tree.get(h).end));
                tree.remove(h);
            } else {
                tree.put(val, new Interval(val, val));
            }
        }
    
        public List<Interval> getIntervals() {
            return new ArrayList<>(tree.values());
        }
    }


### Solution 2
In general case, vector is OK, it will take O(n) time in each add, and O(1) in
get result. But if there are lots of merges and the number of disjoint
intervals are small compared to the data stream's size, we'd better use
another data structure "set", because the insert operation in vector will cost
O(n) time, but it only cost O(log n) in binary search tree, but it will cost
O(n) time in getInterval. So use which data structure will depends.

first one is the solution use vector

    
    
    class SummaryRanges {
    public:
        void addNum(int val) {
            auto Cmp = [](Interval a, Interval b) { return a.start < b.start; };
            auto it = lower_bound(vec.begin(), vec.end(), Interval(val, val), Cmp);
            int start = val, end = val;
            if(it != vec.begin() && (it-1)->end+1 >= val) it--;
            while(it != vec.end() && val+1 >= it->start && val-1 <= it->end)
            {
                start = min(start, it->start);
                end = max(end, it->end);
                it = vec.erase(it);
            }
            vec.insert(it,Interval(start, end));
        }
        
        vector<Interval> getIntervals() {
            return vec;
        }
    private:
        vector<Interval> vec;
    };
    

and below is another solution use binary search tree.

    
    
    class SummaryRanges {
    public:
        /** Initialize your data structure here. */
        void addNum(int val) {
            auto it = st.lower_bound(Interval(val, val));
            int start = val, end = val;
            if(it != st.begin() && (--it)->end+1 < val) it++;
            while(it != st.end() && val+1 >= it->start && val-1 <= it->end)
            {
                start = min(start, it->start);
                end = max(end, it->end);
                it = st.erase(it);
            }
            st.insert(it,Interval(start, end));
        }
        
        vector<Interval> getIntervals() {
            vector<Interval> result;
            for(auto val: st) result.push_back(val);
            return result;
        }
    private:
        struct Cmp{
            bool operator()(Interval a, Interval b){ return a.start < b.start; }
        };
        set<Interval, Cmp> st;
    };


### Solution 3
Since there is no standard TreeMap library for python, I am implementing this
structure with a min heap.  
The idea is straight froward:  
Append interval to heap when addNum called  
Merge intervals when getIntervals called

    
    
    class SummaryRanges(object):
    
      def __init__(self):
        self.intervals = []
        
      def addNum(self, val):
        heapq.heappush(self.intervals, (val, Interval(val, val)))
        
      def getIntervals(self):
        stack = []
        while self.intervals:
            idx, cur = heapq.heappop(self.intervals)
            if not stack:
                stack.append((idx, cur))
            else:
                _, prev = stack[-1]
                if prev.end + 1 >= cur.start:
                    prev.end = max(prev.end, cur.end)
                else:
                    stack.append((idx, cur))
        self.intervals = stack
        return list(map(lambda x: x[1], stack))



