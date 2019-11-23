---
layout: post
title: 1002. Maximum Width Ramp
---
### Question
Given an array `A` of integers, a _ramp_  is a tuple `(i, j)` for which `i <
j` and `A[i] <= A[j]`.  The width of such a ramp is `j - i`.

Find the maximum width of a ramp in `A`.  If one doesn't exist, return 0.



 **Example 1:**

    
    
     **Input:** [6,0,8,2,1,5]
    **Output:** 4
    **Explanation:**
    The maximum width ramp is achieved at (i, j) = (1, 5): A[1] = 0 and A[5] = 5.
    

**Example 2:**

    
    
    **Input:** [9,8,1,0,1,9,4,0,4,1]
    **Output:** 7
    **Explanation:**
    The maximum width ramp is achieved at (i, j) = (2, 9): A[2] = 1 and A[9] = 1.
    



 **Note:**

  1. `2 <= A.length <= 50000`
  2. `0 <= A[i] <= 50000`

### Solution 1
## Solution 1

 **Intuition** :  
Keep a decraesing stack.  
For each number,  
binary search the first smaller number in the stack.

When the number is smaller the the last,  
push it into the stack.

 **Time Complexity** :  
`O(NlogN)`

 **Java**

    
    
         public int maxWidthRamp(int[] A) {
            List<Integer> s = new ArrayList<>();
            int res = 0, n = A.length;
            for (int i = 0; i < n; ++i) {
                if (s.size() == 0 || A[i] < A[s.get(s.size() - 1)]) {
                    s.add(i);
                } else {
                    int left = 0, right = s.size() - 1, mid = 0;
                    while (left < right) {
                        mid = (left + right) / 2;
                        if (A[s.get(mid)] > A[i]) {
                            left = mid + 1;
                        } else {
                            right = mid;
                        }
                    }
                    res = Math.max(res, i - s.get(left));
                }
            }
            return res;
        }
    

**Python**

    
    
        def maxWidthRamp(self, A):
            stack = []
            res =  0
            for i in range(len(A))[::-1]:
                if not stack or A[i] > stack[-1][0]:
                    stack.append([A[i], i])
                else:
                    j = stack[bisect.bisect(stack, [A[i], i])][1]
                    res = max(res, j - i)
            return res
    

  

## Solution 2

Improve the idea above.  
Still one pass and keep a decraesing stack.

**Time Complexity** :  
`O(N)`

**Java**

    
    
        public int maxWidthRamp(int[] A) {
            Stack<Integer> s = new Stack<>();
            int res =  0, n = A.length;
            for (int i = 0; i < n; ++i)
                if (s.empty() || A[s.peek()] > A[i])
                    s.add(i);
            for (int i = n - 1; i > res; --i)
                while (!s.empty() && A[s.peek()] <= A[i])
                    res = Math.max(res, i - s.pop());
            return res;
        }
    

**C++**

    
    
        int maxWidthRamp(vector<int>& A) {
            stack<int> s;
            int res =  0, n = A.size();
            for (int i = 0; i < n; ++i)
                if (s.empty() || A[s.top()] > A[i])
                    s.push(i);
            for (int i = n - 1; i > res; --i)
                while (!s.empty() && A[s.top()] <= A[i])
                    res = max(res, i - s.top()), s.pop();
            return res;
        }
    

**Python:**

    
    
        def maxWidthRamp(self, A):
            s = []
             res = 0
            for i, a in enumerate(A):
                if not s or A[s[-1]] > a:
                    s.append(i)
            for j in range(len(A))[::-1]:
                while s and A[s[-1]] <= A[j]:
                    res = max(res, j - s.pop())
            return res
    


### Solution 2
    
    
    class Solution {
        public int maxWidthRamp(int[] A) {
            int n = A.length;
            int i, j , max = 0;
            int[] maxR = new int[n], minL = new int[n];
            minL[0] = A[0];
            for (i = 1; i < n; i++){
                minL[i] = Math.min(A[i], minL[i - 1]);
            }
            maxR[n - 1] = A[n - 1];
            for (j = n - 2; j >= 0; j--){
                maxR[j] = Math.max(A[j], maxR[j + 1]);
            }
            i = 0; j = 0;
            while (i < n && j < n){
                if (minL[i] <= maxR[j]){
                    max = Math.max(max, j - i);
                    j++;
                }else{
                    i++;
                }
            }
            return max;
        }
    }
    


### Solution 3
  * Initial mx ramp = 0
  * Initial min index (ind) = float("inf")
  * Register every index of every number in "index" collection because there can be duplicate numbers.
  * Sort A
  * For every number in sorted A, get difference between last index of current number (index[num][-1]) and minimum index of previous numbers (ind).

    
    
    class Solution:
        def maxWidthRamp(self, A):
            ind, mx, index = float("inf"), 0, collections.defaultdict(list)
            for i, num in enumerate(A):
                index[num].append(i)
            for num in sorted(A):
                mx = max(mx, index[num][-1] - ind)
                ind = min(ind, index[num][0])
            return mx
    



