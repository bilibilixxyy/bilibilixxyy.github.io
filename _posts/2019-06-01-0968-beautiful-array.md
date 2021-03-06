---
layout: post
title: 968. Beautiful Array
---
### Question
For some fixed `N`, an array `A` is _beautiful_ if it is a permutation of the
integers `1, 2, ..., N`, such that:

For every `i < j`, there is **no**  `k` with `i < k < j` such that `A[k] * 2 =
A[i] + A[j]`.

Given `N`, return **any** beautiful array `A`.  (It is guaranteed that one
exists.)



 **Example 1:**

    
    
     **Input:** 4
    **Output:** [2,1,4,3]
    

**Example 2:**

    
    
    **Input:** 5
    **Output:** [3,1,2,5,4]



 **Note:**

  * `1 <= N <= 1000`

### Solution 1
#  **Intuition** :

Try to divide and conquer,  
so we have left part, right part.

One way is to divide into [1, N / 2] and [N / 2 + 1, N].  
But it will cause problems when we merge them.

Another way is to divide into odds part and evens part.  
So there is no `k` with `A[k] * 2 = odd + even`

I brute force all permutations when N = 5:  
20 beautiful array found,  
only 4 don't fit odd + even pattern:  
`[2, 1, 4, 5, 3]`  
`[3, 1, 2, 5, 4]`  
`[3, 5, 4, 1, 2]`  
`[4, 5, 2, 1, 3]`  
  

#  **Beautiful Array Properties**

Saying that an array is beautiful,  
there is no `i < k < j`,  
such that `A[k] * 2 = A[i] + A[j]`

Apply these 3 following changes a beautiful array,  
we can get a new beautiful array  
  

 **1\. Deletion**  
Easy to prove.

 **2\. Addition**  
If we have `A[k] * 2 != A[i] + A[j]`,  
`(A[k] + x) * 2 = A[k] * 2 + 2x != A[i] + A[j] + 2x = (A[i] + x) + (A[j] + x)`

E.g: `[1,3,2] + 1 = [2,4,3]`.

 **3\. Multiplication**  
If we have `A[k] * 2 != A[i] + A[j]`,  
for any `x != 0`,  
`(A[k] * x) * 2 = A[k] * 2 * x != (A[i] + A[j]) * x = (A[i] * x) + (A[j] * x)`

E.g: `[1,3,2] * 2 = [2,6,4]`  
  

#  **Explanation**

With the observations above, we can easily construct any beautiful array.  
Assume we have a beautiful array `A` with length `N`

`A1 = A * 2 - 1` is beautiful with only odds from `1` to `N * 2 -1`  
`A2 = A * 2` is beautiful with only even from `2` to `N * 2`  
`B = A1 + A2` beautiful array with length `N * 2`

E.g:

    
    
    A = [2, 1, 4, 5, 3]
    A1 = [3, 1, 7, 9, 5]
    A2 = [4, 2, 8, 10, 6]
    B = A1 + A2 = [3, 1, 7, 9, 5, 4, 2, 8, 10, 6]
    

  

# **Time Complexity** :

I have iteration version here `O(N)`  
Naive recursion is `O(NlogN)`  
Recursion with one call or with cache is `O(N)`  
  

# **Solution** :

**C++:**

    
    
        vector< int> beautifulArray(int N) {
            vector<int> res = {1};
            while (res.size() < N) {
                vector<int> tmp;
                for (int i : res) if (i * 2 - 1 <= N) tmp.push_back(i * 2 - 1);
                for (int i : res) if (i * 2 <= N) tmp.push_back(i * 2);
                res = tmp;
            }
            return res;
        }
    

**Java:**

    
    
         public int[] beautifulArray(int N) {
            ArrayList<Integer> res = new ArrayList<>();
            res.add(1);
            while (res.size() < N) {
                ArrayList<Integer> tmp = new ArrayList<>();
                for (int i : res) if (i * 2 - 1 <= N) tmp.add(i * 2 - 1);
                for (int i : res) if (i * 2 <= N) tmp.add(i * 2);
                res = tmp;
            }
            return res.stream().mapToInt(i -> i).toArray();
        }
    

**Python:**

    
    
        def beautifulArray(self, N):
            res = [ 1]
            while len(res) < N:
                res = [i * 2 - 1 for i in res] + [i * 2 for i in res]
            return [i for i in res if i <= N]
    

  

## **More** :

Some other ideas: <https://leetcode.com/problems/beautiful-
array/discuss/186680>  
Author: lee215


### Solution 2
If you agree, click the "up" bottom.


### Solution 3
Sort by reversed binary

    
    
        def beautifulArray(self, N):
            return sorted(range(1, N + 1), key=lambda x: bin(x)[:1:-1])
    

Naive recursion

    
    
        def beautifulArray(self, N):
            return [i * 2 for i in self.beautifulArray(N / 2)] + [i * 2 - 1 for i in self.beautifulArray((N + 1) / 2)] if N > 1 else [1]
    

Binary Reverse

    
    
        def beautifulArray(self, N):
            return [i for i in [int('{:010b}'.format(i)[::-1], 2) for i in range(1, 1 << 10)] if i <= N]
    



