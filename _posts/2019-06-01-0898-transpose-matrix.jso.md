---
layout: post
title: 898. Transpose Matrix
---
### Question
Given a matrix `A`, return the transpose of `A`.

The transpose of a matrix is the matrix flipped over it's main diagonal,
switching the row and column indices of the matrix.



 **Example 1:**

    
    
     **Input:** [[1,2,3],[4,5,6],[7,8,9]]
    **Output:** [[1,4,7],[2,5,8],[3,6,9]]
    

**Example 2:**

    
    
    **Input:** [[1,2,3],[4,5,6]]
    **Output:** [[1,4],[2,5],[3,6]]
    



 **Note:**

  1. ` 1 <= A.length <= 1000`
  2. `1 <= A[0].length <= 1000`

### Solution 1
    
    
     class Solution(object):
        def transpose(self, A):
            return zip(*A)
    


### Solution 2
    
    
    class Solution:
        def transpose(self, A):
            return [[A[i][j] for i in range(len(A))] for j in range(len(A[0]))]
    

**Bonus:**

    
    
     class Solution:
        def transpose(self, A):
            R = len(A)
            C = len(A[0])
            transpose = []
            for c in range(C):
                newRow = []
                for r in range(R):
                    newRow.append(A[r][c])
                transpose.append(newRow)
            return transpose
    


### Solution 3
**C++:**

    
    
         vector<vector<int>> tranpose(vector<vector<int>> A) {
            int M = A.size(), N = A[0].size();
            vector<vector<int>> B(N, vector<int>(M, 0));
            for (int j = 0; j < N; j++)
                for (int i = 0; i < M; i++)
                    B[j][i] = A[i][j];
            return B;
        }
    

**Java:**

    
    
        public int[][] tranpose(int[][] A) {
            int M = A. length, N = A[0].length;
            int[][] B = new int[N][M];
            for (int j = 0; j < N; j++)
                for (int i = 0; i < M; i++)
                    B[j][i] = A[i][j];
            return B;
        }
    

**Python:**

    
    
         def tranpose(self, M):
            return zip(*M)
    



