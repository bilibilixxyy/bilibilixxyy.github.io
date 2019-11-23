---
layout: post
title: 854. Making A Large Island
---
### Question
In a 2D grid of `0`s and `1`s, we change at most one `0` to a `1`.

After, what is the size of the largest island? (An island is a 4-directionally
connected group of `1`s).

 **Example 1:**

    
    
     **Input:** [[1, 0], [0, 1]]
    **Output:** 3
    **Explanation:** Change one 0 to 1 and connect two 1s, then we get an island with area = 3.
    

**Example 2:**

    
    
    **Input:** [[1, 1], [1, 0]]
    **Output:** 4
    **Explanation:** Change the 0 to 1 and make the island bigger, only one island with area = 4.

**Example 3:**

    
    
    **Input:** [[1, 1], [1, 1]]
    **Output:** 4
    **Explanation:** Can't change any 0 to 1, only one island with area = 4.



Notes:

  * `1 <= grid.length = grid[0].length <= 50`.
  * `0 <= grid[i][j] <= 1`.

### Solution 1
For each `1` in the grid, we paint all connected `1` with the next available
color (2, 3, and so on). We also remember the size of the island we just
painted with that color.

Then, we analyze all `0` in the grid, and sum sizes of connected islands
(based on the island color). Note that the same island can connect to `0` more
than once. The example below demonstrates this idea (the answer is
highlighted):

