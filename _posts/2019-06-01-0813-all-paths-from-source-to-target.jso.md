---
layout: post
title: 813. All Paths From Source to Target
---
### Question
Given a directed, acyclic graph of `N` nodes.  Find all possible paths from
node `0` to node `N-1`, and return them in any order.

The graph is given as follows:  the nodes are 0, 1, ..., graph.length - 1.
graph[i] is a list of all nodes j for which the edge (i, j) exists.

    
    
     **Example:**
    **Input:** [[1,2], [3], [3], []] 
    **Output:** [[0,1,3],[0,2,3]] 
    **Explanation:** The graph looks like this:
    0--- >1
    |    |
    v    v
    2--->3
    There are two paths: 0 -> 1 -> 3 and 0 -> 2 -> 3.
    

**Note:**

  * The number of nodes in the graph will be in the range `[2, 15]`.
  * You can print different paths in any order, but you should keep the order of nodes inside one path.

### Solution 1
One dfs solution is to traverse the graph from start node to the end, and keep
track of each node along the path. Each node can be visited many times when it
has multiple indegree.

    
    
    class Solution {
        public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
            List<List<Integer>> res = new ArrayList<>();
            List<Integer> path = new ArrayList<>();
    					
            path.add(0);
            dfsSearch(graph, 0, res, path);
    					
            return res;
        }
    
        private void dfsSearch(int[][] graph, int node, List<List<Integer>> res, List<Integer> path) {
            if (node == graph.length - 1) {
                res.add(new ArrayList<Integer>(path));
                return;
            }
    
            for (int nextNode : graph[node]) {
                path.add(nextNode);
                dfsSearch(graph, nextNode, res, path);
                path.remove(path.size() - 1);
            }
        }
    }
    

Another dfs solution is to use memorization. Each node will be only visited
once since the sub result from this node has already been recorded.
Memorization increses space cost as well as time cost to record existing
paths.

    
    
    class Solution {
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        Map<Integer, List<List<Integer>>> map = new HashMap<>();
    
        return dfsSearch(graph, 0, map);   
    }
    
    private List<List<Integer>> dfsSearch(int[][] graph, int node, Map<Integer, List<List<Integer>>> map) {
        if (map.containsKey(node)) {
            return map.get(node);
        }
    
        List<List<Integer>> res = new ArrayList<>();
        if (node == graph.length - 1) {
            List<Integer> path = new LinkedList<>();
            path.add(node);
            res.add(path);
        } else {
            for (int nextNode : graph[node]) {
                List<List<Integer>> subPaths = dfsSearch(graph, nextNode, map);
                for (List<Integer> path : subPaths) {
                    LinkedList<Integer> newPath = new LinkedList<>(path);
                    newPath.addFirst(node);
                    res.add(newPath);
                }
            }
        }
    
        map.put(node, res);
        return res;
    }
    }
    


### Solution 2
**C++**

    
    
         void dfs(vector<vector<int>>& g, vector<vector<int>>& res, vector<int>& path, int cur) {
            path.push_back(cur);
            if (cur == g.size() - 1)
                res.push_back(path);
            else for (auto it: g[cur])
                dfs(g, res, path, it);
            path.pop_back();
    
        }
    
        vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& g) {
            vector<vector<int>> paths;
            vector<int> path;
            dfs(g, paths, path, 0);
            return paths;
        }
    

**Python**

    
    
         def allPathsSourceTarget(self, graph):
            def dfs(cur, path):
                if cur == len(graph) - 1: res.append(path)
                else:
                    for i in graph[cur]: dfs(i, path + [i])
            res = []
            dfs(0, [0])
            return res
    

**2 line recursive version**

    
    
        def allPathsSourceTarget(self, g, cur= 0):
            if cur == len(g) - 1: return [[len(g) - 1]]
            return [([cur] + path) for i in g[cur] for path in self.allPathsSourceTarget(g, i)]
    


### Solution 3
    
    
    void dfs(vector<vector<int>>& graph, vector<vector<int>>& result, vector<int> path, int src, int dst) {
        path.push_back(src);
        if(src == dst) {
            result.push_back(path);
            return;
        }
    
        for(auto node : graph[src])
            dfs(graph, result, path, node, dst);
    }
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        vector<vector<int>> paths; vector<int> path;
        int nodes = graph.size();
        if(nodes == 0) return paths;
        dfs(graph, paths, path, 0, nodes - 1);
        return paths;
    }



