---
layout: post
title: 999. Regions Cut By Slashes
---
### Question
In a N x N `grid` composed of 1 x 1 squares, each 1 x 1 square consists of a
`/`, `\`, or blank space.  These characters divide the square into contiguous
regions.

(Note that backslash characters are escaped, so a `\` is represented as
`"\\"`.)

Return the number of regions.



 **Example 1:**

    
    
     **Input:** [
      " /",
      "/ "
    ]
    **Output:** 2
    **Explanation:** The 2x2 grid is as follows:
    ![](https://assets.leetcode.com/uploads/2018/12/15/1.png)
    

**Example 2:**

    
    
    **Input:** [
      " /",
      "  "
    ]
    **Output:** 1
    **Explanation:** The 2x2 grid is as follows:
    ![](https://assets.leetcode.com/uploads/2018/12/15/2.png)
    

**Example 3:**

    
    
    **Input:** [
      "\/",
      "/\"
    ]
    **Output:** 4
    **Explanation:** (Recall that because \ characters are escaped, "\/" refers to \/, and "/\" refers to /\.)
    The 2x2 grid is as follows:
    ![](https://assets.leetcode.com/uploads/2018/12/15/3.png)
    

**Example 4:**

    
    
    **Input:** [
      "/\",
      "\/"
    ]
    **Output:** 5
    **Explanation:** (Recall that because \ characters are escaped, "/\" refers to /\, and "\/" refers to \/.)
    The 2x2 grid is as follows:
    ![](https://assets.leetcode.com/uploads/2018/12/15/4.png)
    

**Example 5:**

    
    
    **Input:** [
      "//",
      "/ "
    ]
    **Output:** 3
    **Explanation:** The 2x2 grid is as follows:
    ![](https://assets.leetcode.com/uploads/2018/12/15/5.png)
    



 **Note:**

  1. `1 <= grid.length == grid[0].length <= 30`
  2. `grid[i][j]` is either `'/'`, `'\'`, or `' '`.

