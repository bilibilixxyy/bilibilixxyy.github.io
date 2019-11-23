---
layout: post
title: 373. Find K Pairs with Smallest Sums
---
### Question
You are given two integer arrays **nums1** and **nums2** sorted in ascending
order and an integer **k**.

Define a pair **(u,v)** which consists of one element from the first array and
one element from the second array.

Find the k pairs **(u 1,v1),(u2,v2) ...(uk,vk)** with the smallest sums.

 **Example 1:**

    
    
     **Input:** nums1 = [1,7,11], nums2 = [2,4,6], k = 3
    **Output:** [[1,2],[1,4],[1,6]] 
    **Explanation:** The first 3 pairs are returned from the sequence: 
                 [1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]

**Example 2:**

    
    
    **Input:** nums1 = [1,1,2], nums2 = [1,2,3], k = 2
    **Output:** [1,1],[1,1]
    **Explanation:** The first 2 pairs are returned from the sequence: 
                 [1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]

**Example 3:**

    
    
    **Input:** nums1 = [1,2], nums2 = [3], k = 3
    **Output:** [1,3],[2,3]
    **Explanation:** All possible pairs are returned from the sequence: [1,3],[2,3]
    

### Solution 1
Basic idea: Use min_heap to keep track on next minimum pair sum, and we only
need to maintain K possible candidates in the data structure.

Some observations: For every numbers in nums1, its best partner(yields min
sum) always strats from nums2[0] since arrays are all sorted; And for a
specific number in nums1, its next candidate sould be **[this specific
number]** \+ **nums2[current_associated_index + 1]** , unless out of
boundary;)

Here is a simple example demonstrate how this algorithm works.

![image](https://cloud.githubusercontent.com/assets/8743900/17332795/0bb46cfe-589e-11e6-90b5-5d3c9696c4f0.png)

The run time complexity is O(kLogk) since que.size <= k and we do at most k
loop.

    
    
    public class Solution {
        public List<int[]> kSmallestPairs(int[] nums1, int[] nums2, int k) {
            PriorityQueue<int[]> que = new PriorityQueue<>((a,b)->a[0]+a[1]-b[0]-b[1]);
            List<int[]> res = new ArrayList<>();
            if(nums1.length==0 || nums2.length==0 || k==0) return res;
            for(int i=0; i<nums1.length && i<k; i++) que.offer(new int[]{nums1[i], nums2[0], 0});
            while(k-- > 0 && !que.isEmpty()){
                int[] cur = que.poll();
                res.add(new int[]{cur[0], cur[1]});
                if(cur[2] == nums2.length-1) continue;
                que.offer(new int[]{cur[0],nums2[cur[2]+1], cur[2]+1});
            }
            return res;
        }
    }
    


### Solution 2
Several solutions from naive to more elaborate. I found it helpful to
visualize the input as an **m×n matrix** of sums, for example for
nums1=[1,7,11], and nums2=[2,4,6]:

    
    
           2   4   6
       +------------
     1 |  3   5   7
     7 |  9  11  13
    11 | 13  15  17
    

Of course the smallest pair overall is in the top left corner, the one with
sum 3. We don't even need to look anywhere else. After including that pair in
the output, the next-smaller pair must be the next on the right (sum=5) or the
next below (sum=9). We can keep a "horizon" of possible candidates,
implemented as a heap / priority-queue, and roughly speaking we'll grow from
the top left corner towards the right/bottom. That's what my solution 5 does.
Solution 4 is similar, not quite as efficient but a lot shorter and my
favorite.  
  

## **Solution 1: Brute Force** (accepted in 560 ms)

Just produce all pairs, sort them by sum, and return the first k.

    
    
    def kSmallestPairs(self, nums1, nums2, k):
        return sorted(itertools.product(nums1, nums2), key=sum)[:k]
    

## **Solution 2: Clean Brute Force** (accepted in 532 ms)

The above produces tuples and while the judge doesn't care, it's cleaner to
make them lists as requested:

    
    
    def kSmallestPairs(self, nums1, nums2, k):
        return map(list, sorted(itertools.product(nums1, nums2), key=sum)[:k])
    

## **Solution 3: Less Brute Force** (accepted in 296 ms)

Still going through all pairs, but only with a generator and
`heapq.nsmallest`, which uses a heap of size k. So this only takes O(k) extra
memory and O(mn log k) time.

    
    
    def kSmallestPairs(self, nums1, nums2, k):
        return map(list, heapq.nsmallest(k, itertools.product(nums1, nums2), key=sum))
    

Or (accepted in 368 ms):

    
    
    def kSmallestPairs(self, nums1, nums2, k):
        return heapq.nsmallest(k, ([u, v] for u in nums1 for v in nums2), key=sum)
    

## **Solution 4: Efficient** (accepted in 112 ms)

The brute force solutions computed the whole matrix (see visualization above).
This solution doesn't. It turns each row into a generator of triples [u+v, u,
v], only computing the next when asked for one. And then merges these
generators with a heap. Takes O(m + k*log(m)) time and O(m) extra space.

    
    
    def kSmallestPairs(self, nums1, nums2, k):
        streams = map(lambda u: ([u+v, u, v] for v in nums2), nums1)
        stream = heapq.merge(*streams)
        return [suv[1:] for suv in itertools.islice(stream, k)]
    

