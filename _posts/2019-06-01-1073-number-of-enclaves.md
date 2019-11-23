---
layout: post
title: 1073. Number of Enclaves
---
### Question
Given a 2D array `A`, each cell is 0 (representing sea) or 1 (representing
land)

A move consists of walking from one land square 4-directionally to another
land square, or off the boundary of the grid.

Return the number of land squares in the grid for which we **cannot** walk off
the boundary of the grid in any number of moves.



 **Example 1:**

    
    
     **Input:** [[0,0,0,0],[1,0,1,0],[0,1,1,0],[0,0,0,0]]
    **Output:** 3
    **Explanation:**
    There are three 1s that are enclosed by 0s, and one 1 that isn't enclosed because its on the boundary.

**Example 2:**

    
    
    **Input:** [[0,1,1,0],[0,0,1,0],[0,0,1,0],[0,0,0,0]]
    **Output:** 0
    **Explanation:**
    All 1s are either on the boundary or can reach the boundary.
    



 **Note:**

  1. `1 <= A.length <= 500`
  2. `1 <= A[i].length <= 500`
  3. `0 <= A[i][j] <= 1`
  4. All rows have the same size.

### Solution 1
# Intuition

We flood-fill the land (change 1 to 0) from the boundary of the grid. Then, we
count the remaining land.  
![image](https://assets.leetcode.com/users/votrubac/image_1554006685.png)

# DFS Solution

The first cycle does DFS for the boundary cells. The second cycle counts the
remaining land.

    
    
    void dfs(vector<vector<int>>& A, int i, int j) {
      if (i < 0 || j < 0 || i == A.size() || j == A[i].size() || A[i][j] != 1) return;
      A[i][j] = 0;
      dfs(A, i + 1, j), dfs(A, i - 1, j), dfs(A, i, j + 1), dfs(A, i, j - 1);
    }
    int numEnclaves(vector<vector<int>>& A) {
      for (auto i = 0; i < A.size(); ++i)
        for (auto j = 0; j < A[0].size(); ++j) 
          if (i * j == 0 || i == A.size() - 1 || j == A[i].size() - 1) dfs(A, i, j);
    
      return accumulate(begin(A), end(A), 0, [](int s, vector<int> &r)
        { return s + accumulate(begin(r), end(r), 0); });
    }
    

# BFS Solution

Alternativelly, we can use a queue to implement BFS solution. For this
problem, I don't think it's matter whether we do DFS or BFS. The runtime of
the both solution is almost the same as well.

    
    
    int numEnclaves(vector<vector<int>>& A, int res = 0) {
      queue<pair<int, int>> q;
      for (auto i = 0; i < A.size(); ++i)
        for (auto j = 0; j < A[0].size(); ++j) {
          res += A[i][j];
          if (i * j == 0 || i == A.size() - 1 || j == A[i].size() - 1) q.push({ i, j });
        }
      while (!q.empty()) {
        auto x = q.front().first, y = q.front().second; q.pop();
        if (x < 0 || y < 0 || x == A.size() || y == A[x].size() || A[x][y] != 1) continue;
        A[x][y] = 0;
        --res;
        q.push({ x + 1, y }), q.push({ x - 1, y }), q.push({ x, y + 1 }), q.push({ x, y - 1 });
      }
      return res;
    }
    

## Complexity Analysis

Runtime: _O(n * m)_ , where _n_ and _m_ are the dimensions of the grid.  
Memory: _O(n * m)_. DFS can enumerate all elements in the worst case, and we
need to store each element on the stack for the recursion.


### Solution 2
  * We check edges of A matrix.
  * If A[i][j] is 1 on the edge, do DFS and clean all connected 1's
  * Return sum of left 1's

    
    
    class Solution:
        def numEnclaves(self, A: List[List[int]]) -> int:
            def dfs(i, j):
                A[i][j] = 0
                for x, y in (i - 1, j), (i + 1, j), (i, j - 1), (i, j + 1):
                    if 0 <= x < m and 0 <= y < n and A[x][y]:
                        dfs(x, y)
            m, n = len(A), len(A[0])
            for i in range(m):
                for j in range(n):
                    if A[i][j] == 1 and (i == 0 or j == 0 or i == m - 1 or j == n - 1):
                        dfs(i, j)
            return sum(sum(row) for row in A)
    

Time complexity: O(m * n)  
Space complexity: O(m * n) due to recursion


### Solution 3
    
    
    class Solution {
        public int numEnclaves(int[][] A) {
            int result = 0;
            for(int i = 0; i < A.length; i++) {
                for(int j = 0; j < A[i].length; j++) {
                    if(i == 0 || j == 0 || i == A.length - 1 || j == A[i].length - 1)
                        dfs(A, i, j);
                }
            }
            
            for(int i = 0; i < A.length; i++) {
                for(int j = 0; j < A[i].length; j++) {
                    if(A[i][j] == 1)
                        result++;
                }
            }
            
            return result;
        }
        
        public void dfs(int a[][], int i, int j) {
            if(i >= 0 && i <= a.length - 1 && j >= 0 && j <= a[i].length - 1 && a[i][j] == 1) {
                a[i][j] = 0;
                dfs(a, i + 1, j);
                dfs(a, i - 1, j);
                dfs(a, i, j + 1);
                dfs(a, i, j - 1);
            }
        }
    }
    



