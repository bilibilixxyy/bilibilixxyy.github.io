---
layout: post
title: 971. Shortest Bridge
---
### Question
In a given 2D binary array `A`, there are two islands.  (An island is a
4-directionally connected group of `1`s not connected to any other 1s.)

Now, we may change `0`s to `1`s so as to connect the two islands together to
form 1 island.

Return the smallest number of `0`s that must be flipped.  (It is guaranteed
that the answer is at least 1.)



 **Example 1:**

    
    
     **Input:** [[0,1],[1,0]]
    **Output:** 1
    

**Example 2:**

    
    
    **Input:** [[0,1,0],[0,0,0],[0,0,1]]
    **Output:** 2
    

**Example 3:**

    
    
    **Input:** [[1,1,1,1,1],[1,0,0,0,1],[1,0,1,0,1],[1,0,0,0,1],[1,1,1,1,1]]
    **Output:** 1



 **Note:**

  1. `1 <= A.length = A[0].length <= 100`
  2. `A[i][j] == 0` or `A[i][j] == 1`

### Solution 1
We first paint one of the islands using DFS with color 2, so we can easily
identify island #1 and island #2. Thanks
[@davidluoyes](https://leetcode.com/davidluoyes/) for pointing out that we
only need to paint one island.

Then we start expanding island #2 by paining connected empty area. Each round,
we increase the color (3, 4, and so on) so we can keep track of the newly
painted area. This ends when we "bump" into the first island.  
![image](https://assets.leetcode.com/users/votrubac/image_1541488072.png)  
The fact that we are increasing the color is also useful for the backtracking,
if we need to return the coordinates of the bridge.

    
    
    int paint(vector<vector<int>>& A, int i, int j) {
        if (i < 0 || j < 0 || i == A.size() || j == A.size() || A[i][j] != 1) return 0;
        A[i][j] = 2;
        return 1 + paint(A, i + 1, j) + paint(A, i - 1, j) + paint(A, i, j + 1) + paint(A, i, j - 1);
    }
    bool expand(vector<vector<int>>& A, int i, int j, int cl) {
        if (i < 0 || j < 0 || i == A.size() || j == A.size()) return false;
        if (A[i][j] == 0) A[i][j] = cl + 1;
        return A[i][j] == 1;
    }  
    int shortestBridge(vector<vector<int>>& A) {
        for (int i = 0, found = 0; !found && i < A.size(); ++i)
            for (int j = 0; !found && j < A[0].size(); ++j) found = paint(A, i, j);
        
        for (int cl = 2; ; ++cl)
            for (int i = 0; i < A.size(); ++i)
                for (int j = 0; j < A.size(); ++j) 
                    if (A[i][j] == cl && ((expand(A, i - 1, j, cl) || expand(A, i, j - 1, cl) || 
                        expand(A, i + 1, j, cl) || expand(A, i, j + 1, cl))))
                            return cl - 2;
    }
    

As a bonus, below is the union-find based solution. We convert the map into
the graph, and join connected "land" cells into sub-graphs (island #1 and
island #2). Then, we expand both of these islands by adding directly connected
"water" cells to the sub-graphs. Finally, we stop when we detect that two sub-
graphs are about to merge into one.

Interestingly, the runtime of these two very different solutions are similar
(24 - 35 ms), and the UF solution is much more complex. Probably, the UF
solution can be further optimized by only processing direct connections...

    
    
    int uf_find(int i, vector<int>& nodes) {
      if (nodes[i] <= 0) return i;
      else return nodes[i] = uf_find(nodes[i], nodes);
    }
    int uf_union(int i, int j, vector<int>& nodes) {
      auto pi = uf_find(i, nodes), pj = uf_find(j, nodes);
      if (pi == pj) return 0;
      if (nodes[pi] > nodes[pj]) swap(pi, pj);
      nodes[pi] += min(-1, nodes[pj]);
      nodes[pj] = pi;
      return -nodes[pi];
    }
    int shortestBridge(vector<vector<int>> &A) {
      int sz = A.size();
      vector<int> nodes(sz * sz + 1);
      list<pair<int, int>> edges;
      for (auto i = 0; i < sz; ++i)
        for (auto j = 0; j < sz; ++j) {
          auto idx = i * sz + j + 1;
          if (A[i][j]) nodes[idx] = -1;
          if (j > 0) {
            if (A[i][j] && A[i][j - 1]) uf_union(idx - 1, idx, nodes);
            else edges.push_back({ idx - 1, idx });
          }
          if (i > 0) {
            if (A[i][j] && A[i - 1][j]) uf_union(idx - sz, idx, nodes);
            else edges.push_back({ idx - sz, idx });
          }
        }
    
      for (auto step = 1; ; ++step) {
        vector<pair<int, int>> merge_list;
        for (auto it = edges.begin(); it != edges.end(); ) {
          if (nodes[it->first] == 0 && nodes[it->second] == 0) ++it;
          else {
            if (nodes[it->first] != 0 && nodes[it->second] != 0) {
              if (uf_find(it->first, nodes) != uf_find(it->second, nodes)) return (step - 1) * 2;
            }
            merge_list.push_back({ it->first, it->second });
            edges.erase(it++);
          }
        }
        for (auto p : merge_list) {
          if (nodes[p.first] != 0 && nodes[p.second] != 0) {
            if (uf_find(p.first, nodes) != uf_find(p.second, nodes)) return step * 2 - 1;
          }
          uf_union(p.first, p.second, nodes);
        }
      }
    }
    


### Solution 2
    
    
    class Solution {
        public int shortestBridge(int[][] A) {
            int m = A.length, n = A[0].length;
            boolean[][] visited = new boolean[m][n];
            int[][] dirs = new int[][]{{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
            Queue<int[]> q = new LinkedList<>();
            boolean found = false;
            // 1. dfs to find an island, mark it in `visited`
            for (int i = 0; i < m; i++) {
                if (found) {
                    break;
                }
                for (int j = 0; j < n; j++) {
                    if (A[i][j] == 1) {
                        dfs(A, visited, q, i, j, dirs);
                        found = true;
                        break;
                    }
                }
            }
            // 2. bfs to expand this island
            int step = 0;
            while (!q.isEmpty()) {
                int size = q.size();
                while (size-- > 0) {
                    int[] cur = q.poll();
                    for (int[] dir : dirs) {
                        int i = cur[0] + dir[0];
                        int j = cur[1] + dir[1];
                        if (i >= 0 && j >= 0 && i < m && j < n && !visited[i][j]) {
                            if (A[i][j] == 1) {
                                return step;
                            }
                            q.offer(new int[]{i, j});
                            visited[i][j] = true;
                        }
                    }
                }
                step++;
            }
            return -1;
        }
        private void dfs(int[][] A, boolean[][] visited, Queue<int[]> q, int i, int j, int[][] dirs) {
            if (i < 0 || j < 0 || i >= A.length || j >= A[0].length || visited[i][j] || A[i][j] == 0) {
                return;
            }
            visited[i][j] = true;
            q.offer(new int[]{i, j});
            for (int[] dir : dirs) {
                dfs(A, visited, q, i + dir[0], j + dir[1], dirs);
            }
        }
    }
    


### Solution 3
  * Idea is straightforward.
  * We get root of first island from "first" function
  * We dfs root and add indexes to bfs
  * We bfs and expand the first island in other words
  * Finally return step number when facing other island
  * Note: This can also be done with referenced array if you don't want to modify A.

    
    
    class Solution:
        def shortestBridge(self, A):
            def dfs(i, j):
                A[i][j] = -1
                bfs.append((i, j))
                for x, y in ((i - 1, j), (i + 1, j), (i, j - 1), (i, j + 1)):
                    if 0 <= x < n and 0 <= y < n and A[x][y] == 1:
                        dfs(x, y)
            def first():
                for i in range(n):
                    for j in range(n):
                        if A[i][j]:
                            return i, j
            n, step, bfs = len(A), 0, []
            dfs(*first())
            while bfs:
                new = []
                for i, j in bfs:
                    for x, y in ((i - 1, j), (i + 1, j), (i, j - 1), (i, j + 1)):
                        if 0 <= x < n and 0 <= y < n:
                            if A[x][y] == 1:
                                return step
                            elif not A[x][y]:
                                A[x][y] = -1
                                new.append((x, y))
                step += 1
                bfs = new
    



