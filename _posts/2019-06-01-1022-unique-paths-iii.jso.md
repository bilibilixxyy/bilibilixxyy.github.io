---
layout: post
title: 1022. Unique Paths III
---
### Question
On a 2-dimensional `grid`, there are 4 types of squares:

  * `1` represents the starting square.  There is exactly one starting square.
  * `2` represents the ending square.  There is exactly one ending square.
  * `0` represents empty squares we can walk over.
  * `-1` represents obstacles that we cannot walk over.

Return the number of 4-directional walks from the starting square to the
ending square, that **walk over every non-obstacle square  exactly once**.



 **Example 1:**

    
    
     **Input:** [[1,0,0,0],[0,0,0,0],[0,0,2,-1]]
    **Output:** 2
    **Explanation:** We have the following two paths: 
    1. (0,0),(0,1),(0,2),(0,3),(1,3),(1,2),(1,1),(1,0),(2,0),(2,1),(2,2)
    2. (0,0),(1,0),(2,0),(2,1),(1,1),(0,1),(0,2),(0,3),(1,3),(1,2),(2,2)

**Example 2:**

    
    
    **Input:** [[1,0,0,0],[0,0,0,0],[0,0,0,2]]
    **Output:** 4
    **Explanation:** We have the following four paths: 
    1. (0,0),(0,1),(0,2),(0,3),(1,3),(1,2),(1,1),(1,0),(2,0),(2,1),(2,2),(2,3)
    2. (0,0),(0,1),(1,1),(1,0),(2,0),(2,1),(2,2),(1,2),(0,2),(0,3),(1,3),(2,3)
    3. (0,0),(1,0),(2,0),(2,1),(2,2),(1,2),(1,1),(0,1),(0,2),(0,3),(1,3),(2,3)
    4. (0,0),(1,0),(2,0),(2,1),(1,1),(0,1),(0,2),(0,3),(1,3),(1,2),(2,2),(2,3)

**Example 3:**

    
    
    **Input:** [[0,1],[2,0]]
    **Output:** 0
    **Explanation:**
    There is no path that walks over every empty square exactly once.
    Note that the starting and ending square can be anywhere in the grid.
    



 **Note:**

  1. `1 <= grid.length * grid[0].length <= 20`

