---
layout: post
title: 820. Find Eventual Safe States
---
### Question
In a directed graph, we start at some node and every turn, walk along a
directed edge of the graph.  If we reach a node that is terminal (that is, it
has no outgoing directed edges), we stop.

Now, say our starting node is _eventually safe  _if and only if we must
eventually walk to a terminal node.  More specifically, there exists a natural
number `K` so that for any choice of where to walk, we must have stopped at a
terminal node in less than `K` steps.

Which nodes are eventually safe?  Return them as an array in sorted order.

The directed graph has `N` nodes with labels `0, 1, ..., N-1`, where `N` is
the length of `graph`.  The graph is given in the following form: `graph[i]`
is a list of labels `j` such that `(i, j)` is a directed edge of the graph.

    
    
     **Example:**
    **Input:** graph = [[1,2],[2,3],[5],[0],[5],[],[]]
    **Output:** [2,4,5,6]
    Here is a diagram of the above graph.
    
    

![Illustration of graph](https://s3-lc-
upload.s3.amazonaws.com/uploads/2018/03/17/picture1.png)

**Note:**

  * `graph` will have length at most `10000`.
  * The number of edges in the graph will not exceed `32000`.
  * Each `graph[i]` will be a sorted list of different integers, chosen within the range `[0, graph.length - 1]`.

### Solution 1
value of color represents three states:  
0:have not been visited  
1:safe  
2:unsafe  
For DFS,we need to do some optimization.When we travel a path,we mark the node
with 2 which represents having been visited,and when we encounter a node which
results in a cycle,we return false,all node in the path stays 2 and it
represents unsafe.And in the following traveling,whenever we encounter a node
which points to a node marked with 2,we know it will results in a cycle,so we
can stop traveling.On the contrary,when a node is safe,we can mark it with 1
and whenever we encounter a safe node,we know it will not results in a cycle.

    
    
    class Solution {
        public List<Integer> eventualSafeNodes(int[][] graph) {
            List<Integer> res = new ArrayList<>();
            if(graph == null || graph.length == 0)  return res;
            
            int nodeCount = graph.length;
            int[] color = new int[nodeCount];
            
            for(int i = 0;i < nodeCount;i++){
                if(dfs(graph, i, color))    res.add(i);
            }
            
            return res;
        }
        public boolean dfs(int[][] graph, int start, int[] color){
            if(color[start] != 0)   return color[start] == 1;
            
            color[start] = 2;
            for(int newNode : graph[start]){
                if(!dfs(graph, newNode, color))    return false;
            }
            color[start] = 1;
            
            return true;
        }
    }
    


### Solution 2
This is equal to find nodes which doesn't lead to a circle in any path.  
We can solve it by walking along the path reversely.

  1. Find nodes with out degree 0, they are terminal nodes, we remove them from graph and they are added to result
  2. For nodes who are connected terminal nodes, since terminal nodes are removed, we decrease in-nodes' out degree by 1 and if its out degree equals to 0, it become new terminal nodes
  3. Repeat 2 until no terminal nodes can be found.

Here is my 300ms 20-line Python Code:

    
    
    def eventualSafeNodes(self, graph):
        """
        :type graph: List[List[int]]
        :rtype: List[int]
        """
        n = len(graph)
        out_degree = collections.defaultdict(int)
        in_nodes = collections.defaultdict(list) 
        queue = []
        ret = []
        for i in range(n):
            out_degree[i] = len(graph[i])
            if out_degree[i]==0:
                queue.append(i)
            for j in graph[i]:
                in_nodes[j].append(i)  
        while queue:
            term_node = queue.pop(0)
            ret.append(term_node)
            for in_node in in_nodes[term_node]:
                out_degree[in_node] -= 1
                if out_degree[in_node]==0:
                    queue.append(in_node)
        return sorted(ret)
    


### Solution 3
  * Just move along unvisited (-1) nodes and remark them as 0 on the queue while visiting others on the path and finish them as 1. If you meet them again on the queue while visiting (being 0) it means you completed a cycle, in other words it is not safe and return back without adding.

    
    
    class Solution:
        def eventualSafeNodes(self, graph):
            def explore(i):
                visited[i] = 0
                for v in graph[i]:
                    if visited[v] == 0 or (visited[v] == -1 and explore(v)): return True
                visited[i] = 1
                res.append(i)
                return False
            visited, res = [-1] * len(graph), []
            for i in range(len(graph)):
                if visited[i] == -1: explore(i)
            return sorted(res)
    



