---
layout: post
title: 789. Kth Largest Element in a Stream
---
### Question
Design a class to find the **k** th largest element in a stream. Note that it
is the kth largest element in the sorted order, not the kth distinct element.

Your `KthLargest` class will have a constructor which accepts an integer `k`
and an integer array `nums`, which contains initial elements from the stream.
For each call to the method `KthLargest.add`, return the element representing
the kth largest element in the stream.

 **Example:**

    
    
    int k = 3;
    int[] arr = [4,5,8,2];
    KthLargest kthLargest = new KthLargest(3, arr);
    kthLargest.add(3);   // returns 4
    kthLargest.add(5);   // returns 5
    kthLargest.add(10);  // returns 5
    kthLargest.add(9);   // returns 8
    kthLargest.add(4);   // returns 8
    

**Note:**  
You may assume that `nums`' length ≥ `k-1` and `k` ≥ 1.

### Solution 1
Keep track of the k biggest elements in the minimum priority queue `q`.
`q.peek()` is the answer.

    
    
       class KthLargest {
            final PriorityQueue<Integer> q;
            final int k;
    
            public KthLargest(int k, int[] a) {
                this.k = k;
                q = new PriorityQueue<>(k);
                for (int n : a)
                    add(n);
            }
    
            public int add(int n) {
                if (q.size() < k)
                    q.offer(n);
                else if (q.peek() < n) {
                    q.poll();
                    q.offer(n);
                }
                return q.peek();
            }
        }


### Solution 2
    
    
    class KthLargest(object):
    
        
        def __init__(self, k, nums):
            self.pool = nums
            self.k = k
            heapq.heapify(self.pool)
            while len(self.pool) > k:
                heapq.heappop(self.pool)
    
                
        def add(self, val):
            if len(self.pool) < self.k:
                heapq.heappush(self.pool, val)
            elif val > self.pool[0]:
                heapq.heapreplace(self.pool, val)
            return self.pool[0]
        
    


### Solution 3
We can build a `MinHeap` that contains only `k` largest elements.  
On `add`:

  * compare a new element `x` with `min` to decide if we should `pop min` and `insert x`
  * take into account a case when `heap_size` is less than `k`

Construction is simply calling the `add` function `N` times.

Time complexity:

  * Construction: `O(N * logK)`
  * Adding: `O(logK)`

Additional memory:

  * `O(K)` (can be reduced to `O(1)` by reusing memory of the existing array)

Have fun!



