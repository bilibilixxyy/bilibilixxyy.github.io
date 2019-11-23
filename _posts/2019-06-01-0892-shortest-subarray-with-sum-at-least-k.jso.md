---
layout: post
title: 892. Shortest Subarray with Sum at Least K
---
### Question
Return the **length** of the shortest, non-empty, contiguous  subarray of `A`
with sum at least `K`.

If there is no non-empty subarray with sum at least `K`, return `-1`.



 **Example 1:**

    
    
     **Input:** A = [1], K = 1
    **Output:** 1
    

**Example 2:**

    
    
    **Input:** A = [1,2], K = 4
    **Output:** -1
    

**Example 3:**

    
    
    **Input:** A = [2,-1,2], K = 3
    **Output:** 3
    



 **Note:**

  1. `1 <= A.length <= 50000`
  2. `-10 ^ 5 <= A[i] <= 10 ^ 5`
  3. `1 <= K <= 10 ^ 9`

### Solution 1
Calculate prefix sum `B` of list `A`.  
`B[j] - B[i]` represents the sum of subarray `A[i] ~ A[j-1]`  
Deque `d` will keep indexes of increasing `B[i]`.  
For every `B[i]`, we will compare `B[i] - B[d[0]]` with `K`.

 **Time Complexity** :  
Loop on `B` `O(N)`  
Every index will be pushed only once into deque. `O(N)`

 **C++:**

    
    
         int shortestSubarray(vector<int> A, int K) {
            int N = A.size(), res = N + 1;
            vector<int> B(N + 1, 0);
            for (int i = 0; i < N; i++) B[i + 1] = B[i] + A[i];
            deque<int> d;
            for (int i = 0; i < N + 1; i++) {
                while (d.size() > 0 && B[i] - B[d.front()] >= K)
                    res = min(res, i - d.front()), d.pop_front();
                while (d.size() > 0 && B[i] <= B[d.back()]) d.pop_back();
                d.push_back(i);
            }
            return res <= N ? res : -1;
        }
    

**Java:**

    
    
         public int shortestSubarray(int[] A, int K) {
            int N = A.length, res = N + 1;
            int[] B = new int[N + 1];
            for (int i = 0; i < N; i++) B[i + 1] = B[i] + A[i];
            Deque<Integer> d = new ArrayDeque<>();
            for (int i = 0; i < N + 1; i++) {
                while (d.size() > 0 && B[i] - B[d.getFirst()] >=  K)
                    res = Math.min(res, i - d.pollFirst());
                while (d.size() > 0 && B[i] <= B[d.getLast()]) d.pollLast();
                d.addLast(i);
            }
            return res <= N ? res : -1;
        }
    

**Python:**

    
    
        def shortestSubarray(self, A, K):
            N = len(A)
            B = [ 0] * (N + 1)
            for i in range(N): B[i + 1] = B[i] + A[i]
            d = collections.deque()
            res = N + 1
            for i in xrange(N + 1):
                while d and B[i] - B[d[0]] >= K: res = min(res, i - d.popleft())
                while d and B[i] <= B[d[-1]]: d.pop()
                d.append(i)
            return res if res <= N else -1
    

  

## **FAQ**

 **Q: How to think of such solutions?**  
A: Basic idea, for array starting at every `A[i]`, find the shortest one with
sum at leat `K`.  
In my solution, for `B[i]`, find the smallest `j` that `B[j] - B[i] >= K`.  
Keep this in mind for understanding two while loops.

 **Q: What is the purpose of first while loop?**  
A: For the current prefix sum `B[i]`, it covers all subarray ending at
`A[i-1]`.  
We want know if there is a subarray, which starts from an index, ends at
`A[i-1]` and has at least sum `K`.  
So we start to compare `B[i]` with the smallest prefix sum in our deque, which
is `B[D[0]]`, hoping that `[i] - B[d[0]] >= K`.  
So if `B[i] - B[d[0]] >= K`, we can update our result `res = min(res, i -
d.popleft())`.  
The `while` loop helps compare one by one, until this condition isn't valid
anymore.

 **Q: Why we pop left in the first while loop?**  
A: This the most tricky part that improve my solution to get only `O(N)`.  
D[0] exists in our deque, it means that before `B[i]`, we didn't find a
subarray whose sum at least `K`.  
B[i] is the first prefix sum that valid this condition.  
In other words, `A[D[0]] ~ A[i-1]` is the shortest subarray starting at
A[D[0]] with sum at least `K`.  
We have already find it for `A[D[0]]` and it can't be shorter, so we can drop
it from our deque.

 **Q: What is the purpose of second while loop?**  
A: To keep `B[D[i]]` increasing in the deque.

 **Q: Why keep the deque increase?**  
