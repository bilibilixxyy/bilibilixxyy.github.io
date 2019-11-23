---
layout: post
title: 1037. Minimum Number of K Consecutive Bit Flips
---
### Question
In an array `A` containing only 0s and 1s, a _`K`-bit flip _ consists of
choosing a (contiguous) subarray of length `K` and simultaneously changing
every 0 in the subarray to 1, and every 1 in the subarray to 0.

Return the minimum number of `K`-bit flips required so that there is no 0 in
the array.  If it is not possible, return `-1`.



 **Example 1:**

    
    
     **Input:** A = [0,1,0], K = 1
    **Output:** 2
    **Explanation:** Flip A[0], then flip A[2].
    

**Example 2:**

    
    
    **Input:** A = [1,1,0], K = 2
    **Output:** -1
    **Explanation:**  No matter how we flip subarrays of size 2, we can't make the array become [1,1,1].
    

**Example 3:**

    
    
    **Input:** A = [0,0,0,1,0,1,1,0], K = 3
    **Output:** 3
    **Explanation:**
    Flip A[0],A[1],A[2]:  A becomes [1,1,1,1,0,1,1,0]
    Flip A[4],A[5],A[6]: A becomes [1,1,1,1,1,0,0,0]
    Flip A[5],A[6],A[7]: A becomes [1,1,1,1,1,1,1,1]
    



 **Note:**

  1. `1 <= A.length <= 30000`
  2. `1 <= K <= A.length`

### Solution 1
##  **Intuition** :

There is only one way to filp `A[0]`,  
and `A[0]` will tell us if we need to filp the range `A[0] ~ A[K -1]`.  
So we start from the leftmost one by one using a greedy idea to solve this
problem.

  

## Solution 1

 **Explanation**  
Create a new array `isFlipped[n]`.  
`isFlipped[i] = 1` iff we flip `K` consecutive bits starting at `A[i]`.

We maintain a variable `flipped` and `flipped = 1` iff the current bit is
flipped.

If `flipped = 0` and `A[i] = 0`, we need to flip at `A[i]`.  
If `flipped = 1` and `A[i] = 1`, we need to flip at `A[i]`.

 **Complexity**  
`O(N)` time for one pass  
`O(N)` extra space for `isFlipped[n]`.

 **Java**

    
    
         public int minKBitFlips(int[] A, int K) {
            int n = A.length, flipped = 0, res = 0;
            int[] isFlipped = new int[n];
            for (int i = 0; i < A.length; ++i) {
                if (i >= K)
                    flipped ^= isFlipped[i - K];
                if (flipped == A[i]) {
                    if (i + K > A.length)
                        return -1;
                    isFlipped[i] = 1;
                    flipped ^= 1;
                    res++;
                }
            }
            return res;
        }
    

  

## Solution 2

**Explanation**  
Instead an array `isFlipped` of size `n`,  
use a deque to maintain the state of a sliding window of size `k`.

**Complexity**  
`O(N)` time for one pass  
`O(K)` extra space for `isFlipped[n]`.

  

## Solution 3

**Explanation** :  
One pass, count the `cur` filp numbers in a sliding window of size `K`.  
If `cur` is even and A[i] is `0`, we need to flip.  
If `cur` is odd and A[i] is `1`, we need to flip.

**Complexity** :  
`O(N)` time for one pass  
`O(1)` extra space.

**Java:**

    
    
          public int minKBitFlips(int[] A, int K) {
            int cur = 0, res = 0;
            for (int i = 0; i < A.length; ++i) {
                if (i >= K) cur -= A[i - K] / 2;
                if ((cur & 1 ^ A[i]) == 0) {
                    if (i + K > A.length) return -1;
                    A[i] += 2;
                    cur++;
                    res++;
                }
            }
            return res;
        }
    

**C++:**

    
    
         int minKBitFlips(vector<int>& A, int K) {
            int cur = 0, res = 0;
            for (int i = 0; i < A.size(); ++i) {
                if (i >= K) cur -= A[i - K] / 2;
                if (cur & 1 ^ A[i] == 0) {
                    if (i + K > A.size()) return -1;
                    A[i] += 2;
                    cur++;
                    res++;
                }
            }
            return res;
        }
    

**Python:**

    
    
        def minKBitFlips(self, A, K):
            cur = res =  0
            for i in range(len(A)):
                if i >= K:
                    cur -= A[i - K] / 2
                if cur & 1 ^ A[i] == 0:
                    if i + K > len(A):
                        return -1
                    A[i] += 2
                    cur, res = cur + 1, res + 1
            return res
    


### Solution 2
The idea is, as we move through the array, everytime I see a 0, I need to do a
k-flip.  
As a result of a single k-flip, the k-1 elements to the right are also
affected.  
I use a queue, to keep track of how many valid flips have been executed for
the current index.  
By maintaining the last index for which a flip is valid, I can easily pop it
off, when I pass that index.  
Essentially this makes, the current size of queue equal to the number of flips
the current index has undergone and can determine if its 0 or 1, based on
that.  
So, if the number of flips is even, and my a[i] == 0, then I need to do one
more flip. I add that flip's last index to the queue  
In a similar way, if the number of flips is odd, and mu a[i] == 1, then i need
to do one more flip.