## **Solution 5: More efficient** (accepted in 104 ms)

The previous solution right away considered (the first pair of) all matrix
rows (see visualization above). This one doesn't. It starts off only with the
very first pair at the top-left corner of the matrix, and expands from there
as needed. Whenever a pair is chosen into the output result, the next pair in
the row gets added to the priority queue of current options. Also, if the
chosen pair is the first one in its row, then the first pair in the next row
is added to the queue.

    
    
    def kSmallestPairs(self, nums1, nums2, k):
        queue = []
        def push(i, j):
            if i < len(nums1) and j < len(nums2):
                heapq.heappush(queue, [nums1[i] + nums2[j], i, j])
        push(0, 0)
        pairs = []
        while queue and len(pairs) < k:
            _, i, j = heapq.heappop(queue)
            pairs.append([nums1[i], nums2[j]])
            push(i, j + 1)
            if j == 0:
                push(i + 1, 0)
        return pairs


### Solution 3
This problem is exactly the same as Leetcode378 Kth Smallest Element in a
Sorted Matrix, the only difference is this problem give two array while 378
gives a matrix, but they are the same. You can check my previous post for 378
to see how it works.  
<https://discuss.leetcode.com/topic/52948/share-my-thoughts-and-solution>

    
    
    public class Solution {
        public List<int[]> kSmallestPairs(int[] nums1, int[] nums2, int k) {
            PriorityQueue<Tuple> pq = new PriorityQueue<Tuple>();
            int m = nums1.length, n = nums2.length;
            List<int[]> res = new ArrayList<int[]>();
            if(nums1 == null || nums1.length == 0 || nums2 == null || nums2.length == 0 || k <= 0) return res;
            for(int j = 0; j <= n-1; j++) pq.offer(new Tuple(0, j, nums1[0]+nums2[j]));
            for(int i = 0; i < Math.min(k, m *n); i++) {
                Tuple t = pq.poll();
                res.add(new int[]{nums1[t.x], nums2[t.y]});
                if(t.x == m - 1) continue;
                pq.offer(new Tuple (t.x + 1, t.y, nums1[t.x + 1] + nums2[t.y]));
            }
            return res;
        }
    }
    
    class Tuple implements Comparable<Tuple> {
        int x, y, val;
        public Tuple (int x, int y, int val) {
            this.x = x;
            this.y = y;
            this.val = val;
        }
        
        @Override
        public int compareTo (Tuple that) {
            return this.val - that.val;
        }
    }
    



