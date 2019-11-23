---
layout: post
title: 417. Pacific Atlantic Water Flow
---
### Question
Given an `m x n` matrix of non-negative integers representing the height of
each unit cell in a continent, the "Pacific ocean" touches the left and top
edges of the matrix and the "Atlantic ocean" touches the right and bottom
edges.

Water can only flow in four directions (up, down, left, or right) from a cell
to another one with height equal or lower.

Find the list of grid coordinates where water can flow to both the Pacific and
Atlantic ocean.

 **Note:**

  1. The order of returned grid coordinates does not matter.
  2. Both _m_ and _n_ are less than 150.



 **Example:**

    
    
    Given the following 5x5 matrix:
    
      Pacific ~   ~   ~   ~   ~ 
           ~  1   2   2   3  (5) *
           ~  3   2   3  (4) (4) *
           ~  2   4  (5)  3   1  *
           ~ (6) (7)  1   4   5  *
           ~ (5)  1   1   2   4  *
              *   *   *   *   * Atlantic
    
    Return:
    
    [[0, 4], [1, 3], [1, 4], [2, 2], [3, 0], [3, 1], [4, 0]] (positions with parentheses in above matrix).
    

### Solution 1
  1. Two Queue and add all the Pacific border to one queue; Atlantic border to another queue.
  2. Keep a visited matrix for each queue. In the end, add the cell visited by two queue to the result.  
BFS: Water flood from ocean to the cell. Since water can only flow from
high/equal cell to low cell, add the neighboor cell with height larger or
equal to current cell to the queue and mark as visited.

    
    
    public class Solution {
        int[][]dir = new int[][]{{1,0},{-1,0},{0,1},{0,-1}};
        public List<int[]> pacificAtlantic(int[][] matrix) {
            List<int[]> res = new LinkedList<>();
            if(matrix == null || matrix.length == 0 || matrix[0].length == 0){
                return res;
            }
            int n = matrix.length, m = matrix[0].length;
            //One visited map for each ocean
            boolean[][] pacific = new boolean[n][m];
            boolean[][] atlantic = new boolean[n][m];
            Queue<int[]> pQueue = new LinkedList<>();
            Queue<int[]> aQueue = new LinkedList<>();
            for(int i=0; i<n; i++){ //Vertical border
                pQueue.offer(new int[]{i, 0});
                aQueue.offer(new int[]{i, m-1});
                pacific[i][0] = true;
                atlantic[i][m-1] = true;
            }
            for(int i=0; i<m; i++){ //Horizontal border
                pQueue.offer(new int[]{0, i});
                aQueue.offer(new int[]{n-1, i});
                pacific[0][i] = true;
                atlantic[n-1][i] = true;
            }
            bfs(matrix, pQueue, pacific);
            bfs(matrix, aQueue, atlantic);
            for(int i=0; i<n; i++){
                for(int j=0; j<m; j++){
                    if(pacific[i][j] && atlantic[i][j])
                        res.add(new int[]{i,j});
                }
            }
            return res;
        }
        public void bfs(int[][]matrix, Queue<int[]> queue, boolean[][]visited){
            int n = matrix.length, m = matrix[0].length;
            while(!queue.isEmpty()){
                int[] cur = queue.poll();
                for(int[] d:dir){
                    int x = cur[0]+d[0];
                    int y = cur[1]+d[1];
                    if(x<0 || x>=n || y<0 || y>=m || visited[x][y] || matrix[x][y] < matrix[cur[0]][cur[1]]){
                        continue;
                    }
                    visited[x][y] = true;
                    queue.offer(new int[]{x, y});
                } 
            }
        }
    }
    

DFS version:

    
    
    public class Solution {
        public List<int[]> pacificAtlantic(int[][] matrix) {
            List<int[]> res = new LinkedList<>();
            if(matrix == null || matrix.length == 0 || matrix[0].length == 0){
                return res;
            }
            int n = matrix.length, m = matrix[0].length;
            boolean[][]pacific = new boolean[n][m];
            boolean[][]atlantic = new boolean[n][m];
            for(int i=0; i<n; i++){
                dfs(matrix, pacific, Integer.MIN_VALUE, i, 0);
                dfs(matrix, atlantic, Integer.MIN_VALUE, i, m-1);
            }
            for(int i=0; i<m; i++){
                dfs(matrix, pacific, Integer.MIN_VALUE, 0, i);
                dfs(matrix, atlantic, Integer.MIN_VALUE, n-1, i);
            }
            for (int i = 0; i < n; i++) 
                for (int j = 0; j < m; j++) 
                    if (pacific[i][j] && atlantic[i][j]) 
                        res.add(new int[] {i, j});
            return res;
        }
        
        int[][]dir = new int[][]{{0,1},{0,-1},{1,0},{-1,0}};
        
        public void dfs(int[][]matrix, boolean[][]visited, int height, int x, int y){
            int n = matrix.length, m = matrix[0].length;
            if(x<0 || x>=n || y<0 || y>=m || visited[x][y] || matrix[x][y] < height)
                return;
            visited[x][y] = true;
            for(int[]d:dir){
                dfs(matrix, visited, matrix[x][y], x+d[0], y+d[1]);
            }
        }
    }
    


