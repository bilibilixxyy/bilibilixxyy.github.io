---
layout: post
title: 922. Possible Bipartition
---
### Question
Given a set of `N` people (numbered `1, 2, ..., N`), we would like to split
everyone into two groups of **any** size.

Each person may dislike some other people, and they should not go into the
same group.

Formally, if `dislikes[i] = [a, b]`, it means it is not allowed to put the
people numbered `a` and `b` into the same group.

Return `true` if and only if it is possible to split everyone into two groups
in this way.



 **Example 1:**

    
    
     **Input:** N = 4, dislikes = [[1,2],[1,3],[2,4]]
    **Output:** true
    **Explanation** : group1 [1,4], group2 [2,3]
    

**Example 2:**

    
    
    **Input:** N = 3, dislikes = [[1,2],[1,3],[2,3]]
    **Output:** false
    

**Example 3:**

    
    
    **Input:** N = 5, dislikes = [[1,2],[2,3],[3,4],[4,5],[1,5]]
    **Output:** false
    



 **Note:**

  1. `1 <= N <= 2000`
  2. `0 <= dislikes.length <= 10000`
  3. `1 <= dislikes[i][j] <= N`
  4. `dislikes[i][0] < dislikes[i][1]`
  5. There does not exist `i != j` for which `dislikes[i] == dislikes[j]`.

### Solution 1
Notes:  
`group[i] = 0` means node `i` hasn't been visited.  
`group[i] = 1` means node `i` has been grouped to `1`.  
`group[i] = -1` means node `i` has been grouped to `-1`.

    
    
    class Solution {
        public boolean possibleBipartition(int N, int[][] dislikes) {
            int[][] graph = new int[N][N];
            for (int[] d : dislikes) {
                graph[d[0] - 1][d[1] - 1] = 1;
                graph[d[1] - 1][d[0] - 1] = 1;
            }
            int[] group = new int[N];
            for (int i = 0; i < N; i++) {
                if (group[i] == 0 && !dfs(graph, group, i, 1)) {
                    return false;
                }
            }
            return true;
        }
        private boolean dfs(int[][] graph, int[] group, int index, int g) {
            group[index] = g;
            for (int i = 0; i < graph.length; i++) {
                if (graph[index][i] == 1) {
                    if (group[i] == g) {
                        return false;
                    }
                    if (group[i] == 0 && !dfs(graph, group, i, -g)) {
                        return false;
                    }
                }
            }
            return true;
        }
    }
    


### Solution 2
    
    
    class Solution {
        public boolean possibleBipartition(int N, int[][] dislikes) {
            int[] color = new int[N + 1];
            List<List<Integer>> adj = new ArrayList<>(N + 1);
            for(int i = 0; i <= N; i++) adj.add(new ArrayList<Integer>());
            for(int[] d : dislikes) {
                adj.get(d[0]).add(d[1]);
                adj.get(d[1]).add(d[0]);
            }
            
            for(int i = 1; i <= N; i++) {
                if(color[i] == 0) {
                    color[i] = 1;
                    Queue<Integer> q = new LinkedList<>();
                    q.add(i);
                    while(!q.isEmpty()) {
                        int cur = q.poll();
                        for(int nb : adj.get(cur)) {
                            if(color[nb] == 0) {
                                color[nb] = color[cur] == 1 ? 2 : 1;
                                q.add(nb);
                            } else {
                                if(color[nb] == color[cur]) return false;
                            }
                        }
                    }
                }
            }
            return true;
        }
    }


### Solution 3
It can be shown that an undirected graph is bipartite if and only if it
contains no odd cycles. The detailed proof can be found at:
<https://proofwiki.org/wiki/Graph_is_Bipartite_iff_No_Odd_Cycles>. Then we can
use DFS to detect the existence of odd cycles.

    
    
    # Time complexity: O(|V| + |E|)
    # Space complxity: O(|V|)
    # |V| = N, |E| = len(dislikes).
    
    class Solution:
        def possibleBipartition(self, N, dislikes):
            """
            :type N: int
            :type dislikes: List[List[int]]
            :rtype: bool
            """
            # Create an undirected graph where vertices are "N" people and edges are
            # the dislikes between people.
            neighbor_list = [[] for _ in range(N)]
            for dislike in dislikes:
                # The vertex index starts from "0".
                neighbor_list[dislike[0] - 1].append(dislike[1] - 1)
                neighbor_list[dislike[1] - 1].append(dislike[0] - 1)
            
            # People can be partitioned into two groups if and only if the graph is
            # bipartite where:
            # (1) vertices can be partitioned into two groups;
            # (2) no edges are within each group and all edges connect two nodes in
            # different groups.
            # It can be shown that a graph is bipartite if and only if it contains no
            # odd cycles. The detailed proof can be found at:
            # https://proofwiki.org/wiki/Graph_is_Bipartite_iff_No_Odd_Cycles
            def isOddCyclic(curr, parent, path, path_len, visited):
                """Detects if the undirected graph has an odd cycle."""
                visited[curr] = True
                # path = the dict from the vertex to its index in the "path".
                path[curr] = path_len
                
                for neighbor in neighbor_list[curr]:
                    if not visited[neighbor]:
                        # Recursively check if the neighbor has an odd cycle.
                        if isOddCyclic(neighbor, curr, path, path_len + 1, visited):
                            return True
                    elif neighbor != parent:
                        # If we see a vertex other than the parent, we have found a cycle.      
                        if neighbor in path and (path_len - path[neighbor]) % 2 == 0:
                            return True
                        
                path.pop(curr)
                return False
                
            path = {}
            visited = [False] * N
            for i in range(N):
                if not visited[i] and isOddCyclic(i, -1, path, 0, visited):
                    return False
                
            return True
    



