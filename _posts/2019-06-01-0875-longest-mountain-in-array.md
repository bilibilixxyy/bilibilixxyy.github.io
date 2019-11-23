---
layout: post
title: 875. Longest Mountain in Array
---
### Question
Let's call any (contiguous) subarray B (of A) a _mountain_ if the following
properties hold:

  * `B.length >= 3`
  * There exists some `0 < i < B.length - 1` such that `B[0] < B[1] < ... B[i-1] < B[i] > B[i+1] > ... > B[B.length - 1]`

(Note that B could be any subarray of A, including the entire array A.)

Given an array `A` of integers, return the length of the longest  _mountain_.  

Return `0` if there is no mountain.

 **Example 1:**

    
    
     **Input:** [2,1,4,7,3,2,5]
    **Output:** 5
    **Explanation:** The largest mountain is [1,4,7,3,2] which has length 5.
    

**Example 2:**

    
    
    **Input:** [2,2,2]
    **Output:** 0
    **Explanation:** There is no mountain.
    

**Note:**

  1. `0 <= A.length <= 10000`
  2. `0 <= A[i] <= 10000`

 **Follow up:**

  * Can you solve it using only one pass?
  * Can you solve it in `O(1)` space?

### Solution 1
 **Intuition** :  
We have already many 2-pass or 3-pass problems, like 821. Shortest Distance to
a Character.  
They have almost the same idea.  
One forward pass and one backward pass.  
Maybe another pass to get the final result, or you can merge it in one
previous pass.

 **Explanation** :  
In this problem, we take one forward pass to count up hill length (to every
point).  
We take another backward pass to count down hill length (from every point).  
Finally a pass to find max(up[i] + down[i] + 1) where up[i] and down[i] should
be positives.

 **Time Complexity** :  
O(N)

 **C++:**

    
    
        int longestMountain(vector<int> A) {
            int N = A. size(), res = 0;
            vector<int> up(N, 0), down(N, 0);
            for (int i = N - 2; i >= 0; --i) if (A[i] > A[i + 1]) down[i] = down[i + 1] + 1;
            for (int i = 0; i < N; ++i) {
                if (i > 0 && A[i] > A[i - 1]) up[i] = up[i - 1] + 1;
                if (up[i] && down[i]) res = max(res, up[i] + down[i] + 1);
            }
            return res;
        }
    

**Java:**

    
    
         public int longestMountain(int[] A) {
            int N = A.length, res = 0;
            int[] up = new int[N], down = new int[N];
            for (int i = N - 2; i >= 0; --i) if (A[i] > A[i + 1]) down[i] = down[i + 1] + 1;
            for (int i = 0; i < N; ++i) {
                if (i > 0 && A[i] > A[i - 1]) up[i] = up[i - 1] + 1;
                if (up[i] > 0 && down[i] > 0) res = Math.max(res, up[i] + down[i] + 1);
            }
            return res;
        }
    

**Python:**

    
    
        def longestMountain(self, A):
            up, down = [ 0] * len(A), [0] * len(A)
            for i in range(1, len(A)):
                if A[i] > A[i - 1]: up[i] = up[i - 1] + 1
            for i in range(len(A) - 1)[::-1]:
                if A[i] > A[i + 1]: down[i] = down[i + 1] + 1
            return max([u + d + 1 for u, d in zip(up, down) if u and d] or [0])
    

**Follow up**

Can you solve this problem with only one pass?  
Can you solve this problem in O(1) space?

In this solution, I count up length and down length.  
Both up and down length are clear to 0 when `A[i - 1] == A[i]` or `down > 0 &&
A[i - 1] < A[i]`.

**C++:**

    
    
         int longestMountain(vector<int> A) {
            int res = 0, up = 0, down = 0;
            for (int i = 1; i < A.size(); ++i) {
                if (down && A[i - 1] < A[i] || A[i - 1] == A[i]) up = down = 0;
                up += A[i - 1] < A[i];
                down += A[i - 1] > A[i];
                if (up && down) res = max(res, up + down + 1);
            }
            return res;
        }
    

**Java:**

    
    
         public int longestMountain(int[] A) {
            int res = 0, up = 0, down = 0;
            for (int i = 1; i < A.length; ++i) {
                if (down > 0 && A[i - 1] < A[i] || A[i - 1] == A[i]) up = down = 0;
                if (A[i - 1] < A[i]) up++;
                if (A[i - 1] > A[i]) down++;
                if (up > 0 && down > 0 && up + down + 1 > res) res = up + down + 1;
            }
            return res;
        }
    

**Python:**

    
    
        def longestMountain(self, A):
             res = up = down = 0
            for i in range(1, len(A)):
                if down and A[i - 1] < A[i] or A[i - 1] == A[i]: up = down = 0
                up += A[i - 1] < A[i]
                down += A[i - 1] > A[i]
                if up and down: res = max(res, up + down + 1)
            return res
    


### Solution 2
    
    
    public int longestMountain(int[] A) {
            int n=A.length;
            if(n<3)return 0;
            
            int left=0;int right;int max=0;
            
            while(left<n-2){
                //skip decending and equal array
                while(left<n-1 && A[left]>=A[left+1]){
                    left++;
                }
                right=left+1;
                //mountain up
                while(right<n-1 && A[right]<A[right+1]){
                    right++;
                }
                //mountain down
                while(right<n-1 && A[right]>A[right+1]){
                    right++;
                    //update the max value
                    max=Math.max(max,right-left+1);
                }
                left=right;
            }
            return max;
        }
    


### Solution 3
    
    
    class Solution {
        public int longestMountain(int[] A) {
            if (A.length < 3) return 0;
            int[] increase = new int[A.length];
            int[] decrease = new int[A.length];
            Arrays.fill(increase, 1);
            Arrays.fill(decrease, 1);
            for (int i = 1; i < A.length; i++) {
                if (A[i] > A[i-1]) increase[i] = increase[i-1]+1;
            }
           
            
            for (int i = A.length-2; i >= 0; i--) {
                if (A[i] > A[i+1]) decrease[i] = decrease[i+1]+1;
            }
           
            int res = Integer.MIN_VALUE;
            for (int i = 1; i < A.length - 1; i++) {
                if (A[i] > A[i-1] && A[i] > A[i+1]) {
                    res = Math.max(res, increase[i-1] + decrease[i+1] + 1);
                }
            }
            return res == Integer.MIN_VALUE ? 0 : res;
        }
    }
    



