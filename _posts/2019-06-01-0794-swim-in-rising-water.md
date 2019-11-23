---
layout: post
title: 794. Swim in Rising Water
---
### Question
On an N x N `grid`, each square `grid[i][j]` represents the elevation at that
point `(i,j)`.

Now rain starts to fall. At time `t`, the depth of the water everywhere is
`t`. You can swim from a square to another 4-directionally adjacent square if
and only if the elevation of both squares individually are at most `t`. You
can swim infinite distance in zero time. Of course, you must stay within the
boundaries of the grid during your swim.

You start at the top left square `(0, 0)`. What is the least time until you
can reach the bottom right square `(N-1, N-1)`?

 **Example 1:**

    
    
     **Input:** [[0,2],[1,3]]
    **Output:** 3
    **Explanation:**
    At time 0, you are in grid location (0, 0).
    You cannot go anywhere else because 4-directionally adjacent neighbors have a higher elevation than t = 0.
    
    You cannot reach point (1, 1) until time 3.
    When the depth of water is 3, we can swim anywhere inside the grid.
    

**Example 2:**

    
    
    **Input:** [[0,1,2,3,4],[24,23,22,21,5],[12,13,14,15,16],[11,17,18,19,20],[10,9,8,7,6]]
    **Output:** 16
    **Explanation:**
    **0  1  2  3  4**
    24 23 22 21  **5**
    **12 13 14 15 16**
    **11** 17 18 19 20
    **10  9  8  7  6**
    
    The final route is marked in bold.
    We need to wait until time 16 so that (0, 0) and (4, 4) are connected.
    

**Note:**

  1. `2 <= N <= 50`.
  2. grid[i][j] is a permutation of [0, ..., N*N - 1].

### Solution 1
Python:

    
    
        def swimInWater(self, grid):
            N, pq, seen, res = len(grid), [(grid[0][0], 0, 0)], set([(0, 0)]), 0
            while True:
                T, x, y = heapq.heappop(pq)
                res = max(res, T)
                if x == y == N - 1:
                    return res
                for i, j in [(x + 1, y), (x, y + 1), (x - 1, y), (x, y - 1)]:
                    if 0 <= i < N and 0 <= j < N and (i, j) not in seen:
                        seen.add((i, j))
                        heapq.heappush(pq, (grid[i][j], i, j))
    

C++

    
    
    class Solution {
    public:
    
        struct T {
            int t, x, y;
            T(int a, int b, int c) : t (a), x (b), y (c){}
            bool operator< (const T &d) const {return t > d.t;}
        };
    
        int swimInWater(vector<vector<int>>& grid) {
            int N = grid.size (), res = 0;
            priority_queue<T> pq;
            pq.push(T(grid[0][0], 0, 0));
            vector<vector<int>> seen(N, vector<int>(N, 0));
            seen[0][0] = 1;
            static int dir[4][2] = {{0, 1}, {0, -1}, {1, 0}, { -1, 0}};
    
            while (true) {
                auto p = pq.top ();
                pq.pop ();
                res = max(res, p.t);
                if (p.x == N - 1 && p.y == N - 1) return res;
                for (auto& d : dir) {
                    int i = p.x + d[0], j = p.y + d[1];
                    if (i >= 0 && i < N && j >= 0 && j < N && !seen[i][j]) {
                        seen[i][j] = 1;
                        pq.push (T(grid[i][j], i, j));
                    }
                }
            }
        }
    };
    


### Solution 2
Binary Search + DFS, O(n^2logn), 14ms  
Binary Search range [0, n*n-1] to find the minimum feasible water level. For
each water level, verification using DFS or BFS is O(n^2). DFS is slightly
faster in practice.

    
    
    class Solution {
    public:
        int swimInWater(vector<vector<int>>& grid) {
            int n = grid.size();
            int low = grid[0][0], hi = n*n-1;
            while (low < hi) {
                int mid = low + (hi-low)/2;
                if (valid(grid, mid)) 
                   hi = mid;
                else
                   low = mid+1;
            }
            return low;
        }
    private:
        bool valid(vector<vector<int>>& grid, int waterHeight) {
            int n = grid.size();
            vector<vector<int>> visited(n, vector<int>(n, 0));
            vector<int> dir({-1, 0, 1, 0, -1});
            return dfs(grid, visited, dir, waterHeight, 0, 0, n);
        }
        bool dfs(vector<vector<int>>& grid, vector<vector<int>>& visited, vector<int>& dir, int waterHeight, int row, int col, int n) {
            visited[row][col] = 1;
            for (int i = 0; i < 4; ++i) {
                int r = row + dir[i], c = col + dir[i+1];
                if (r >= 0 && r < n && c >= 0 && c < n && visited[r][c] == 0 && grid[r][c] <= waterHeight) {
                    if (r == n-1 && c == n-1) return true;
                    if (dfs(grid, visited, dir, waterHeight, r, c, n)) return true;
                }
            }
            return false;
        }
    };
    