When to return -1 ?  
If at any point, we see that a necessary k-flip extends beyond the length of
the array, we know that this flip cant happen. Hence we can return -1 at that
instant.

    
    
    def minKBitFlips(self, a: 'List[int]', k: 'int') -> 'int':
            q = deque()
            res = 0
            for i in range(len(a)):
                if len(q) % 2 != 0:
                    if a[i] == 1:
                        res += 1
                        q.append(i+k-1)
                else:
                    if a[i] == 0:
                        res += 1
                        q.append(i+k-1)
                if q and q[0] == i: q.popleft()
                if q and q[-1] >= len(a): return -1
            return res
    

**O(1) space:**  
Now that we have the solution using queue, how do we get rid of the queue, and
use only O(1) space.  
The logic is exactly the same.  
But instead of the queue storing the indexes which are endpoints to a k-flip,
we use the array itself and use some addon value  > 1, that we add to the
value at the indexes which were earlier stored in the queue.  
Because we dont have the queue, we need an explicit variable flips (this is
equivalent to the length of the queue in the previous version).  
Now, whenever we encounter an a[i] that is >1 , we know that this is an
endpoint to a k-flip and reduce the number of flips accordingly.

Here is the code, which resemblems the previous version as close as possible,
but replaces the portion using the queue with flips and addon

[I didnot implement this O(1) version in the contest, but based on other's
posted solutions, realized, we can easily get rid of the queue in my original
version]

    
    
    def minKBitFlips(self, a: 'List[int]', k: 'int') -> 'int':
            res = 0
            flips = 0
            addon = 10
            for i in range(len(a)):
                if flips % 2 != 0:
                    if (a[i] - addon) % 2 == 1:
                        res += 1
                        if i+k-1 >= len(a): return -1
                        a[i+k-1] += addon
                        flips += 1
                else:
                    if (a[i] - addon) % 2 == 0:
                        res += 1
                        if i+k-1 >= len(a): return -1
                        a[i+k-1] += addon
                        flips += 1
                if a[i] > 1: 
                    flips -= 1
                    a[i] -= addon #Restore the array value back
            return res
    

The same solution, with combining if conditions:

    
    
    def minKBitFlips(self, a: 'List[int]', k: 'int') -> 'int':
            res = 0
            flips = 0
            addon = 10
            for i in range(len(a)):
                if (flips % 2 != 0 and (a[i] - addon) % 2 == 1) or (flips % 2 == 0 and (a[i] - addon) % 2 == 0):
                        res += 1
                        if i+k-1 >= len(a): return -1
                        a[i+k-1] += addon
                        flips += 1
                if a[i] > 1: 
                    flips -= 1
                    a[i] -= addon
            return res
    


### Solution 3
This is a cool problem that sounds very hard by becomes easy with two
intuition sparks (and a little twinkle).

# Intuition 1

Just want to share my thought process; I am sure that there is a formal proof
somewhere.

  1. Since K is fixed, it does not make sense to do the flip for any given index more than once. It's a XOR operation, even flips will be equal to zero flips, odd flips will be equal to one flip. So, there could be up to `n - K` flips.
  2. Since it's a XOR operation, we can do flips in any order.
  3. Say we start do flips left to right. That means that, when we encounter zero, we have no choice but flip.

At this point, this intuition is sound enough to try a greedy approach.

## Naïve Greedy Solution

Go through the array and flip K elements when encounter zero. Return `-1` if
you cannot do K flips.

    
    
    int minKBitFlips(vector<int>& A, int K, int res = 0) {
      for (auto i = 0; i < A.size(); ++i) {
        if (A[i] != 1) {
          if (i + K - 1 >= A.size()) return -1;
          for (auto j = i; j < i + K; ++j) A[j] = !A[j];
          ++res;
        }
      }
      return res;
    }
    

### Complexity Analysis

The time complexity of this solution is _O(n * K)_ , where _n_ is the length
of `A`. This solution is not accepted by the online judge.

**Update:** this solution is accepted by OJ with the ~5,000 ms runtime (vs.  <
100 ms for the solutions below). So, I could have gotten lucky during the
contest!

# Intuition 2

Since we are doing XOR operation, even flips will be equal to zero flips, odd
flips will be equal to one flip. So, instead of modifying K bits every time we
encounter zero, we can just track the current number of flips.

## Linear Solution

Here, we are using a queue to track flips. When we 'flip', we put the end
index of our flip (`i + K - 1`) into our queue. The size of the queue will
indicate number of flips; we also remove past 'flips' from our queue.

    
    
    int minKBitFlips(vector<int>& A, int K, int res = 0) {
      queue<int> flips;
      for (auto i = 0; i < A.size(); ++i) {
        if (A[i] != (flips.size() % 2 ? 0 : 1)) {
          ++res;
          flips.push(i + K - 1);
        }
        if (!flips.empty() && flips.front() <= i) flips.pop();
      }
      return flips.empty() ? res : -1;
    }
    

### Complexity Analysis

The time complexity of this solution is _O(n)_ , and the memory complexity is
_O(K)_.

## Constant Memory Solution

Instead of using the queue, we can track the total number of flips, and use
the source array to mark flips with negative values.

Note that we restore original values after the processing, so the source array
is not changed.

    
    
    int minKBitFlips(vector<int>& A, int K, int res = 0, int flips = 0) {
      for (auto i = 0; i < A.size(); ++i) {
        if (A[i] == flips % 2) {
          if (i > A.size() - K) return -1;
          ++res, ++flips, A[i] -= 2;
        }
        if (i >= K - 1 && A[i - K + 1] < 0) --flips, A[i - K + 1] += 2;
      }
      return res;
    }
    

### Complexity Analysis

The time complexity of this solution is _O(n)_ , and the memory complexity is
_O(1)_.



