---
layout: post
title: 861. Flipping an Image
---
### Question
Given a binary matrix `A`, we want to flip the image horizontally, then invert
it, and return the resulting image.

To flip an image horizontally means that each row of the image is reversed.
For example, flipping `[1, 1, 0]` horizontally results in `[0, 1, 1]`.

To invert an image means that each `0` is replaced by `1`, and each `1` is
replaced by `0`. For example, inverting `[0, 1, 1]` results in `[1, 0, 0]`.

 **Example 1:**

    
    
     **Input:** [[1,1,0],[1,0,1],[0,0,0]]
    **Output:** [[1,0,0],[0,1,0],[1,1,1]]
    **Explanation:** First reverse each row: [[0,1,1],[1,0,1],[0,0,0]].
    Then, invert the image: [[1,0,0],[0,1,0],[1,1,1]]
    

**Example 2:**

    
    
    **Input:** [[1,1,0,0],[1,0,0,1],[0,1,1,1],[1,0,1,0]]
    **Output:** [[1,1,0,0],[0,1,1,0],[0,0,0,1],[1,0,1,0]]
    **Explanation:** First reverse each row: [[0,0,1,1],[1,0,0,1],[1,1,1,0],[0,1,0,1]].
    Then invert the image: [[1,1,0,0],[0,1,1,0],[0,0,0,1],[1,0,1,0]]
    

**Notes:**

  * `1 <= A.length = A[0].length <= 20`
  * `0 <= A[i][j]  <= 1`

### Solution 1
 **Explanation** :

  1. reverse every row.
  2. toggle every value.

In java, I did both steps together.  
I compared the `i` th and `n - i - 1` th in a row.  
If they are same, we toggle both. Otherwise we do nothing.

 **Time Complexity** :  
O(N^2)

 **C++:**

    
    
         vector<vector<int>> flipAndInvertImage(vector<vector<int>>& A) {
            for (auto & row : A) reverse(row.begin(), row.end());
            for (auto & row : A) for (int & i: row) i ^= 1;
            return A;
        }
    

**Java:**

    
    
         public int[][] flipAndInvertImage(int[][] A) {
            int n = A.length;
            for (int[] row : A)
                for (int i = 0; i * 2 < n; i++)
                    if (row[i] == row[n - i - 1])
                        row[i] = row[n - i - 1] ^= 1;
            return A;
        }
    

**1-line Python:**

    
    
         def flipAndInvertImage(self, A):
            return [[1 ^ i for i in row[::-1]] for row in A]
    


### Solution 2
    
    
    class Solution(object):
        def flipAndInvertImage(self, A):
            """
            :type A: List[List[int]]
            :rtype: List[List[int]]
            """
            
            return [[1-i for i in row[::-1]] for row in A]
    


### Solution 3
The idea is simple. For each row, use two pointers. One is going forward and
the other is going backward.  
(1). If the two elements are the same, then make a slight change like this 0
-> 1 or 1 -> 0.  
(2). If the two elements are different, DON'T do anything. Just let it go.

Only scan the whole matrix once.

    
    
    class Solution {
        public int[][] flipAndInvertImage(int[][] A) {
            for (int i = 0; i < A.length; i++) {
                int lo = 0, hi = A[0].length - 1;
                while (lo <= hi) {
                    if (A[i][lo] == A[i][hi]) {
                        A[i][lo] = 1 - A[i][lo];
                        A[i][hi] = A[i][lo];
                    }
                    lo++;
                    hi--;
                }
            }
            
            return A;
        }
    }
    



