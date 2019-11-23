---
layout: post
title: 1171. Shortest Path in Binary Matrix
---
### Question
In an N by N square grid, each cell is either empty (0) or blocked (1).

A  _clear  path from top-left to bottom-right_ has length `k` if and only if
it is composed of cells `C_1, C_2, ..., C_k` such that:

  * Adjacent cells `C_i` and `C_{i+1}` are connected 8-directionally (ie., they are different and share an edge or corner)
  * `C_1` is at location `(0, 0)` (ie. has value `grid[0][0]`)
  * `C_k` is at location `(N-1, N-1)` (ie. has value `grid[N-1][N-1]`)
  * If `C_i` is located at `(r, c)`, then `grid[r][c]` is empty (ie. `grid[r][c] == 0`).

Return the length of the shortest such clear path from top-left to bottom-
right.  If such a path does not exist, return -1.



 **Example 1:**

    
    
     **Input:** [[0,1],[1,0]]
    **Output:** 2
    

**Example 2:**

    
    
    **Input:** [[0,0,0],[1,1,0],[1,1,0]]
    **Output:** 4



 **Note:**

  1. `1 <= grid.length == grid[0].length <= 100`
  2. `grid[r][c]` is `0` or `1`

### Solution 1
BFS gives us the shortest path, so we will just do that.

    
    
    int shortestPathBinaryMatrix(vector<vector<int>>& g, int steps = 0) {
      queue<pair<int, int>> q;
      q.push({ 0, 0 });
      while (!q.empty()) {
        ++steps;
        queue<pair<int, int>> q1;
        while (!q.empty()) {
          auto c = q.front();
          q.pop();
          if (c.first >= 0 && c.second >= 0 && c.first < g.size() && c.second < g.size() && !g[c.first][c.second]) {
            g[c.first][c.second] = 1;
            if (c.first == g.size() - 1 && c.second == g.size() - 1) return steps;
            for (auto i = -1; i < 2; ++i)
              for (auto j = -1; j < 2; ++j)
                if (i != 0 || j != 0) q1.push({ c.first + i, c.second + j });
          }
        }
        swap(q, q1);
      }
      return -1;
    }
    

We can also check the coordinates before adding them to the queue; it can save
some memory and runtinme:

    
    
    int shortestPathBinaryMatrix(vector<vector<int>>& g, int steps = 0) {
      queue<pair<int, int>> q;
      q.push({ 0, 0 });
      while (!q.empty()) {
        ++steps;
        queue<pair<int, int>> q1;
        while (!q.empty()) {
          auto c = q.front();
          q.pop();
          if (exchange(g[c.first][c.second], 1) == 1) continue;
          if (c.first == g.size() - 1 && c.second == g.size() - 1) return steps;
          for (auto i = c.first - 1; i <= c.first + 1; ++i)
            for (auto j = c.second - 1; j <= c.second + 1; ++j)
              if (i != c.first || j != c.second) {
                if (i >= 0 && j >= 0 && i < g.size() && j < g.size() && !g[i][j]) {
                  q1.push({ i, j });
                }
              }
        }
        swap(q, q1);
      }
      return -1;
    }
    


### Solution 2
Since it's BFS, we can securely set the visited grid as non-empty to avoid
revisiting.

    
    
    def shortestPathBinaryMatrix(grid):
    	n = len(grid)
    	if grid[0][0] or grid[n-1][n-1]:
    		return -1
    	q = [(0, 0, 1)]
    	for i, j, d in q:
    		if i == n-1 and j == n-1: return d
    		for x, y in ((i-1,j-1),(i-1,j),(i-1,j+1),(i,j-1),(i,j+1),(i+1,j-1),(i+1,j),(i+1,j+1)):
    			if 0 <= x < n and 0 <= y < n and not grid[x][y]:
    				grid[x][y] = 1
    				q.append((x, y, d+1))
    	return -1
    


### Solution 3
    
    
    private int dir[][] = new int[][]{{0,1},{0,-1},{1,0},{-1,0},{1,-1},{-1,1},{-1,-1},{1,1}};
    
        public int shortestPathBinaryMatrix(int[][] grid) {
    
            int m = grid.length;
            int n = grid[0].length;
    
            if(grid[0][0]==1 || grid[m-1][n-1]==1) {
                return -1;
            }
    
            boolean[][] visited = new boolean[m][n];
            visited[0][0] = true;
            Queue<int[]> queue = new LinkedList<>();
            queue.add(new int[]{0,0});
    
            int ans=0;
            while (!queue.isEmpty()) {
                int size = queue.size();
                for(int i=0;i<size;i++) {
                    int[] pop = queue.remove();
                    if(pop[0]==m-1 && pop[1]==n-1) {
                        return ans+1;
                    }
                    for (int k=0;k<8;k++) {
                        int nextX = dir[k][0]+pop[0];
                        int nextY = dir[k][1]+pop[1];
    
                        if(nextX>=0 && nextX<m && nextY>=0 && nextY<n && !visited[nextX][nextY] && grid[nextX][nextY]==0) {
                            queue.add(new int[]{nextX,nextY});
                            visited[nextX][nextY]=true;
                        }
    
                    }
                }
                ans++;
            }
    
            return -1;
        }
    