A: If `B[i] <= B[d.back()]` and moreover we already know that `i > d.back()`,
it means that compared with `d.back()`,  
`B[i]` can help us make the subarray length shorter and sum bigger. So no need
to keep `d.back()` in our deque.  
  
  
Please let me know if there is still anything unclear.


### Solution 2
What makes this problem hard is that we have negative values. If you haven't
already done the problem with positive integers only, I highly recommend
solving it first, as I will use its `Sliding Window` solution to reach the
Deque solution here. You can find the problem here :
<https://leetcode.com/problems/minimum-size-subarray-sum/description/> , and a
`Sliding window` solution here : <https://leetcode.com/problems/minimum-size-
subarray-sum/discuss/59078/Accepted-clean-Java-O(n)-solution-(two-pointers)>.

# Recall of the Sliding window solution in a positive array

The `Sliding window` solution finds the subarray we are looking for in a
`linear` time complexity. The idea behind it is to maintain two pointers:
**start** and **end** , moving them in a `smart way` to avoid examining all
possible values `0<=end<=n-1` and `0<=start<=end` (to avoid brute force).  
What it does is:

  1. Incremeting the **end** pointer while the sum of current subarray (defined by current values of `start` and `end`) is smaller than the target.
  2. `Once we satisfy` our condition (the sum of current subarray >= target) we keep `incrementing` the **start** pointer until we `violate` it (until `sum(array[start:end+1]) < target`).
  3. Once we violate the condition we keep incrementing the **end** pointer until the condition is satisfied again and so on.

The reason why we stop incrementing `start` when we violate the condition is
that we are sure we will not satisfy it again if we keep incrementing `start`.
In other words, if the sum of the current subarray `start -> end` is smaller
than the target then the sum of `start+1 -> end` is neccessarily smaller than
the target. (positive values)  
The problem with this solution is that it doesn't work if we have negative
values, this is because of the sentence above `Once we "violate" the condition
we stop incrementing start`.

# Problem of the Sliding window with negative values

Now, let's take an example with negative values `nums = [3, -2, 5]` and
`target=4`. Initially `start=0`, we keep moving the **end** pointer until we
satisfy the condition, here we will have `start=0` and `end=2`. Now we are
going to move the start pointer `start=1`. The sum of the current subarray is
`-2+5=3 < 4` so we violate the condition. However if we just move the
**start** pointer another time `start=2` we will find `5 >= 4` and we are
satisfying the condition. And this is not what the Sliding window assumes.

# Deque solution

The Deque solution is just a `modification` of the Sliding window solution
above. We will modify the way we are updating `start`.  
Let's explain the Deque solution based on the code of @Lee215 by answering
some questions :

    
    
        def shortestSubarray(self, A, K):
            N = len(A)
            B = [0] * (N + 1)
            for i in range(N): B[i + 1] = B[i] + A[i]
            d = collections.deque()
            res = N + 1
            for i in xrange(N + 1):
                while d and B[i] - B[d[0]] >= K: res = min(res, i - d.popleft())
                while d and B[i] <= B[d[-1]]: d.pop()
                d.append(i)
            return res if res <= N else -1
    

**What does the Deque store :**  
The deque stores the `possible` values of the **start** pointer. Unlike the
sliding window, values of the `start` variable will not necessarily be
contiguous.

 **Why is it increasing :**  
So that when we move the **start** pointer and we violate the condition, we
are sure we will violate it if we keep taking the other values from the Deque.
In other words, if the sum of the subarray from `start=first value in the
deque` to `end` is smaller than `target`, then the sum of the subarray from
`start=second value in the deque` to `end` is necessarily smaller than
`target`.  
So because the Deque is increasing (`B[d[0]] <= B[d[1]]`), we have `B[i] -
B[d[0]] >= B[i] - B[d[1]]`, which means the sum of the subarray starting from
`d[0]` is greater than the sum of the sub array starting from `d[1]`.

 **Why are we having a prefix array and not just the initial array like in the
sliding window :**  
Because in the sliding window when we move `start` (typically when we
increment it) we can just substract `nums[start-1]` from the current sum and
we get the sum of the new subarray. Here the value of the `start` is `jumping`
and one way to compute the sum of the current subarray in a `constant` time is
to have the prefix array.

 **Why using Deque and not simply an array :**  
We can use an array, however we will find ourselves doing only three
operations:  
1- `remove_front` : when we satisfy our condition and we want to move the
start pointer  
2- `append_back` : for any index that may be a future _start pointer_  
3- `remove_back` : When we are no longer satisfying the increasing order of
the array  
Deque enables doing these 3 operations in a constant time.


### Solution 3
# Monotonic Queue

Hope it helps.

The following question can be solved by monotonic queue:

  *  **LC84. Largest Rectangle in Histogram**
  *  **LC239. Sliding Window Maximum**
  *  **LC739. Daily Temperatures**
  *  **LC862. Shortest Subarray with Sum at Least K**
  *  **LC901. Online Stock Span**
  *  **LC907. Sum of Subarray Minimums**