### Solution 1
We can upscale the input grid to [n * 3][n * 3] grid and draw "lines" there.
Then, we can paint empty regions using DFS and count them. Picture below says
it all. Note that [n * 2][n * 2] grid does not work as "lines" are too thick
to identify empty areas correctly.  
![image](https://assets.leetcode.com/users/votrubac/image_1544935075.png)

    
    
    void dfs(vector<vector<int>>& g, int i, int j) {
      if (i >= 0 && j >= 0 && i < g.size() && j < g.size() && g[i][j] == 0) {
        g[i][j] = 1;
        dfs(g, i - 1, j), dfs(g, i + 1, j), dfs(g, i, j - 1), dfs(g, i, j + 1);
      }
    }
    int regionsBySlashes(vector<string>& grid, int regions = 0) {
      vector<vector<int>> g(grid.size() * 3, vector<int>(grid.size() * 3, 0));
      for (auto i = 0; i < grid.size(); ++i)
        for (auto j = 0; j < grid.size(); ++j) {
          if (grid[i][j] == '/') g[i * 3][j * 3 + 2] = g[i * 3 + 1][j * 3 + 1] = g[i * 3 + 2][j * 3] = 1;
          if (grid[i][j] == '\') g[i * 3][j * 3] = g[i * 3 + 1][j * 3 + 1] = g[i * 3 + 2][j * 3 + 2] = 1;
        }
      for (auto i = 0; i < g.size(); ++i)
        for (auto j = 0; j < g.size(); ++j) if (g[i][j] == 0) dfs(g, i, j), ++regions;
      return regions;
    }
    


### Solution 2
Split a cell in to 4 parts like this.  
We give it a number top is 1, right is 2, bottom is 3 left is 4.

![](https://assets.leetcode.com/uploads/2018/12/15/3.png)

Two adjacent parts in different cells are contiguous regions.  
In case `'/'`, top and left are contiguous, botton and right are contiguous.  
In case `'\\'`, top and right are contiguous, bottom and left are contiguous.  
In case `' '`, all 4 parts are contiguous.

Congratulation.  
Now you have another problem of counting the number of islands.

DFS will be good enough to solve it.  
As I did in [947.Most Stones Removed with Same Row or
Column](https://leetcode.com/problems/most-stones-removed-with-same-row-or-
column/discuss/197668)  
I solve it with union find.

Good luck and have fun.

 **Time Complexity** :  
O(N^2)

 **Java:**

    
    
        int count, n;
        int[] f;
        public int regionsBySlashes(String[] grid) {
            n = grid.length;
            f = new int[n * n *  4];
            count = n * n * 4;
            for (int i = 0; i < n * n * 4; ++i)
                f[i] = i;
            for (int i = 0; i < n; ++i) {
                for (int j = 0; j < n; ++j) {
                    if (i > 0) union(g(i - 1, j, 2), g(i, j, 0));
                    if (j > 0) union(g(i , j - 1, 1), g(i , j, 3));
                    if (grid[i].charAt(j) != '/') {
                        union(g(i , j, 0), g(i , j,  1));
                        union(g(i , j, 2), g(i , j,  3));
                    }
                    if (grid[i].charAt(j) != '\') {
                        union(g(i , j, 0), g(i , j,  3));
                        union(g(i , j, 2), g(i , j,  1));
                    }
                }
            }
            return count;
        }
    
        public int find(int x) {
            if (x != f[x]) {
                f[x] = find(f[x]);
            }
            return f[x];
        }
        public void union(int x, int y) {
            x = find(x); y = find(y);
            if (x != y) {
                f[x] = y;
                count--;
            }
        }
        public int g(int i, int j, int k) {
            return (i * n + j) * 4 + k;
        }
    

**C++:**

    
    
    class Solution {
    public:
       int count, n;
       vector<int> f;
       int regionsBySlashes(vector<string>& grid) {
            n = grid. size();
            count = n * n * 4;
            for (int i = 0; i < n * n * 4; ++i)
                f.push_back(i);
            for (int i = 0; i < n; ++i) {
                for (int j = 0; j < n; ++j) {
                    if (i > 0) uni(g(i - 1, j, 2), g(i, j, 0));
                    if (j > 0) uni(g(i , j - 1, 1), g(i , j, 3));
                    if (grid[i][j] != '/') {
                        uni(g(i , j, 0), g(i , j,  1));
                        uni(g(i , j, 2), g(i , j,  3));
                    }
                    if (grid[i][j] != '\') {
                        uni(g(i , j, 0), g(i , j,  3));
                        uni(g(i , j, 2), g(i , j,  1));
                    }
                }
            }
            return count;
        }
    
        int find(int x) {
            if (x != f[x]) {
                f[x] = find(f[x]);
            }
            return f[x];
        }
        void uni(int x, int y) {
            x = find(x); y = find(y);
            if (x != y) {
                f[x] = y;
                count--;
            }
        }
        int g(int i, int j, int k) {
            return (i * n + j) * 4 + k;
        }
    };
    

**Python:**

    
    
         def regionsBySlashes(self, grid):
            f = {}
            def find(x):
                f.setdefault(x, x)
                if x != f[x]:
                    f[x] = find(f[x])
                return f[x]
            def union(x, y):
                f[find(x)] = find(y)
    
            for i in xrange(len(grid)):
                for j in xrange(len(grid)):
                    if i:
                        union((i - 1, j, 2), (i, j, 0))
                    if j:
                        union((i, j - 1, 1), (i, j, 3))
                    if grid[i][j] != "/":
                        union((i, j, 0), (i, j, 1))
                        union((i, j, 2), (i, j, 3))
                    if grid[i][j] != "\":
                        union((i, j, 3), (i, j, 0))
                        union((i, j, 1), (i, j, 2))
            return len(set(map(find, f)))
    


### Solution 3
First mark the boundary by painting [n * 3][m * 3] grid, then use the
algorithm count number of island
([leetcode200](https://leetcode.com/problems/number-of-islands/)) using either
bfs or dfs  
Using a 3X3 array to represent '/' or '\'  
0 0 1  
0 1 0  
1 0 0

1 0 0  
0 1 0  
0 0 1  
Why need to use 3x3 array to represent '/' or '\'? For example ["//","/ "]  
use 1d: can't not distinct individual component  
1 1  
1 0  
use 2d: some connected area was isolated  
0 1 0 1  
1 0 1 0  
0 1 0 0  
1 0 0 0

use 3d:  
0 0 1 0 0 1  
0 1 0 0 1 0  
1 0 0 1 0 0  
0 0 1 0 0 0  
0 1 0 0 0 0  
1 0 0 0 0 0  
use 4d or more: it works, but not necessary

    
    
    class Solution {
        public int regionsBySlashes(String[] grid) {
            int n = grid.length, m = grid[0].length();
            int cnt = 0;
            int[][] g = new int[n*3][m*3];
            for(int i=0;i<n;i++){
                for(int j=0;j<m;j++){
                    if(grid[i].charAt(j)=='/'){
                        g[i * 3][j * 3 + 2] = 1;
                        g[i * 3 + 1][j * 3 + 1] = 1;
                        g[i * 3 + 2][j * 3] = 1;
                    }else if(grid[i].charAt(j)=='\'){
                        g[i * 3][j * 3] = 1;
                        g[i * 3 + 1][j * 3 + 1] = 1;
                        g[i * 3 + 2][j * 3 + 2] = 1;
                    }
                }
            }
            for(int i=0;i<g.length;i++){
                for(int j=0;j<g[0].length;j++){
                    if(g[i][j]==0){
                        dfs(g,i,j);
                        cnt++;
                    }
                }
            }
            return cnt;
        }
        
        void dfs(int[][] g, int i, int j){
            int n = g.length, m = g[0].length;
            if(i<0 || i>=n || j<0 || j>=m || g[i][j]==1) return;
            g[i][j]=1;
            int d[] = {0,-1,0,1,0};
            for(int k=0;k<4;k++){
                dfs(g,i+d[k],j+d[k+1]);
            }
        }
    }
    



