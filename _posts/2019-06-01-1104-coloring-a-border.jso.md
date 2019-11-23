---
layout: post
title: 1104. Coloring A Border
---
### Question
Given a 2-dimensional `grid` of integers, each value in the grid represents
the color of the grid square at that location.

Two squares belong to the same _connected component_ if and only if they have
the same color and are next to each other in any of the 4 directions.

The  _border_ of a connected component is  all the squares in the connected
component that are either 4-directionally adjacent to a square not in the
component, or on the boundary of the grid (the first or last row or column).

Given a square at location `(r0, c0)` in the grid and a `color`, color the
border of the connected component of that square with the given `color`, and
return the final `grid`.



 **Example 1:**

    
    
     **Input:** grid = [[1,1],[1,2]], r0 = 0, c0 = 0, color = 3
    **Output:** [[3, 3], [3, 2]]
    

**Example 2:**

    
    
    **Input:** grid = [[1,2,2],[2,3,2]], r0 = 0, c0 = 1, color = 3
    **Output:** [[1, 3, 3], [2, 3, 3]]
    

**Example 3:**

    
    
    **Input:** grid = [[1,1,1],[1,1,1],[1,1,1]], r0 = 1, c0 = 1, color = 2
    **Output:** [[2, 2, 2], [2, 1, 2], [2, 2, 2]]



 **Note:**

  1. `1 <= grid.length <= 50`
  2. `1 <= grid[0].length <= 50`
  3. `1 <= grid[i][j] <= 1000`
  4. `0 <= r0 < grid.length`
  5. `0 <= c0 < grid[0].length`
  6. `1 <= color <= 1000`

### Solution 1
From an initial point, perform DFS and flip the cell color to negative to
track visited cells.

After DFS is complete for the cell, check if this cell is inside. If so, flip
its color back to the positive.