### Solution 2
The DFS solution is straightforward. Starting from each point, and dfs its
neighbor if the neighbor is equal or less than itself. And maintain two
boolean matrix for two oceans, indicating an ocean can reach to that point or
not. Finally go through all nodes again and see if it can be both reached by
two oceans. The trick is if a node is already visited, no need to visited
again. Otherwise it will reach the recursion limits.

This question is very similar to <https://leetcode.com/problems/longest-
increasing-path-in-a-matrix/> And here are some common tips for this kind of
question

  1. init a directions var like `self.directions = [(1,0),(-1,0),(0,1),(0,-1)]` so that when you want to explore from a node, you can just do

    
    
    for direction in self.directions:
                x, y = i + direction[0], j + direction[1]
    

  2. this is a what I normally do for a dfs helper method for exploring a matrix

    
    
    def dfs(self, i, j, matrix, visited, m, n):
      if visited: 
        # return or return a value
      for dir in self.directions:
        x, y = i + direction[0], j + direction[1]
            if x < 0 or x >= m or y < 0 or y >= n or matrix[x][y] <= matrix[i][j] (or a condition you want to skip this round):
               continue
            # do something like
            visited[i][j] = True
            # explore the next level like
            self.dfs(x, y, matrix, visited, m, n)
    

Hope it helps

_Solution_

    
    
     class Solution(object):
        def pacificAtlantic(self, matrix):
            """
            :type matrix: List[List[int]]
            :rtype: List[List[int]]
            """
            if not matrix: return []
            self.directions = [(1,0),(-1,0),(0,1),(0,-1)]
            m = len(matrix)
            n = len(matrix[0])
            p_visited = [[False for _ in range(n)] for _ in range(m)]
            
            a_visited = [[False for _ in range(n)] for _ in range(m)]
            result = []
            
            for i in range(m):
                # p_visited[i][0] = True
                # a_visited[i][n-1] = True
                self.dfs(matrix, i, 0, p_visited, m, n)
                self.dfs(matrix, i, n-1, a_visited, m, n)
            for j in range(n):
                # p_visited[0][j] = True
                # a_visited[m-1][j] = True
                self.dfs(matrix, 0, j, p_visited, m, n)
                self.dfs(matrix, m-1, j, a_visited, m, n)
                
            for i in range(m):
                for j in range(n):
                    if p_visited[i][j] and a_visited[i][j]:
                        result.append([i,j])
            return result
                    
                    
        def dfs(self, matrix, i, j, visited, m, n):
            # when dfs called, meaning its caller already verified this point 
            visited[i][j] = True
            for dir in self.directions:
                x, y = i + dir[0], j + dir[1]
                if x < 0 or x >= m or y < 0 or y >= n or visited[x][y] or matrix[x][y] < matrix[i][j]:
                    continue
                self.dfs(matrix, x, y, visited, m, n)
    # 113 / 113 test cases passed.
    # Runtime: 196 ms
    

_Solution for longest increasing path in matrix_

    
    
     class Solution(object):
        def longestIncreasingPath(self, matrix):
            """
            :type matrix: List[List[int]]
            :rtype: int
            """
            if not matrix: return 0
            self.directions = [(1,0),(-1,0),(0,1),(0,-1)]
            m = len(matrix)
            n = len(matrix[0])
            cache = [[-1 for _ in range(n)] for _ in range(m)]
            res = 0
            for i in range(m):
                for j in range(n):
                    cur_len = self.dfs(i, j, matrix, cache, m, n)
                    res = max(res, cur_len)
            return res
            
        def dfs(self, i, j, matrix, cache, m, n):
            if cache[i][j] != -1:
                return cache[i][j]
            res = 1
            for direction in self.directions:
                x, y = i + direction[0], j + direction[1]
                if x < 0 or x >= m or y < 0 or y >= n or matrix[x][y] <= matrix[i][j]:
                    continue
                length = 1 + self.dfs(x, y, matrix, cache, m, n)
                res = max(length, res)
            cache[i][j] = res
            return res
    


### Solution 3
Hi Guys,

Sorry for the noob question. I actually don't understand the problem very
well. Where I am not understanding the problem is that, in the matrix given in
the problem, if the water can flow only in 4 directions, why is it spilling
diagonally to 2nd row 3rd column (5)? How is water going from (5) -> (7)?

Could someone please help me understand the problem?

Thank you!

    
    
    Given the following 5x5 matrix:
    
      Pacific ~   ~   ~   ~   ~ 
           ~  1   2   2   3  (5) *
           ~  3   2   3  (4) (4) *
           ~  2   4  (5)  3   1  *
           ~ (6) (7)  1   4   5  *
           ~ (5)  1   1   2   4  *
              *   *   *   *   * Atlantic
    
    Return:
    
    [[0, 4], [1, 3], [1, 4], [2, 2], [3, 0], [3, 1], [4, 0]] (positions with parentheses in above matrix).```