Dijkstra using Priority Queue, O(n^2logn), 20 ms;  
In every step, find lowest water level to move forward, so using PQ rather
than queue

    
    
    class Solution {
    public:
        int swimInWater(vector<vector<int>>& grid) {
            int n = grid.size(), ans = max(grid[0][0], grid[n-1][n-1]);
            priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> pq;
            vector<vector<int>> visited(n, vector<int>(n, 0));
            visited[0][0] = 1;
            vector<int> dir({-1, 0, 1, 0, -1});
            pq.push({ans, 0, 0});
            while (!pq.empty()) {
                auto cur = pq.top();
                pq.pop();
                ans = max(ans, cur[0]);
                for (int i = 0; i < 4; ++i) {
                    int r = cur[1] + dir[i], c = cur[2] + dir[i+1];
                    if (r >= 0 && r < n && c >= 0 && c < n && visited[r][c] == 0) {
                        if (r == n-1 && c == n-1) return ans;
                        pq.push({grid[r][c], r, c});
                        visited[r][c] = 1;
                    }
                }
            }
            return -1;
        }
    };
    

Dijkstra with BFS optimization, O(n^2logn), 11 ms  
Similar to above solution, but we can use BFS, which is more efficient, to
expand reachable region.

    
    
    class Solution {
    public:
        int swimInWater(vector<vector<int>>& grid) {
            int n = grid.size(), ans = max(grid[0][0], grid[n-1][n-1]);
            priority_queue<vector<int>, vector<vector<int>>, greater<vector<int>>> pq;
            vector<vector<int>> visited(n, vector<int>(n, 0));
            visited[0][0] = 1;
            vector<int> dir({-1, 0, 1, 0, -1});
            pq.push({ans, 0, 0});
            while (!pq.empty()) {
                auto cur = pq.top();
                pq.pop();
                ans = max(ans, cur[0]);
                queue<pair<int, int>> myq;
                myq.push({cur[1], cur[2]});
                while (!myq.empty()) {
                    auto p = myq.front();
                    myq.pop();
                    if (p.first == n-1 && p.second == n-1) return ans;
                    for (int i = 0; i < 4; ++i) {
                        int r = p.first + dir[i], c = p.second + dir[i+1];
                        if (r >= 0 && r < n && c >= 0 && c < n && visited[r][c] == 0) {
                            visited[r][c] = 1;
                            if (grid[r][c] <= ans) 
                               myq.push({r, c});
                            else
                               pq.push({grid[r][c], r, c});
                        }
                    }
                }
            }
            return -1;
        }
    };
    


### Solution 3
DFS:

    
    
    class Solution {
        public int swimInWater(int[][] grid) {
            int time = 0;
            int N = grid.length;
            Set<Integer> visited = new HashSet<>();
            while(!visited.contains(N*N-1)) {
                visited.clear();
                dfs(grid, 0, 0, time, visited);
                time++;
            }
            return time - 1;
        }
        int[][] dirs = {{-1,0},{1,0},{0,1},{0,-1}};
        private void dfs(int[][] grid, int i, int j, int time, Set<Integer> visited) {
            if (i < 0 || i > grid.length - 1 || j < 0 || j > grid[0].length - 1 || grid[i][j] > time || visited.contains(i*grid.length+j)) return;
            visited.add(i*grid.length+j);
            for (int[] dir : dirs) {
                dfs(grid, i+dir[0], j+dir[1], time, visited);
            }
        }
    }
    

Union Find:

    
    
    class Solution {
        class UF {
            int[] id;
            public UF(int N) {
                id = new int[N];
                for (int i = 0; i < N; i++) {
                    id[i] = i;
                }
            }
            public int root(int i) {
                while (i != id[i]) {
                    id[i] = id[id[i]];
                    i = id[i];
                }
                return i;
            }
            public boolean isConnected(int p, int q) {
                return root(p)==root(q);
            }
            public void union(int p, int q) {
                if (isConnected(p, q)) return;
                id[root(p)] = root(q);
            }
        }
        public int swimInWater(int[][] grid) {
            int N = grid.length;
            UF uf = new UF(N*N);
            int time = 0;
            while(!uf.isConnected(0, N*N-1)) {
                for (int i = 0; i < N; i++) {
                    for (int j = 0; j < N; j++) {
                        if (grid[i][j] > time) continue;
                        if (i < N-1 && grid[i+1][j]<=time) uf.union(i*N+j, i*N+j+N);
                        if (j < N-1 && grid[i][j+1]<=time) uf.union(i*N+j, i*N+j+1);
                    }
                }
                time++;
            }
            return time - 1;
        }
    }
    