In the end, cells with the negative color are on the border. Change their
color to the target color.  
![image](https://assets.leetcode.com/users/votrubac/image_1556425139.png)

    
    
    void dfs(vector<vector<int>>& g, int r, int c, int cl) {
      if (r < 0 || c < 0 || r >= g.size() || c >= g[r].size() || g[r][c] != cl) return;
      g[r][c] = -cl;
      dfs(g, r - 1, c, cl), dfs(g, r + 1, c, cl), dfs(g, r, c - 1, cl), dfs(g, r, c + 1, cl);
      if (r > 0 && r < g.size() - 1 && c > 0 && c < g[r].size() - 1 && cl == abs(g[r - 1][c]) &&
        cl == abs(g[r + 1][c]) && cl == abs(g[r][c - 1]) && cl == abs(g[r][c + 1]))
        g[r][c] = cl;
    }
    vector<vector<int>> colorBorder(vector<vector<int>>& grid, int r0, int c0, int color) {
      dfs(grid, r0, c0, grid[r0][c0]);
      for (auto i = 0; i < grid.size(); ++i)
        for (auto j = 0; j < grid[i].size(); ++j) grid[i][j] = grid[i][j] < 0 ? color : grid[i][j];
      return grid;
    }
    


### Solution 2
**Python, DFS:**

Suggested by @IvanaGyro, we can color the border inside the DFS.

    
    
        def colorBorder(self, grid, r0, c0, color):
            seen, m, n = set(), len(grid), len(grid[0])
    
            def dfs(x, y):
                if (x, y) in seen: return True
                if not (0 <= x < m and 0 <= y < n and grid[x][y] == grid[r0][c0]):
                    return False
                seen.add((x, y))
                if dfs(x + 1, y) + dfs(x - 1, y) + dfs(x, y + 1) + dfs(x, y - 1) < 4:
                    grid[x][y] = color
                return True
            dfs(r0, c0)
            return grid
    

**Python, BFS:**

    
    
        def colorBorder(self,  grid, r0, c0, color):
            m, n = len(grid), len(grid[0])
            bfs, component, border = [[r0, c0]], set([(r0, c0)]), set()
            for r0, c0 in bfs:
                for i, j in [[0, 1], [1, 0], [-1, 0], [0, -1]]:
                    r, c = r0 + i, c0 + j
                    if 0 <= r < m and 0 <= c < n and grid[r][c] == grid[r0][c0]:
                        if (r, c) not in component:
                            bfs.append([r, c])
                            component.add((r, c))
                    else:
                        border.add((r0, c0))
            for x, y in border: grid[x][y] = color
            return grid
    


### Solution 3
**Method 1** :

 **BFS**

  1. Let m = grid.length, n = grid[0].length, use the number  
from 0 to m * n - 1 to identify the cells to avoid duplicates;  
e.g., grid[x][y]'s cell number is x * n + y;

  2. put the initial cell [r0, c0] into the Queue then poll it out,  
then check if it is on the grid bounday;

  3. Traverse the cell's 4 neighbors:  
a) if its neighbor is of different color, the cell is on the  
component border;  
b) if same color, put the neighbor into Queue;

  4. repeat the above 2 and 3 till Queue is empty.

    
    
        private static final int[] d = { 0, 1, 0, -1, 0 }; // neighbors' relative displacements.
        public int[][] colorBorder(int[][] grid, int r0, int c0, int color) {
            int clr = grid[r0][c0], m = grid.length, n = grid[0].length;
            Set<Integer> seen = new HashSet<>(); // put the cell number into Set to avoid visiting again.
            Queue<int[]> q = new LinkedList<>();
            q.offer(new int[]{ r0, c0 }); // add initial cell.
            seen.add(r0 * n + c0); // add initial cell number.
            while (!q.isEmpty()) { // BFS starts.
                int r = q.peek()[0], c = q.poll()[1];
                if (r == 0 || r == m - 1 || c == 0 || c == n - 1) { grid[r][c] = color; } // on grid boundary.
                for (int i = 0; i < 4; ++i) { // travers its 4 neighbors.
                    int x = r + d[i], y = c + d[i + 1]; // neighbor coordinates.
                    if (x < 0 || x >= m || y < 0 || y >= n || seen.contains(x * n + y)) { continue; } // out of grid or visited already.
                    if (grid[x][y] == clr) { // its neighbor is of same color.
                        seen.add(x * n + y); // avoid visiting again.
                        q.offer(new int[]{ x, y }); // put it into Queue.
                    }else { // its neighbor is of different color, hence it is on component boundary. 
                        grid[r][c] = color;
                    }
                }
            }
            return grid;
        }
    

**Method 2** :

**DFS**

Use DFS to explore the cell (r0, c0)'s component, and negate the visited cell,
traverse its 4 neighbors. After the traversal, change back from the negative
if the component cell is at inner part.

    
    
        private int[] d = { 0, 1, 0, -1, 0 }; // neighbors' relative displacements.
        public int[][] colorBorder(int[][] grid, int r0, int c0, int color) {
            dfs(grid, r0, c0, grid[r0][c0]);
            for (int[] g : grid) {
                for (int i = 0; i < g.length; ++i) {
                    if (g[i] < 0) { g[i] = color; }
                }
            }
            return grid;
        }
        private void dfs(int[][] grid, int r, int c, int clr) {
            grid[r][c] = -clr; // mark as visited.
            int cnt = 0; // use to count grid[r][c]'s component neighbors (same color as itself).
            for (int i = 0; i < 4; ++i) { // traverse 4 neighbors.
                int x = r + d[i], y = c + d[i + 1]; // nieghbor's coordinates.
                if (x < 0 || x >= grid.length || y < 0 || y >= grid[0].length || Math.abs(grid[x][y]) != clr) { continue; } // out of grid or not same component.
                ++cnt; // only if all 4 neighbors of grid[r][c] have same color as itself, it is on inner part.
                if (grid[x][y] == clr) { dfs(grid, x, y, clr); }
            }
            if (cnt == 4) { grid[r][c] = clr; } // inner part, change back.
        }
    



