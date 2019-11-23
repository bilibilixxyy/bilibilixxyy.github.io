---
layout: post
title: 1120. Flower Planting With No Adjacent
---
### Question
You have `N` gardens, labelled `1` to `N`.  In each garden, you want to plant
one of 4 types of flowers.

`paths[i] = [x, y]` describes the existence of a bidirectional path from
garden `x` to garden `y`.

Also, there is no garden that has more than 3 paths coming into or leaving it.

Your task is to choose a flower type for each garden such that, for any two
gardens connected by a path, they have different types of flowers.

Return **any** such a choice as an array `answer`, where `answer[i]` is the
type of flower planted in the `(i+1)`-th garden.  The flower types are denoted
1, 2, 3, or 4.  It is guaranteed an answer exists.



 **Example 1:**

    
    
     **Input:** N = 3, paths = [[1,2],[2,3],[3,1]]
    **Output:** [1,2,3]
    

**Example 2:**

    
    
    **Input:** N = 4, paths = [[1,2],[3,4]]
    **Output:** [1,2,1,2]
    

**Example 3:**

    
    
    **Input:** N = 4, paths = [[1,2],[2,3],[3,4],[4,1],[1,3],[2,4]]
    **Output:** [1,2,3,4]
    



 **Note:**

  * ` 1 <= N <= 10000`
  * `0 <= paths.size <= 20000`
  * No garden has 4 or more paths coming into or leaving it.
  * It is guaranteed an answer exists.

### Solution 1
##  **Intuition**

Greedily paint nodes one by one.  
Because there is no node that has more than 3 neighbors,  
always one possible color to choose.

##  **Complexity**

Time `O(N)` with `O(paths) = O(1.5N)`  
Space `O(N)`

 **Java**

    
    
         public int[] gardenNoAdj(int N, int[][] paths) {
            Map<Integer, Set<Integer>> G = new HashMap<>();
            for (int i = 0; i < N; i++) G.put(i, new HashSet<>());
            for (int[] p : paths) {
                G.get(p[0] - 1).add(p[1] - 1);
                G.get(p[1] - 1).add(p[0] - 1);
            }
            int[] res = new int[N];
            for (int i = 0; i < N; i++) {
                int[] colors = new int[5];
                for (int j : G.get(i))
                    colors[res[j]] = 1;
                for (int c = 4; c > 0; --c)
                    if (colors[c] == 0)
                        res[i] = c;
            }
            return res;
        }
    

**C++**

    
    
        vector< int> gardenNoAdj(int N, vector<vector<int>>& paths) {
            vector<int> res(N);
            vector<vector<int>> G(N);
            for (vector<int>& p : paths) {
                G[p[0] - 1].push_back(p[1] - 1);
                G[p[1] - 1].push_back(p[0] - 1);
            }
            for (int i = 0; i < N; ++i) {
                int colors[5] = {};
                for (int j : G[i])
                    colors[res[j]] = 1;
                for (int c = 4; c > 0; --c)
                    if (!colors[c])
                        res[i] = c;
            }
            return res;
        }
    

**Python:**

    
    
        def gardenNoAdj(self, N, paths):
            res = [ 0] * N
            G = [[] for i in range(N)]
            for x, y in paths:
                G[x - 1].append(y - 1)
                G[y - 1].append(x - 1)
            for i in range(N):
                res[i] = ({1, 2, 3, 4} - {res[j] for j in G[i]}).pop()
            return res
    


### Solution 2
# Intuition

This problem is marked as Easy, so the greedy solution should work.

# Solution

  * Transform paths into the adjacency list
    * Better than adjacency matrix as we have 3 connections max per node.
  * For each garden, track used flowers from connected gardens using a boolean array.
  * Assign any available flower to the garden.

    
    
    vector<int> gardenNoAdj(int N, vector<vector<int>>& paths) {
      vector<int> res(N);
      vector<vector<int>> g(N);
      for (auto p : paths) {
        g[p[0] - 1].push_back(p[1] - 1);
        g[p[1] - 1].push_back(p[0] - 1);
      }
      for (auto i = 0; i < N; ++i) {
        bool used[5] = {};
        for (auto j : g[i]) used[res[j]] = true;
        for (auto fl = 1; fl <= 4; ++fl) {
          if (!used[fl]) res[i] = fl;
        }
      }
      return res;
    }
    


### Solution 3
Basic idea: adjust the color of nodes whenever two nodes have the same color.
Initialy, every node has color 1.

    
    
    public int[] gardenNoAdj(int n, int[][] paths) {
            int[] result = new int[n];
            Arrays.fill(result, 1);
            boolean stop = false;
            do {
                stop = true;
                for(int[] edge: paths) {
                    int u = Math.min(edge[0], edge[1]);
                    int v = Math.max(edge[0], edge[1]);
                    if (result[u - 1] == result[v - 1]) {
                        stop = false;
                        result[v - 1] = result[v - 1] == 4 ? 1 : result[v - 1] + 1;
                    }
                }
            }
            while(!stop);
            return result;
        }	
    



