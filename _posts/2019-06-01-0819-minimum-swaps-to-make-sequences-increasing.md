---
layout: post
title: 819. Minimum Swaps To Make Sequences Increasing
---
### Question
We have two integer sequences `A` and `B` of the same non-zero length.

We are allowed to swap elements `A[i]` and `B[i]`.  Note that both elements
are in the same index position in their respective sequences.

At the end of some number of swaps, `A` and `B` are both strictly increasing.
(A sequence is _strictly increasing_ if and only if `A[0] < A[1] < A[2] < ...
< A[A.length - 1]`.)

Given A and B, return the minimum number of swaps to make both sequences
strictly increasing.  It is guaranteed that the given input always makes it
possible.

    
    
     **Example:**
    **Input:** A = [1,3,5,4], B = [1,2,3,7]
    **Output:** 1
    **Explanation:**
    Swap A[3] and B[3].  Then the sequences are:
    A = [1, 3, 5, 7] and B = [1, 2, 3, 4]
    which are both strictly increasing.
    

**Note:**

  * `A, B` are arrays with the same length, and that length will be in the range `[1, 1000]`.
  * `A[i], B[i]` are integer values in the range `[0, 2000]`.

### Solution 1
`swapRecord` means for the ith element in A and B, the minimum swaps if we
swap `A[i]` and `B[i]`  
`fixRecord` means for the ith element in A and B, the minimum swaps if we
DONOT swap `A[i]` and `B[i]`

    
    
    class Solution {
        public int minSwap(int[] A, int[] B) {
            int swapRecord = 1, fixRecord = 0;
            for (int i = 1; i < A.length; i++) {
                if (A[i - 1] >= B[i] || B[i - 1] >= A[i]) {
    		// In this case, the ith manipulation should be same as the i-1th manipulation
                    // fixRecord = fixRecord;
                    swapRecord++;
                } else if (A[i - 1] >= A[i] || B[i - 1] >= B[i]) {
    		// In this case, the ith manipulation should be the opposite of the i-1th manipulation
                    int temp = swapRecord;
                    swapRecord = fixRecord + 1;
                    fixRecord = temp;
                } else {
                    // Either swap or fix is OK. Let's keep the minimum one
                    int min = Math.min(swapRecord, fixRecord);
                    swapRecord = min + 1;
                    fixRecord = min;
                }
            }
            return Math.min(swapRecord, fixRecord);
        }
    }
    

Let me firstly explain the O(n) space DP solution which uses swapRecord[n] and
fixRecord[n]. It would be more explicit.

One thing should be kept in mind is, the array A and B would always be valid
after you do the swap manipulation or not for each element. Take an example:

    
    
    index               0    1    2    3    4
    A                   1    3    5    4    9
    B                   1    2    3    7    10   
    swapRecord          1    1    2    1    2
    fixRecord           0    0    0    2    1
    

`swapRecord[i]` means for the ith element in A and B, the minimum swaps if we
swap A[i] and B[i]  
`fixRecord[i]` means for the ith element in A and B, the minimum swaps if we
DONOT swap A[i] and B[i]

Obviously, `swapRecord[0] = 1` and `fixRecord[0] = 0`.

For `i = 1`, either swap or fix is OK. So we take the minimum previous result,
`min = min(swapRecord[0], fixRecord[0]) = 0`. `swapRecord[1] = min + 1 = 1`,
`fixRecord[1] = min = 0`  
For `i = 2`, notice that A[1] >= B[2], which means the manipulation of `A[2]
and B[2]` should be same as `A[1] and B[1]`, if A[1] and B[1] swap, A[2] and
B[2] should swap, vice versa. Make sense, right? So `swapRecord[2] =
swapRecord[1] + 1` and `fixRecord[2] = fixRecord[1]`  
For `i = 3`, notice that A[2] >= A[3], which mean the manipulation of `A[3]
and B[3]` and `A[2] and B[2]` should be opposite. In this case, `swapRecord[3]
= fixRecord[2] + 1` and `fixRecord[3] = swapRecord[2]`  
For the last elements, it's similiar as the elements when i = 1. Either swap
or fix is OK. You can try to figure this out. :D

Finally, we get the minimum of last swapRecord and fixRecord. It should be the
result.

Notice that every ith swapRecord and fixRecord is only relevant with the
previous one. So the algorithm should be optimized to an O(1) space version.
Just like the code I write above.


### Solution 2
`swap[n]` means the minimum swaps to make the `A[i]` and `B[i]` sequences
increasing for `0 <= i <= n` in condition that we swap `A[n]` and `B[n]`  
`not_swap[n]` is the same with `A[n]` and `B[n]` not swapped.

