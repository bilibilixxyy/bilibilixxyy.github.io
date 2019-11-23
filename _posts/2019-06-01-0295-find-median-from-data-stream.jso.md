---
layout: post
title: 295. Find Median from Data Stream
---
### Question
Median is the middle value in an ordered integer list. If the size of the list
is even, there is no middle value. So the median is the mean of the two middle
value.

For example,

`[2,3,4]`, the median is `3`

`[2,3]`, the median is `(2 + 3) / 2 = 2.5`

Design a data structure that supports the following two operations:

  * void addNum(int num) - Add a integer number from the data stream to the data structure.
  * double findMedian() - Return the median of all elements so far.



 **Example:**

    
    
    addNum(1)
    addNum(2)
    findMedian() -> 1.5
    addNum(3) 
    findMedian() -> 2
    



 **Follow up:**

  1. If all integer numbers from the stream are between 0 and 100, how would you optimize it?
  2. If 99% of all integer numbers from the stream are between 0 and 100, how would you optimize it?

### Solution 1
I keep two heaps (or priority queues):

  * Max-heap `small` has the smaller half of the numbers.
  * Min-heap `large` has the larger half of the numbers.

This gives me direct access to the one or two middle values (they're the tops
of the heaps), so getting the median takes O(1) time. And adding a number
takes O(log n) time.

Supporting both min- and max-heap is more or less cumbersome, depending on the
language, so I simply negate the numbers in the heap in which I want the
reverse of the default order. To prevent this from causing a bug with -231
(which negated is itself, when using 32-bit ints), I use integer types larger
than 32 bits.

Using larger integer types also prevents an overflow error when taking the
mean of the two middle numbers. I think almost all solutions posted previously
have that bug.

 **Update:** These are pretty short already, but by now I wrote [even shorter
ones](https://leetcode.com/discuss/64910/very-short-o-log-n-o-1).

* * *

 **Java**

    
    
     class MedianFinder {
    
        private Queue<Long> small = new PriorityQueue(),
                            large = new PriorityQueue();
    
        public void addNum(int num) {
            large.add((long) num);
            small.add(-large.poll());
            if (large.size() < small.size())
                large.add(-small.poll());
        }
    
        public double findMedian() {
            return large.size() > small.size()
                   ? large.peek()
                   : (large.peek() - small.peek()) / 2.0;
        }
    };
    

Props to [larrywang2014's solution](https://leetcode.com/discuss/64842/32ms-
easy-to-understand-java-solution) for making me aware that I can use Queue in
the declaration instead of PriorityQueue (that's all I got from him, though
(just saying because I just saw he changed his previously longer addNum and
it's now equivalent to mine)).

* * *

**C++**

    
    
     class MedianFinder {
        priority_queue<long> small, large;
    public:
    
        void addNum(int num) {
            small.push(num);
            large.push(-small.top());
            small.pop();
            if (small.size() < large.size()) {
                small.push(-large.top());
                large.pop();
            }
        }
    
        double findMedian() {
            return small.size() > large.size()
                   ? small.top()
                   : (small.top() - large.top()) / 2.0;
        }
    };
    

Big thanks to jianchao.li.fighter for telling me that C++'s priority_queue is
a max-queue (see comments below).

* * *

**Python**

    
    
    from heapq import *
    
     class MedianFinder:
    
        def __init__(self):
            self.heaps = [], []
    
        def addNum(self, num):
            small, large = self.heaps
            heappush(small, -heappushpop(large, num))
            if len(large) < len(small):
                heappush(large, -heappop(small))
    
        def findMedian(self):
            small, large = self.heaps
            if len(large) > len(small):
                return float(large[0])
            return (large[0] - small[0]) / 2.0


### Solution 2
Not sure why it is marked as hard, i think this is one of the easiest
questions on leetcode.

    
    
    class MedianFinder {
        // max queue is always larger or equal to min queue
        PriorityQueue<Integer> min = new PriorityQueue();
        PriorityQueue<Integer> max = new PriorityQueue(1000, Collections.reverseOrder());
        // Adds a number into the data structure.
        public void addNum(int num) {
            max.offer(num);
            min.offer(max.poll());
            if (max.size() < min.size()){
                max.offer(min.poll());
            }
        }
    
        // Returns the median of current data stream
        public double findMedian() {
            if (max.size() == min.size()) return (max.peek() + min.peek()) /  2.0;
            else return max.peek();
        }
    };


### Solution 3
The invariant of the algorithm is two heaps, small and large, each represent
half of the current list. The length of smaller half is kept to be n / 2 at
all time and the length of the larger half is either n / 2 or n / 2 + 1 depend
on n's parity.

This way we only need to peek the two heaps' top number to calculate median.

Any time before we add a new number, there are two scenarios, (total n
numbers, k = n / 2):

    
    
    (1) length of (small, large) == (k, k)
    (2) length of (small, large) == (k, k + 1)
    

After adding the number, total (n + 1) numbers, they will become:

    
    
    (1) length of (small, large) == (k, k + 1)
    (2) length of (small, large) == (k + 1, k + 1)
    

Here we take the first scenario for example, we know the large will gain one
more item and small will remain the same size, but we cannot just push the
item into large. What we should do is we push the new number into small and
pop the maximum item from small then push it into large (all the pop and push
here are heappop and heappush). By doing this kind of operations for the two
scenarios we can keep our invariant.

Therefore to add a number, we have 3 O(log n) heap operations. Luckily the
heapq provided us a function "heappushpop" which saves some time by combine
two into one. The document says:

> Push item on the heap, then pop and return the smallest item from the heap.
The combined action runs more efficiently than heappush() followed by a
separate call to heappop().

Alltogether, the add operation is O(logn), The findMedian operation is O(1).

Note that the heapq in python is a min heap, thus we need to invert the values
in the smaller half to mimic a "max heap".

A further observation is that the two scenarios take turns when adding
numbers, thus it is possible to combine the two into one. For this please see
[stefan's post](https://leetcode.com/discuss/64910/very-short-o-log-n-o-1)

**Java**

    
    
    private PriorityQueue< Integer> small = new PriorityQueue<>(Collections.reverseOrder());
    private PriorityQueue<Integer> large = new PriorityQueue<>();
    private boolean even = true;
    
    public double findMedian() {
        if (even)
            return (small.peek() + large.peek()) / 2.0;
        else
            return small.peek();
    }
    
    public void addNum(int num) {
        if (even) {
            large.offer(num);
            small.offer(large.poll());
        } else {
            small.offer(num);
            large.offer(small.poll());
        }
        even = !even;
    }
    

**Python**

    
    
    from heapq import *
    
    
     class MedianFinder:
        def __init__(self):
            self.small = []  # the smaller half of the list, max heap (invert min-heap)
            self.large = []  # the larger half of the list, min heap
    
        def addNum(self, num):
            if len(self.small) == len(self.large):
                heappush(self.large, -heappushpop(self.small, -num))
            else:
                heappush(self.small, -heappushpop(self.large, num))
    
        def findMedian(self):
            if len(self.small) == len(self.large):
                return float(self.large[0] - self.small[0]) / 2.0
            else:
                return float(self.large[0])
    
    # 18 / 18 test cases passed.
    # Status: Accepted
    # Runtime: 388 ms
    



