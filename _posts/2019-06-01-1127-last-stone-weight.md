---
layout: post
title: 1127. Last Stone Weight
---
### Question
We have a collection of rocks, each rock has a positive integer weight.

Each turn, we choose the two **heaviest**  rocks and smash them together.
Suppose the stones have weights `x` and `y` with `x <= y`.  The result of this
smash is:

  * If `x == y`, both stones are totally destroyed;
  * If `x != y`, the stone of weight `x` is totally destroyed, and the stone of weight `y` has new weight `y-x`.

At the end, there is at most 1 stone left.  Return the weight of this stone
(or 0 if there are no stones left.)



 **Example 1:**

    
    
     **Input:** [2,7,4,1,8,1]
    **Output:** 1
    **Explanation:**
    We combine 7 and 8 to get 1 so the array converts to [2,4,1,1,1] then,
    we combine 2 and 4 to get 2 so the array converts to [2,1,1,1] then,
    we combine 2 and 1 to get 1 so the array converts to [1,1,1] then,
    we combine 1 and 1 to get 0 so the array converts to [1] then that's the value of last stone.



 **Note:**

  1. `1 <= stones.length <= 30`
  2. `1 <= stones[i] <= 1000`

### Solution 1
 **Java, PriorityQueue**

    
    
        public int lastStoneWeight(int[] A) {
            PriorityQueue<Integer> pq =  new PriorityQueue<>((a, b)-> b - a);
            for (int a : A)
                pq.offer(a);
            for (int i = 0; i < A.length - 1; ++i)
                pq.offer(pq.poll() - pq.poll());
            return pq.poll();
        }
    

**Python, Priority queue, o(nlogn) time**

    
    
        def lastStoneWeight( self, A):
            pq = [-x for x in A]
            heapq.heapify(pq)
            for i in xrange(len(A) - 1):
                x, y = -heapq.heappop(pq), -heapq.heappop(pq)
                heapq.heappush(pq, -abs(x - y))
            return -pq[0]
    

**Python, insort, o(n^2) time**

    
    
        def lastStoneWeight(self, A):
            stones = sorted(stones)
             for _ in xrange(len(stones) - 1):
                x, y = stones.pop(), stones.pop()
                bisect.insort(stones, abs(x - y))
            return stones.pop()
    
    


### Solution 2
Sort stones descendingly in PriorityQueue, then pop out pair by pair, compute
the difference between them and add back to PriorityQueue.

Note: since we already know the first poped out is not smaller, it is not
necessary to use Math.abs().

    
    
        public int lastStoneWeight(int[] stones) {
            PriorityQueue<Integer> q = new PriorityQueue<>(Comparator.reverseOrder());
            for (int st : stones) { q.offer(st); }
            while (q.size() > 1) {
                q.offer(q.poll() - q.poll());
            }
            return q.peek();
        }
    

**Analysis:**

Time: O(nlogn), space: O(n), where n = stones.length.

 **Q & A:**

Q: If not adding zeroes in the queue when polling out two elements are equal,
is the result same as the above code?

A: Yes. 0s are always at the end of the PriorityQueue. No matter a positive
deduct 0 or 0 deduct 0, the result is same as NOT adding 0s into the
PriorityQueue.


### Solution 3
    
    
    int lastStoneWeight(vector<int>& st) {
      multiset<int> s(begin(st), end(st));
      while (s.size() > 1) {
        auto w1 = *prev(s.end());
        s.erase(prev(s.end()));
        auto w2 = *prev(s.end());
        s.erase(prev(s.end()));
        if (abs(w1 - w2) > 0) s.insert(abs(w1 - w2));
      }
      return s.empty() ? 0 : *s.begin();
    }
    