C++

    
    
        int minSwap(vector<int>& A, vector<int>& B) {
            int N = A.size();
            int not_swap[1000] = {0};
            int swap[1000] = {1};
            for (int i = 1; i < N; ++i) {
                not_swap[i] = swap[i] = N;
                if (A[i - 1] < A[i] && B[i - 1] < B[i]) {
                    not_swap[i] = not_swap[i - 1];
                    swap[i] = swap[i - 1] + 1;
                }
                if (A[i - 1] < B[i] && B[i - 1] < A[i]) {
                    not_swap[i] = min(not_swap[i], swap[i - 1]);
                    swap[i] = min(swap[i], not_swap[i - 1] + 1);
                }
            }
            return min(swap[N - 1], not_swap[N - 1]);
        }
    

Java

    
    
        public int minSwap(int[] A, int[] B) {
            int N = A.length;
            int[] swap = new int[1000];
            int[] not_swap = new int[1000];
            swap[0] = 1;
            for (int i = 1; i < N; ++i) {
                not_swap[i] = swap[i] = N;
                if (A[i - 1] < A[i] && B[i - 1] < B[i]) {
                    not_swap[i] = not_swap[i - 1];
                    swap[i] = swap[i - 1] + 1;
                }
                if (A[i - 1] < B[i] && B[i - 1] < A[i]) {
                    not_swap[i] = Math.min(not_swap[i], swap[i - 1]);
                    swap[i] = Math.min(swap[i], not_swap[i - 1] + 1);
                }
            }
            return Math.min(swap[N - 1], not_swap[N - 1]);
        }
    
    

Python

    
    
        def minSwap(self, A, B):
            N = len(A)
            not_swap, swap = [N] * N, [N] * N
            not_swap[0], swap[0] = 0, 1
            for i in range(1, N):
                if A[i - 1] < A[i] and B[i - 1] < B[i]:
                    not_swap[i] = not_swap[i - 1]
                    swap[i] = swap[i - 1] + 1
                if A[i - 1] < B[i] and B[i - 1] < A[i]:
                    not_swap[i] = min(not_swap[i], swap[i - 1])
                    swap[i] = min(swap[i], not_swap[i - 1] + 1)
            return min(swap[-1], not_swap[-1])


### Solution 3
This problem can be solved using dynamic programming, at each position, we can
choose to swap or not. Since we want two sorted arrays, at each position,
whether to swap or not depends on the choice at previous position, so we can
form a recursive formula.

    
    
    N = len(A)
    dp = [[maxint]*2 for _ in range(N)]
    

Let initialize a N*2 array dp,

  * dp[i][0] means the least swaps used to make A[:i+1] and B[:i+1] sorted having no swap at i-th position.
  * dp[i][1] means the least swaps used to make A[:i+1] and B[:i+1] sorted having swap at i-th position.

**Picture explanation:**  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/luckypants/image_1521451802.png)

Here is the recursive formula:

There are two cases that can make the two arrays sorted:

    
    
    A . B
    C . D
    

Here our cases are `A<B and C<D` and `A<D and C<B`.

Because the possible combination to be sorted are only `A B` in array1 `C D`
in array2 or `A D` in array1 `C B` in array2, so only the 2 cases, and they
can holds at the same time: for example `A=1, B=2, C=1, D=2`. If both don't
hold, swapping can't make them in order.

For $i in [1, N]$:

If A[i]>A[i-1] and B[i]>B[i-1] (they are in order without swap):  
dp[i][0]=min(dp[i][0], dp[i-1][0]) (no swap at i-1 and no swap at i)  
dp[i][1]=min(dp[i][1], dp[i-1][1]+1) (swap at i-1 so swap at i to make in
order)

If A[i]>B[i-1] and B[i]>A[i-1] (they are in order with a swap):  
dp[i][0]=min(dp[i][0], dp[i-1][1]) (swap at i-1, no need to swap at i)  
dp[i][1]=min(dp[i][1], dp[i-1][0]+1) (no swap at i-1, so swap at i)

The two cases don't conflict with each other, so we choose minimum of them
when both holds.

What we want to return is min(dp[N-1][0], dp[N-1][1]).

At every recursion, we only need the last result, so we can use less space,
from O(N) to O(1), time complexity O(N).

20-Line Python Solution：

    
    
    def minSwap(self, A, B):
        """
        :type A: List[int]
        :type B: List[int]
        :rtype: int
        """
        n = len(A)
        pre = [0, 1]
        for i in range(1, n):
            cur = [sys.maxsize, sys.maxsize]
            if A[i]>A[i-1] and B[i]>B[i-1]:
                cur[0] = min(cur[0], pre[0])
                cur[1] = min(cur[1], pre[1]+1)
            if A[i]>B[i-1] and B[i]>A[i-1]:
                cur[0] = min(cur[0], pre[1])
                cur[1] = min(cur[1], pre[0]+1)
            pre = cur
            return min(pre)
    



