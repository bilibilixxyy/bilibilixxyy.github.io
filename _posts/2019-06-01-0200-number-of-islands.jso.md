---
layout: post
title: 200. Number of Islands
---
### Question
Given a 2d grid map of `'1'`s (land) and `'0'`s (water), count the number of
islands. An island is surrounded by water and is formed by connecting adjacent
lands horizontally or vertically. You may assume all four edges of the grid
are all surrounded by water.

 **Example 1:**

    
    
     **Input:**
    11110
    11010
    11000
    00000
    
    **Output:**  1
    

**Example 2:**

    
    
    **Input:**
    11000
    11000
    00100
    00011
    
    **Output:** 3
    

### Solution 1
    
    
    public  class Solution {
    
    private int n;
    private int m;
    
    public int numIslands(char[][] grid) {
        int count = 0;
        n = grid.length;
        if (n == 0) return 0;
        m = grid[0].length;
        for (int i = 0; i < n; i++){
            for (int j = 0; j < m; j++)
                if (grid[i][j] == '1') {
                    DFSMarking(grid, i, j);
                    ++count;
                }
        }    
        return count;
    }
    
    private void DFSMarking(char[][] grid, int i, int j) {
        if (i < 0 || j < 0 || i >= n || j >= m || grid[i][j] != '1') return;
        grid[i][j] = '0';
        DFSMarking(grid, i + 1, j);
        DFSMarking(grid, i - 1, j);
        DFSMarking(grid, i, j + 1);
        DFSMarking(grid, i, j - 1);
    }
    

}


### Solution 2
Sink and count the islands.

* * *

 **Python Solution**

    
    
    def numIslands(self, grid):
        def sink( i, j):
            if 0 <= i < len(grid) and 0 <= j < len(grid[i]) and grid[i][j] == '1':
                grid[i][j] = '0'
                map(sink, (i+1, i-1, i, i), (j, j, j+1, j-1))
                return 1
            return 0
        return sum(sink(i, j) for i in range(len(grid)) for j in range(len(grid[i])))
    

* * *

**Java Solution 1**

    
    
    public class Solution {
        char[][] g;
        public int numIslands(char[][] grid) {
            int islands =  0;
            g = grid;
            for (int i=0; i<g.length; i++)
                for (int j=0; j<g[i].length; j++)
                    islands += sink(i, j);
            return islands;
        }
        int sink(int i, int j) {
            if (i < 0 || i == g.length || j < 0 || j == g[i].length || g[i][j] == '0')
                return 0;
            g[i][j] = '0';
            sink(i+1, j); sink(i-1, j); sink(i, j+1); sink(i, j-1);
            return 1;
        }
    }
    

* * *

**Java Solution 2**

    
    
     public class Solution {
        public int numIslands(char[][] grid) {
            int islands = 0;
            for (int i=0; i<grid.length; i++)
                for (int j=0; j<grid[i].length; j++)
                    islands += sink(grid, i, j);
            return islands;
        }
        int sink(char[][] grid, int i, int j) {
            if (i < 0 || i == grid.length || j < 0 || j == grid[i].length || grid[i][j] == '0')
                return 0;
            grid[i][j] = '0';
            for (int k=0; k<4; k++)
                sink(grid, i+d[k], j+d[k+1]);
            return 1;
        }
        int[] d = {0, 1, 0, -1, 0};
    }


### Solution 3
Iterate through each of the cell and if it is an island, do dfs to mark all
adjacent islands, then increase the counter by 1.

    
    
    def numIslands(self, grid):
        if not grid:
            return 0
            
        count = 0
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if grid[i][j] == '1':
                    self.dfs(grid, i, j)
                    count += 1
        return count
    
    def dfs(self, grid, i, j):
        if i<0 or j<0 or i>=len(grid) or j>=len(grid[0]) or grid[i][j] != '1':
            return
        grid[i][j] = '#'
        self.dfs(grid, i+1, j)
        self.dfs(grid, i-1, j)
        self.dfs(grid, i, j+1)
        self.dfs(grid, i, j-1)



