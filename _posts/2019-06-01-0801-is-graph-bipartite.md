---
layout: post
title: 801. Is Graph Bipartite?
---
### Question
Given an undirected `graph`, return `true` if and only if it is bipartite.

Recall that a graph is _bipartite_ if we can split it's set of nodes into two
independent  subsets A and B such that every edge in the graph has one node in
A and another node in B.

The graph is given in the following form: `graph[i]` is a list of indexes `j`
for which the edge between nodes `i` and `j` exists.  Each node is an integer
between `0` and `graph.length - 1`.  There are no self edges or parallel
edges: `graph[i]` does not contain `i`, and it doesn't contain any element
twice.

    
    
     **Example 1:**
    **Input:** [[1,3], [0,2], [1,3], [0,2]]
    **Output:** true
    **Explanation:** 
    The graph looks like this:
    0----1
    |    |
    |    |
    3----2
    We can divide the vertices into two groups: {0, 2} and {1, 3}.
    
    
    
    **Example 2:**
    **Input:** [[1,2,3], [0,2], [0,1,3], [0,2]]
    **Output:** false
    **Explanation:** 
    The graph looks like this:
    0----1
    | \  |
    |  \ |
    3----2
    We cannot find a way to divide the set of nodes into two independent subsets.
    



 **Note:**

  * `graph` will have length in range `[1, 100]`.
  * `graph[i]` will contain integers in range `[0, graph.length - 1]`.
  * `graph[i]` will not contain `i` or duplicate values.
  * The graph is undirected: if any element `j` is in `graph[i]`, then `i` will be in `graph[j]`.

### Solution 1
`Our goal` is trying to use two colors to color the graph and see if there are
any adjacent nodes having the same color.  
Initialize a color[] array for each node. Here are three states for `colors[]`
array:  
`0: Haven't been colored yet.`  
`1: Blue.`  
`-1: Red.`  
For each node,

  1. If it hasn't been colored, use a color to color it. Then use the other color to color all its adjacent nodes (DFS).
  2. If it has been colored, check if the current color is the same as the color that is going to be used to color it. (Please forgive my english... Hope you can understand it.)

`DFS Solution:`

    
    
    class Solution {
        public boolean isBipartite(int[][] graph) {
            int n = graph.length;
            int[] colors = new int[n];			
    				
            for (int i = 0; i < n; i++) {              //This graph might be a disconnected graph. So check each unvisited node.
                if (colors[i] == 0 && !validColor(graph, colors, 1, i)) {
                    return false;
                }
            }
            return true;
        }
        
        public boolean validColor(int[][] graph, int[] colors, int color, int node) {
            if (colors[node] != 0) {
                return colors[node] == color;
            }       
            colors[node] = color;       
            for (int next : graph[node]) {
                if (!validColor(graph, colors, -color, next)) {
                    return false;
                }
            }
            return true;
        }
    }
    

`BFS Solution:`

    
    
    class Solution {
        public boolean isBipartite(int[][] graph) {
            int len = graph.length;
            int[] colors = new int[len];
            
            for (int i = 0; i < len; i++) {
                if (colors[i] != 0) continue;
                Queue<Integer> queue = new LinkedList<>();
                queue.offer(i);
                colors[i] = 1;   // Blue: 1; Red: -1.
                
                while (!queue.isEmpty()) {
                    int cur = queue.poll();
                    for (int next : graph[cur]) {
                        if (colors[next] == 0) {          // If this node hasn't been colored;
                            colors[next] = -colors[cur];  // Color it with a different color;
                            queue.offer(next);
                        } else if (colors[next] != -colors[cur]) {   // If it is colored and its color is different, return false;
                            return false;
                        }
                    }
                }
            }
            
            return true;
        }
    }
    


### Solution 2
just want to provide another type of solution

we need to check each if each cluster(edges linked together) is Bipartite.

    
    
    class Solution {
        public boolean isBipartite(int[][] graph) {
            //BFS
            // 0(not meet), 1(black), 2(white)
            int[] visited = new int[graph.length];
            
            for (int i = 0; i < graph.length; i++) {
                if (graph[i].length != 0 && visited[i] == 0) {
                    visited[i] = 1;
                    Queue<Integer> q = new LinkedList<>();
                    q.offer(i);
                    while(! q.isEmpty()) {
                        int current = q.poll();
                        for (int c: graph[current]) {
    
                                if (visited[c] == 0) {
                                    visited[c] = (visited[current] == 1) ? 2 : 1;
                                    q.offer(c);
                                } else {
                                    if (visited[c] == visited[current]) return false;
                                }
                        }
                    }                        
                    
                }
            }
            
            return true;
        }
    }
    


### Solution 3
    
    
        def isBipartite(self, graph):
            color = {}
            def dfs(pos):
                for i in graph[pos]:
                    if i in color:
                        if color[i] == color[pos]:
                            return False
                    else:
                        color[i] = 1 - color[pos]
                        if not dfs(i):
                            return False
                return True
            for i in range(len(graph)):
                if i not in color:
                    color[i] = 0
                    if not dfs(i):
                        return False
            return True
    