### Solution 1
Video [here](https://www.youtube.com/watch?v=Dny7JhfvJ0o) for this problem in
Java.

First find out where the start and the end is.  
Also We need to know the number of empty cells.

We we try to explore a cell,  
it will change 0 to -2 and do a dfs in 4 direction.

If we hit the target and pass all empty cells, increment the result.

Time complexity is as good as dp, but it take less space and easier to
implement.

 **Java:**

    
    
         int res = 0, empty = 1, sx, sy, ex, ey;
        public int uniquePathsIII(int[][] grid) {
            int m = grid.length, n = grid[0].length;
            for (int i = 0; i < m; ++i) {
                for (int j = 0; j < n; ++j) {
                    if (grid[i][j] == 0) empty++;
                    else if (grid[i][j] == 1) {
                        sx = i;
                        sy = j;
                    } else if (grid[i][j] == 2) {
                        ex = i;
                        ey = j;
                    }
                }
            }
            dfs(grid, sx, sy);
            return res;
        }
    
        public void dfs(int[][] grid, int x0, int y0) {
            if (check(grid, x0, y0) == false) return;
            if (x0 == ex && y0 == ey) {
                if (empty == 0) res++;
                return;
            }
            grid[x0][y0] = -2;
            empty--;
            dfs(grid, x0 + 1, y0);
            dfs(grid, x0 - 1, y0);
            dfs(grid, x0, y0 + 1);
            dfs(grid, x0, y0 - 1);
            grid[x0][y0] = 0;
            empty++;
        }
    
        public boolean check(int[][] grid, int i, int j) {
            int m = grid.length, n = grid[0].length;
            return 0 <= i && i < m && 0 <= j && j < n && grid[i][j] >= 0;
        }
    

**Python:**

    
    
        def uniquePathsIII( self, A):
            self.res = 0
            m, n,empty = len(A), len(A[0]),1
            for i in range(m):
                for j in range(n):
                    if A[i][j] == 1: x,y = (i, j)
                    elif A[i][j] == 2: end = (i, j)
                    elif A[i][j] == 0: empty += 1
            def dfs(x, y, empty):
                if not (0 <= x < m and 0 <= y < n and A[x][y] >= 0): return
                if (x, y) == end:
                    self.res += empty == 0
                    return
                A[x][y] = -2
                dfs(x + 1, y, empty - 1)
                dfs(x - 1, y, empty - 1)
                dfs(x, y + 1, empty - 1)
                dfs(x, y - 1, empty - 1)
                A[x][y] = 0
            dfs(x,y, empty)
            return self.res
    


### Solution 2
First, we find our startig point (`i1, j1`) and count the number of empty
cells. We then do DFS, marking visited elements and tracking number of steps
(`s`). If we reach the target, and number of steps matches the number of empty
cells (`s == t_s`) - we found a path.

    
    
    int dfs(vector<vector<int>>& g, int i, int j, int s, int t_s) {
      if (i < 0 || j < 0 || i >= g.size() || j >= g[0].size() || g[i][j] == -1) return 0;
      if (g[i][j] == 2) return s == t_s ? 1 : 0;
      g[i][j] = -1;
      int paths = dfs(g, i + 1, j, s + 1, t_s) + dfs(g, i - 1, j, s + 1, t_s) +
        dfs(g, i, j + 1, s + 1, t_s) + dfs(g, i, j - 1, s + 1, t_s);
      g[i][j] = 0;
      return paths;
    }
    int uniquePathsIII(vector<vector<int>>& g) {
      auto i1 = 0, j1 = 0, t_steps = 0;
      for (auto i = 0; i < g.size(); ++i)
        for (auto j = 0; j < g[0].size(); ++j) {
          if (g[i][j] == 1) i1 = i, j1 = j;
          if (g[i][j] != -1) ++t_steps;
        }
      return dfs(g, i1, j1, 1, t_steps);
    }
    


### Solution 3
Some notes:

  1. `dfs()`: returns the number of valid paths from current status to the destination.
  2. `cur`: the number of visited squares.
  3. `total`: the number of squares need to be visited in the grid.

Time: N^2 * 2^N, where N is grid.length * grid[0].length  
Space: N * 2^N

    
    
    class Solution {
        public int uniquePathsIII(int[][] grid) {
            int x = -1, y = -1, m = grid.length, n = grid[0].length, total = 0;
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    if (grid[i][j] == 1) {
                        x = i;
                        y = j;
                    }
                    if (grid[i][j] == 0) {
                        total++;
                    }
                }
            }
            return dfs(grid, x, y, 0, total + 1, new HashMap<>());
        }
        private int dfs(int[][] grid, int x, int y, int cur, int total, Map<String, Integer> memo) {
            int m = grid.length, n = grid[0].length;
            if (x < 0 || x >= m || y < 0 || y >= n || grid[x][y] == -1) {
                return 0;
            }
            if (grid[x][y] == 2) {
                if (total == cur) {
                    return 1;
                } else {
                    return 0;
                }
            }
            grid[x][y] = -1;
            String key = getKey(grid) + " " + x + " " + y;
            if (!memo.containsKey(key)) {
                int[][] dirs = new int[][]{{1, 0}, {0, 1}, {0, -1}, {-1, 0}};
                int result = 0;
                for (int[] dir : dirs) {
                    result += dfs(grid, x + dir[0], y + dir[1], cur + 1, total, memo);
                }
                memo.put(key, result);
            }
            grid[x][y] = 0;
            return memo.get(key);
        }
        private int getKey(int[][] grid) {
            int result = 0;
            for (int[] row : grid) {
                for (int a : row) {
                    result <<= 1;
                    if (a != 0) {
                        result ^= 1;
                    }
                }
            }
            return result;
        }
    }
    



