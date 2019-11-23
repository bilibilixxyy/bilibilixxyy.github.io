---
layout: post
title: 1049. Minimum Domino Rotations For Equal Row
---
### Question
In a row of dominoes, `A[i]` and `B[i]` represent the top and bottom halves of
the `i`-th domino.  (A domino is a tile with two numbers from 1 to 6 - one on
each half of the tile.)

We may rotate the `i`-th domino, so that `A[i]` and `B[i]` swap values.

Return the minimum number of rotations so that all the values in `A` are the
same, or all the values in `B` are the same.

If it cannot be done, return `-1`.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2019/03/08/domino.png)

    
    
     **Input:** A = [2,1,2,4,2,2], B = [5,2,6,2,3,2]
    **Output:** 2
    **Explanation:**
    The first figure represents the dominoes as given by A and B: before we do any rotations.
    If we rotate the second and fourth dominoes, we can make every value in the top row equal to 2, as indicated by the second figure.
    

**Example 2:**

    
    
    **Input:** A = [3,5,1,2,3], B = [3,6,3,3,4]
    **Output:** -1
    **Explanation:**
    In this case, it is not possible to rotate the dominoes to make one row of values equal.
    



 **Note:**

  1. `1 <= A[i], B[i] <= 6`
  2. `2 <= A.length == B.length <= 20000`

### Solution 1
## Solution 1:

Try all possibilities from 1 to 6.

    
    
        def minDominoRotations(self, A, B):
            for x in range(1, 7):
                if all(x == a or x == b for a, b in zip(A, B)):
                    return min(len(A) - A.count(x), len(B) - B.count(x))
            return -1
    

  

## Solution 2

  1. Try `A[0]`
  2. Try `B[0]`
  3. return -1

One observation is that, if `A[0]` works, no need to check `B[0]`.  
Because if both `A[0]` and `B[0]` exist in all dominoes,  
the result will be the same.

**Java**

    
    
         public int minDominoRotations(int[] A, int[] B) {
            int n = A.length;
            for (int i = 0, a = 0, b = 0; i < n && (A[i] == A[0] || B[i] == A[0]); ++i) {
                if (A[i] != A[0]) a++;
                if (B[i] != A[0]) b++;
                if (i == n - 1) return Math.min(a, b);
            }
            for (int i = 0, a = 0, b = 0; i < n && (A[i] == B[0] || B[i] == B[0]); ++i) {
                if (A[i] != B[0]) a++;
                if (B[i] != B[0]) b++;
                if (i == n - 1) return Math.min(a, b);
            }
            return -1;
        }
    

**C++**

    
    
         int minDominoRotations(vector<int>& A, vector<int>& B) {
            int n = A.size();
            for (int i = 0, a = 0, b = 0; i < n && (A[i] == A[0] || B[i] == A[0]); ++i) {
                if (A[i] != A[0]) a++;
                if (B[i] != A[0]) b++;
                if (i == n - 1) return min(a, b);
            }
            for (int i = 0, a = 0, b = 0; i < n && (A[i] == B[0] || B[i] == B[0]); ++i) {
                if (A[i] != B[0]) a++;
                if (B[i] != B[0]) b++;
                if (i == n - 1) return min(a, b);
            }
            return -1;
        }
    

  

## Solution 3

Find intersection set `s` of `{A[i], B[i]}`  
`s.size = 0`, no possible result.  
`s.size = 1`, one and only one result.  
`s.size = 2`, it means all dominoes are `[a,b]` or `[b,a]`, try either one.  
`s.size > 2`, impossible.

**Java:**

    
    
        public  int minDominoRotations(int[] A, int[] B) {
            HashSet<Integer> s = new HashSet<>(Arrays.asList(1, 2, 3, 4, 5, 6));
            int[] countA = new int[7], countB = new int[7];
            for (int i = 0; i < A.length; ++i) {
                s.retainAll(new HashSet<>(Arrays.asList(A[i], B[i])));
                countA[A[i]]++;
                countB[B[i]]++;
            }
            for (int i : s) return Math.min(A.length - countA[i], B.length - countB[i]);
            return -1;
        }
    

**Python:**

    
    
        def minDominoRotations(self, A, B):
            s = reduce( set.__and__, [set(d) for d in zip(A, B)])
            if not s: return -1
            x = s.pop()
            return min(len(A) - A.count(x), len(B) - B.count(x))
    


### Solution 2
  1. Count the frequency of each number in A and B, respectively;
  2. Count the frequency of A[i] if A[i] == B[i];
  3. If countA[i] + countB[i] - same[i] == A.length, we find a solution; otherwise, return -1;
  4. min(countA[i], countB[i]) - same[i] is the answer.

    
    
    class Solution {
        public int minDominoRotations(int[] A, int[] B) {
            if (A.length != B.length) { return -1; }
            int[] countA = new int[7]; // countA[i] records the occurrence of i in A.
            int[] countB = new int[7]; // countB[i] records the occurrence of i in B.
            int[] same = new int[7]; // same[k] records the occurrence of k, where k == A[i] == B[i].
            for (int i = 0; i < A.length; ++i) {
                ++countA[A[i]];
                ++countB[B[i]];
                if (A[i] == B[i]) { ++same[A[i]]; }
            }
            for (int i = 1; i < 7; ++i) {
                if (countA[i] + countB[i] - same[i] >= A.length) {
                    return Math.min(countA[i], countB[i]) - same[i];
                }
            }
            return -1;
        }
    }
    


### Solution 3
We are as good as our very first domino :) So, we just need to check for A[0]
(`top`) and B[0] (`bot`).

If we find a domino that does not have `top` or `bot`, then we exclude that
number from consideration. We count how many times a number appear in the top
or bottom of each domino and use these numbers to detect the minimum number of
flips.

    
    
    int minDominoRotations(vector<int>& A, vector<int>& B) {
      auto top = A[0], bot = B[0], top1 = 0, bot1 = 0, top2 = 0, bot2 = 0;
      for (auto i = 0; i < A.size(); ++i) {
        if (A[i] != top && B[i] != top) top = 0;
        if (A[i] != bot && B[i] != bot) bot = 0;
        top1 += A[i] == top;
        bot1 += B[i] == top;
        top2 += A[i] == bot;
        bot2 += B[i] == bot;
      }
      return top || bot ? min(A.size() - max(top1, bot1), A.size() - max(top2, bot2)) : -1;
    }
    