In general, the following "prototype" problems can be solved by monotonic
queue:

## Any DP problem where `A[i] = min(A[j:k]) + C` where `j < k <= i`

This is a sliding max/min window problem.

The task is to return the max/min elements in some sliding window. For
example, we want a running max in the sliding windows, `amax =
max(A[i:i+width])`.

Key observation: Given input array `A`, when `A[l] < A[r]` for `l < r`, then
`A[l]` should never be retuned as the sliding max `amax`, once `A[r]` has
entered the sliding window.

So we maintain a monotonic array with index **increasing** and value
**decreasing** , because smaller elements like `A[l]` on the left are useless.

For example, with sliding window of fixed length 3,

> `A = [3, 1, 4, 3, 8] => monotonic queue is like [3], [3, 1], [4], [4, 3],
[8]`  
> when element `4` enters, we remove `[3, 1]` because they are on the left and
smaller than `4`, no chance being chosen as the max element.

The head of the increasing queue is the running max!

The only unique thing here is that we can keep the elements in the window
sorted. It brings great benefits because it takes O(1) to obtain the min/max
element in the window.

That's why any DP problem where `A[i] = min(A[j:k]) + C` for `j < k <= i` and
some constant `C` can be solved by Monotonic Queue.

## Find the nearest larger element on the left

Given array `A` and an element `A[i]`, the task is to find the maximum index
`j < i` such that `A[j] > A[i]`. Namely, `A[j]` is the nearest larger element
on the left of `A[i]`.

Key observation: given `A[k] < A[j] > A[i]` for `k < j < i`, `A[k]` never
become the **nearest** element larger than `A[i]` because of `A[j]`.

So we should have a decreasing monotonic queue here. The arrow indicates that
the mapping from element on the right to the nearest element on the left
larger than it. The elements in the valley are ignored.

![alt text](https://imgur.com/ZfQSOag.png=10x2)

 **LC 85. Maximal Rectangle**

Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle
containing only 1's and return its area.

Idea: convert 2D matrix to 1D height array. The task becomes **LC84. Largest
Rectangle in Histogram** which is essentially "finding the index of the
nearest previous value smaller than itself".

    
    
             if not matrix: return 0
            N, M = len(matrix), len(matrix[0])
            dp = [0] * (M + 1)
            area = 0
                
            for i in range(N):
                for j in range(M):
                    # obtain the height based on each row
                    if matrix[i][j] == '1':
                        dp[j] += 1
                    else:
                        dp[j] = 0
                
                s = []
                for j in range(M + 1): # IMPORTANT: note that the last ZERO should pop out all remaining heights
                    if not s: s.append(j)
                    else:
                        while s and dp[s[-1]] >= dp[j]:
                            x = s.pop()
                            if s: area = max(area, dp[x]*(j - s[-1] - 1))
                            else: area = max(area, dp[x]*j)
                        s.append(j)
                
            return area
    

**LC862. Shortest Subarray with Sum at Least K**

Return the length of the shortest, non-empty, contiguous subarray of A with
sum at least K.

Key observation: If we accumulate array A to obtain B, then `B[l] <= B[r] - K`
indicates `sum(A[l:r]) >= K`. Given `B[r]`, the problem is equivalent to
finding the **nearest** previous element `B[l]` such that `B[l] <= B[r] - K`.

We maintain a **increasing queue** here because, given a new `B[i]`, the
larger element on the left are inferior than `B[i]` as a candidate to make
some future element `B[j] >= B[i] + K` (`j > i`).

One extra optimization learnt from
[@lee215](https://leetcode.com/problems/shortest-subarray-with-sum-at-
least-k/discuss/143726/C%2B%2BJavaPython-O\(N\)-Using-Deque) is that we can
also pop up the element on the left side `<= B[i] - K` of the **increasing**
queue because, given current element `B[i]`, if a future element `B[j] >
B[i]`, then `B[j] - K` would be within the queue after the removal of such
elements `<= B[i] - K`; Otherwise, if a future element `B[j] > B[i]` then it
never appears in the final results.

    
    
            Q = collections.deque([])
            
            B = [ 0]
            for a in A: B.append(B[-1] + a)
                
            res = float('inf')
            for i, b in enumerate(B):
                if not Q: Q.append(i)
                else:
                    while Q and B[Q[-1]] > b: Q.pop()
                    while Q and B[Q[0]] <= b - K:
                        res = min(res, i - Q[0])
                        Q.popleft()
                    Q.append(i)
            return res if res < float('inf') else -1
    

More notes are kept at [this github
page](https://github.com/xiaoylu/leetcode_category/tree/master/MonotonicQueue)



