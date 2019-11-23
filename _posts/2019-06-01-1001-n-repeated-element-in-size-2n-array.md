---
layout: post
title: 1001. N-Repeated Element in Size 2N Array
---
### Question
In a array `A` of size `2N`, there are `N+1` unique elements, and exactly one
of these elements is repeated N times.

Return the element repeated `N` times.



 **Example 1:**

    
    
     **Input:** [1,2,3,3]
    **Output:** 3
    

**Example 2:**

    
    
    **Input:** [2,1,2,5,3,2]
    **Output:** 2
    

**Example 3:**

    
    
    **Input:** [5,1,5,2,5,3,5,4]
    **Output:** 5
    



 **Note:**

  1. `4 <= A.length <= 10000`
  2. `0 <= A[i] < 10000`
  3. `A.length` is even

### Solution 1
### Solution 1

Use array or set and return seen number at once.  
`O(N)` time, `O(N)` space

 **Java, use array**

    
    
         public int repeatedNTimes(int[] A) {
            int[] count = new int[10000];
            for (int a : A)
                if (count[a]++ == 1)
                    return a;
            return -1;
        }
    

**C++, use set**

    
    
         int repeatedNTimes2(vector<int>& A) {
            unordered_set<int> seen;
            for (int a: A) {
                if (seen.count(a))
                    return a;
                seen.insert(a);
            }
        }
    

  

## Solution 2

Check if `A[i] == A[i - 1]` or `A[i] == A[i - 2]`  
If so, we return `A[i]`  
If not, it must be `[x, x, y, z]` or `[x, y, z, x]`.  
We return `A[0]` for the cases that we miss.  
`O(N)` time `O(1)` space

**C++**

    
    
        int repeatedNTimes(vector<int>& A) {
             for (int i = 2; i < A.size(); ++i)
                if (A[i] == A[i - 1] || A[i] == A[i - 2])
                    return A[i];
            return A[0];
        }
    

**Java**

    
    
        public int repeatedNTimes(int[] A) {
             for (int i = 2; i < A.length; ++i)
                if (A[i] == A[i - 1] || A[i] == A[i - 2])
                    return A[i];
            return A[0];
        }
    

  

## Solution 3

This is a solution for fun, not for interview.  
Instead of compare from left to right,  
we can compare in random order.

Random pick two numbers.  
Return if same.

50% to get the right number.  
Each turn, 25% to get two right numbers.  
Return the result in average 4 turns.  
Time complexity `O(4)`, space `O(1)`

**C++:**

    
    
        int repeatedNTimes(vector<int>& A) {
            int  i = 0, j = 0, n = A.size();
            while (i == j || A[i] != A[j])
                i = rand() % n, j = rand() % n;
            return A[i];
        }
    

**Java:**

    
    
        public  int repeatedNTimes(int[] A) {
            int i = 0, j = 0, n = A.length;
            while (i == j || A[i] != A[j]) {
                i = (int)(Math.random() * n);
                j = (int)(Math.random() * n);
            }
            return A[i];
        }
    

**Python:**

    
    
        def repeatedNTimes(self, A):
             while 1:
                s = random.sample(A, 2)
                if s[0] == s[1]:
                    return s[0]
    


### Solution 2
The intuition here is that the repeated numbers have to appear either next to
each other (`A[i] == A[i + 1]`), or alternated (`A[i] == A[i + 2]`).

The only exception is sequences like `[2, 1, 3, 2]`. In this case, the result
is the last number, so we just return it in the end. This solution has O(n)
runtime.

    
    
    int repeatedNTimes(vector<int>& A) {
      for (auto i = 0; i < A.size() - 2; ++i)
        if (A[i] == A[i + 1] || A[i] == A[i + 2]) return A[i];
      return A[A.size() - 1]; 
    }
    

Another interesting approach is to use randomization (courtesy of [@lee215
](https://leetcode.com/lee215)). If you pick two numbers randomly, there is a
25% chance you bump into the repeated number. So, in average, we will find the
answer in 4 attempts, thus O(4) runtime.

    
    
    int repeatedNTimes(vector<int>& A, int i = 0, int j = 0) {
      while (A[i = rand() % A.size()] != A[j = rand() % A.size()] || i == j);
      return A[i];
    }
    


### Solution 3
If a number is repeated N times in a list of size 2N, it is always possible
for the repeated number to stay within a distance of 2.  
Consider this exaple where N = 4, Number **x** is repeated twice. All possible
comibnations for x to fit in a list of size 4 are:  
[a,b,x,x]  
[x,a,b,x] (distance between both the x is still 1, consider it as a circular
list)  
[x,a,x,b]  
[x,x,a,b]  
[a,x,b,x]  
[a,x,x,b]

    
    
    class Solution:
        def repeatedNTimes(self, A):
            for i in range(len(A)):
                if A[i - 1] == A[i] or A[i - 2] == A[i]:
                    return A[i]
    



