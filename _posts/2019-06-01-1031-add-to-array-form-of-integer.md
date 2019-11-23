---
layout: post
title: 1031. Add to Array-Form of Integer
---
### Question
For a non-negative integer `X`, the  _array-form of`X`_  is an array of its
digits in left to right order.  For example, if `X = 1231`, then the array
form is `[1,2,3,1]`.

Given the array-form `A` of a non-negative integer `X`, return the array-form
of the integer `X+K`.



 **Example 1:**

    
    
     **Input:** A = [1,2,0,0], K = 34
    **Output:** [1,2,3,4]
    **Explanation:** 1200 + 34 = 1234
    

**Example 2:**

    
    
    **Input:** A = [2,7,4], K = 181
    **Output:** [4,5,5]
    **Explanation:** 274 + 181 = 455
    

**Example 3:**

    
    
    **Input:** A = [2,1,5], K = 806
    **Output:** [1,0,2,1]
    **Explanation:** 215 + 806 = 1021
    

**Example 4:**

    
    
    **Input:** A = [9,9,9,9,9,9,9,9,9,9], K = 1
    **Output:** [1,0,0,0,0,0,0,0,0,0,0]
    **Explanation:** 9999999999 + 1 = 10000000000
    



 **Note：**

  1. `1 <= A.length <= 10000`
  2. `0 <= A[i] <= 9`
  3. `0 <= K <= 10000`
  4. If `A.length > 1`, then `A[0] != 0`

### Solution 1
 **Explanation**  
Take `K` as a carry.  
Add it to the lowest digit,  
Update carry `K`,  
and Keep going to higher digit.

 **Time Complexity**

Insert will take `O(1)` time or `O(N)` time on shifting, depending on the data
stucture.  
But in this problem `K` is at most 5 digit so this is restricted.  
So this part doesn't matter.

The overall time complexity is `O(N)`.

 **Java**

    
    
         public List<Integer> addToArrayForm(int[] A, int K) {
            List<Integer> res = new LinkedList<>();
            for (int i = A.length - 1; i >= 0; --i) {
                res.add(0, (A[i] + K) % 10);
                K = (A[i] + K) / 10;
            }
            while (K > 0) {
                res.add(0, K % 10);
                K /= 10;
            }
            return res;
        }
    

**Java**  
With one loop, suggested by @LearningMind.

    
    
         public List<Integer> addToArrayForm(int[] A, int K) {
            List res = new ArrayList<>();
            for (int i = A.length - 1; i >= 0 || K > 0; --i) {
                res.add(0, (i >= 0 ? A[i] + K : K) % 10);
                K = (i >= 0 ? A[i] + K : K) / 10;
            }
            return res;
        }
    

**C++:**

    
    
        vector< int> addToArrayForm(vector<int> A, int K) {
            for (int i = A.size() - 1; i >= 0 && K > 0; --i) {
                A[i] += K;
                K = A[i] / 10;
                A[i] %= 10;
            }
            while (K > 0) {
                A.insert(A.begin(), K % 10);
                K /= 10;
            }
            return A;
        }
    

**Python:**

    
    
         def addToArrayForm(self, A, K):
            for i in range(len(A))[::-1]:
                A[i], K = (A[i] + K) % 10, (A[i] + K) / 10
            return [int(i) for i in str(K)] + A if K else A
    


### Solution 2
<https://www.youtube.com/watch?v=50jJcFYaskQ>


### Solution 3
# **Note:**

I read several other java solutions, and found `ArrayList.add(0, K % 10)` was
used, and it is not `O(1)` but `O(n)` instead.

`LinkedList.add(0, i)` or `offerFirst(i)` is `O(1)`.

Correct me if I am wrong.

    
    
        public List<Integer> addToArrayForm(int[] A, int K) {
            LinkedList<Integer> ans = new LinkedList<>();
            for (int i = A.length - 1; K > 0 || i >= 0; --i, K /= 10) { // loop through A and K, from right to left.
                if (i >= 0) { K += A[i]; } // Use K as carry over, and add A[i].
                ans.offerFirst(K % 10); // add the least significant digit of K.
            }
            return ans;
        }
    

**Analysis:**

 **Time & space: O(n + logK)**, where n = A.length.