![](https://s3-lc-upload.s3.amazonaws.com/users/votrubac/image_1525310120.png)

    
    
    int get(int i, int j, vector<vector<int>>& g) {
      return (i < 0 || j < 0 || i >= g.size() || j >= g[0].size()) ? 0 : g[i][j];
    }
    int paint(int i, int j, int clr, vector<vector<int>>& g)
    {
      if (get(i, j, g) != 1) return 0;
      g[i][j] = clr;
      return 1 + paint(i + 1, j, clr, g) + paint(i - 1, j, clr, g) + paint(i, j + 1, clr, g) + paint(i, j - 1, clr, g);
    }
    int largestIsland(vector<vector<int>>& g, int res = 0) {
      vector<int> sizes = { 0, 0 }; // sentinel values; colors start from 2.
      for (auto i = 0; i < g.size(); ++i)
        for (auto j = 0; j < g[i].size(); ++j)
          if (g[i][j] == 1) sizes.push_back(paint(i, j, sizes.size(), g));
      for (auto i = 0; i < g.size(); ++i)
        for (auto j = 0; j < g[i].size(); ++j)
          if (g[i][j] == 0) {
            unordered_set<int> s = { get(i + 1, j, g), get(i - 1, j, g), get(i, j + 1, g), get(i, j - 1, g) };
            res = max(res, 1 + accumulate(begin(s), end(s), 0, [&](int a, int b) {return a + sizes[b]; }));
          }
      return res == 0 ? g.size() * g[0].size() : res;
    }
    

Just to compare, below is a brute-force solution (accepted) that calculates
the size of an island after flipping `0`. It's shorter, but runtime is above
400 ms. It would probably be a good idea so stick to a simpler solution to
save time during contests.

    
    
    int paint(int i, int j, int c, vector<vector<int>>& g, bool flip = false)
    {
      if (!flip && (i < 0 || j < 0 || i >= g.size() || j >= g[0].size() || g[i][j] == 0 || g[i][j] == c)) return 0;
      g[i][j] = g[i][j] == 0 ? 0 : c;
      return 1 + paint(i + 1, j, c, g) + paint(i - 1, j, c, g) + paint(i, j + 1, c, g) + paint(i, j - 1, c, g);
    }
    int largestIsland(vector<vector<int>>& g, int res = 0) {
      for (auto i = 0; i < g.size(); ++i)
        for (auto j = 0; j < g[i].size(); ++j)
          if (g[i][j] == 0) {
            res = max(res, paint(i, j, 2, g, true));
            paint(i, j, 1, g, true);
          }
      return res == 0 ? g.size() * g[0].size() : res;
    }
    


### Solution 2
**README**  
The solution is long, but in fact it is really straight forward.  
I suggest **not** going into my codes but reading my explanations, which
should be enough.

 **Prepare helpful subfunction** :  
I have several simple sub function to help me on this kind of problem.

  1. valid(int x, int y), check if (x, y) is valid in the grid.
  2. move(int x, int y), return all possible next position in 4 directions.

 **Explanation, Only 2 steps**

  1. Explore every island using DFS, count its area, give it an island index and save the result to a {index: area} map.
  2. Loop every cell == 0, check its connected islands and calculate total islands area.

 **C++:**

    
    
     class Solution {
    public:
        int N;
        int largestIsland(vector<vector<int>>& grid) {
            N = grid.size();
            //DFS every island and give it an index of island
            int index = 2, res = 0;
            unordered_map <int, int>area;
            for (int x = 0; x < N; ++x) {
                for (int y = 0; y < N; ++y) {
                    if (grid[x][y] == 1) {
                        area[index] = dfs(grid, x, y, index);
                        res = max(res, area[index++]);
                    }
                }
            }
            //traverse every 0 cell and count biggest island it can conntect
            for (int x = 0; x < N; ++x) for (int y = 0; y < N; ++y) if (grid[x][y] == 0) {
                unordered_set<int> seen = {};
                int cur = 1;
                for (auto p : move(x, y)) {
                    index = grid[p.first][p.second];
                    if (index > 1 && seen.count(index) == 0) {
                        seen.insert(index);
                        cur += area[index];
                    }
                }
                res = max(res, cur);
            }
            return res;
        }
    
        vector<pair<int, int>> move(int x, int y) {
            vector<pair<int, int>> res;
            for (auto p : vector<vector<int>> {{1, 0}, {-1, 0}, {0, 1}, {0, -1}}) {
                if (valid(x + p[0], y + p[1]))
                    res.push_back(make_pair(x + p[0], y + p[1]));
            }
            return res;
        }
    
        int valid(int x, int y) {
            return 0 <= x && x < N && 0 <= y && y < N;
        }
    
        int dfs(vector<vector<int>>& grid, int x, int y, int index) {
            int area = 0;
            grid[x][y] = index;
            for (auto p : move(x, y))
                if (grid[p.first][p.second] == 1)
                    area += dfs(grid, p.first, p.second, index);
            return area + 1;
        }
    };
    

**Java:**

    
    
     import javafx.util.Pair;
    class Solution {
        public int N = 0;
        public int largestIsland(int[][] grid) {
            N = grid.length;
            //DFS every island and give it an index of island
            int index = 3, res = 0;
            HashMap<Integer, Integer> area = new HashMap<>();
            for (int x = 0; x < N; ++x) for (int y = 0; y < N; ++y)
                if (grid[x][y] == 1) {
                    area.put(index, dfs(grid, x, y, index));
                    res = Math.max(res, area.get(index++));
                }
    
            //traverse every 0 cell and count biggest island it can conntect
            for (int x = 0; x < N; ++x) for (int y = 0; y < N; ++y)
                if (grid[x][y] == 0) {
                    HashSet<Integer> seen = new HashSet<>();
                    int cur = 1;
                    for (Pair<Integer, Integer> p : move(x, y)) {
                        index = grid[p.getKey()][p.getValue()];
                        if (index > 1 && !seen.contains(index)) {
                            seen.add(index);
                            cur += area.get(index);
                        }
                    }
                    res = Math.max(res, cur);
                }
            return res;
        }
    
        public List <Pair<Integer, Integer>> move(int x, int y) {
            ArrayList <Pair<Integer, Integer>> res = new ArrayList<>();
            if (valid(x, y + 1)) res.add(new Pair<Integer, Integer>(x, y + 1));
            if (valid(x, y - 1)) res.add(new Pair<Integer, Integer>(x, y - 1));
            if (valid(x + 1, y)) res.add(new Pair<Integer, Integer>(x + 1, y));
            if (valid(x - 1, y)) res.add(new Pair<Integer, Integer>(x - 1, y));
            return res;
        }
    
        public boolean valid(int x, int y) {
            return 0 <= x && x < N && 0 <= y && y < N;
        }
    
        public int dfs(int[][] grid, int x, int y, int index) {
            int area = 0;
            grid[x][y] = index;
            for (Pair<Integer, Integer> p : move(x, y))
                if (grid[p.getKey()][p.getValue()] == 1)
                    area += dfs(grid, p.getKey(), p.getValue(), index);
            return area + 1;
        }
    }
    
    
    

**Python:**

    
    
        def largestIsland(self, grid):
            N = len(grid)
            def move(x, y):
                 for i, j in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                    if 0 <= x + i < N and 0 <= y + j < N:
                        yield x + i, y + j
    
            def dfs(x, y, index):
                area = 0
                grid[x][y] = index
                for i, j in move(x, y):
                    if grid[i][j] == 1:
                        area += dfs(i, j, index)
                return area + 1
    
            # DFS every island and give it an index of island
            index = 2
            area = {}
            for x in range(N):
                for y in range(N):
                    if grid[x][y] == 1:
                        area[index] = dfs(x, y, index)
                        index += 1
    
            # traverse every 0 cell and count biggest island it can conntect
            res = max(area.values() or [0])
            for x in range(N):
                for y in range(N):
                    if grid[x][y] == 0:
                        possible = set(grid[i][j] for i, j in move(x, y) if grid[i][j] > 1)
                        res = max(res, sum(area[index] for index in possible) + 1)
            return res
    
    


### Solution 3
My idea is similar to the problem: number of islands

    
    
    class Solution {
        public int largestIsland(int[][] grid) {
            int max = 0, m = grid.length, n = grid[0].length;
            boolean hasZero = false; //To check if there is any zero in the grid
            for(int i = 0; i < grid.length; i++){
                for(int j = 0;j<grid[0].length;j++){
                    if(grid[i][j] == 0){
                        grid[i][j] = 1;
                        max = Math.max(max,dfs(i,j,grid,new boolean[m][n]));   
                        if(max == m*n) return max;
                        grid[i][j] = 0;
                        hasZero = true;
                    }
                }
            }
            return hasZero?max:m*n;
        }
        private int dfs(int i, int j, int[][] grid,boolean[][] visited){
            if(i < 0 || j < 0 || i >= grid.length || j >= grid[0].length || grid[i][j] == 0||visited[i][j]) return 0;
            visited[i][j] = true;
            int result = 1+dfs(i-1,j,grid,visited)+dfs(i+1,j,grid,visited)+dfs(i,j+1,grid,visited)+dfs(i,j-1,grid,visited);
            return result;
        }
    }
    



