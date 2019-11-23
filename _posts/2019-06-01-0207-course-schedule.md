---
layout: post
title: 207. Course Schedule
---
### Question
There are a total of _n_ courses you have to take, labeled from `0` to `n-1`.

Some courses may have prerequisites, for example to take course 0 you have to
first take course 1, which is expressed as a pair: `[0,1]`

Given the total number of courses and a list of prerequisite **pairs** , is it
possible for you to finish all courses?

 **Example 1:**

    
    
     **Input:** 2, [[1,0]] 
    **Output:** true
    **Explanation:**  There are a total of 2 courses to take. 
                 To take course 1 you should have finished course 0. So it is possible.

**Example 2:**

    
    
    **Input:** 2, [[1,0],[0,1]]
    **Output:** false
    **Explanation:**  There are a total of 2 courses to take. 
                 To take course 1 you should have finished course 0, and to take course 0 you should
                 also have finished course 1. So it is impossible.
    

**Note:**

  1. The input prerequisites is a graph represented by **a list of edges** , not adjacency matrices. Read more about [how a graph is represented](https://www.khanacademy.org/computing/computer-science/algorithms/graph-representation/a/representing-graphs).
  2. You may assume that there are no duplicate edges in the input prerequisites.

### Solution 1
This problem is equivalent to detecting a cycle in the directed graph
represented by `prerequisites`. Both BFS and DFS can be used to solve it using
the idea of **topological sort**. Since `pair<int, int>` is inconvenient for
implementing graph algorithms, we first transform it to the adjacency-list
representation. If course `u` is a prerequisite of course `v`, then the
adjacency list of `u` will contain `v`.

 **BFS**

BFS uses the indegrees of each node. We will first try to find a node with `0`
indegree. If we fail to do so, there must be a cycle in the graph and we
return `false`. Otherwise we set its indegree to be `-1` to prevent from
visiting it again and reduce the indegrees of its neighbors by `1`. This
process will be repeated for `n` (number of nodes) times.

    
    
    class Solution {
    public:
        bool canFinish(int numCourses, vector<pair<int, int>>& prerequisites) {
            graph g = buildGraph(numCourses, prerequisites);
            vector<int> degrees = computeIndegrees(g);
            for (int i = 0; i < numCourses; i++) {
                int j = 0;
                for (; j < numCourses; j++) {
                    if (!degrees[j]) {
                        break;
                    }
                }
                if (j == numCourses) {
                    return false;
                }
                degrees[j]--;
                for (int v : g[j]) {
                    degrees[v]--;
                }
            }
            return true;
        }
    private:
        typedef vector<vector<int>> graph;
        
        graph buildGraph(int numCourses, vector<pair<int, int>>& prerequisites) {
            graph g(numCourses);
            for (auto p : prerequisites) {
                g[p.second].push_back(p.first);
            }
            return g;
        }
        
        vector<int> computeIndegrees(graph& g) {
            vector<int> degrees(g.size(), 0);
            for (auto adj : g) {
                for (int v : adj) {
                    degrees[v]++;
                }
            }
            return degrees;
        }
    };
    

**DFS**

For DFS, in each visit, we start from a node and keep visiting its neighbors,
if at a time we return to a visited node, there is a cycle. Otherwise, start
again from another unvisited node and repeat this process. We use `todo` and
`done` for nodes to visit and visited nodes.

    
    
    class Solution {
    public:
        bool canFinish(int numCourses, vector<pair<int, int>>& prerequisites) {
            graph g = buildGraph(numCourses, prerequisites);
            vector<bool> todo(numCourses, false), done(numCourses, false);
            for (int i = 0; i < numCourses; i++) {
                if (!done[i] && !acyclic(g, todo, done, i)) {
                    return false;
                }
            }
            return true;
        }
    private:
        typedef vector<vector<int>> graph;
        
        graph buildGraph(int numCourses, vector<pair<int, int>>& prerequisites) {
            graph g(numCourses);
            for (auto p : prerequisites) {
                g[p.second].push_back(p.first);
            }
            return g;
        }
        
        bool acyclic(graph& g, vector<bool>& todo, vector<bool>& done, int node) {
            if (todo[node]) {
                return false;
            }
            if (done[node]) {
                return true;
            }
            todo[node] = done[node] = true;
            for (int v : g[node]) {
                if (!acyclic(g, todo, done, v)) {
                    return false;
                }
            }
            todo[node] = false;
            return true;
        }
    };
    


### Solution 2
    
    
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int[][] matrix = new int[numCourses][numCourses]; // i -> j
        int[] indegree = new int[numCourses];
        
        for (int i=0; i<prerequisites.length; i++) {
            int ready = prerequisites[i][0];
            int pre = prerequisites[i][1];
            if (matrix[pre][ready] == 0)
                indegree[ready]++; //duplicate case
            matrix[pre][ready] = 1;
        }
        
        int count = 0;
        Queue<Integer> queue = new LinkedList();
        for (int i=0; i<indegree.length; i++) {
            if (indegree[i] == 0) queue.offer(i);
        }
        while (!queue.isEmpty()) {
            int course = queue.poll();
            count++;
            for (int i=0; i<numCourses; i++) {
                if (matrix[course][i] != 0) {
                    if (--indegree[i] == 0)
                        queue.offer(i);
                }
            }
        }
        return count == numCourses;
    }


### Solution 3
    
    
    def canFinish(self, numCourses, prerequisites):
        graph = [[] for _ in xrange(numCourses)]
        visit = [0 for _ in xrange(numCourses)]
        for x, y in prerequisites:
            graph[x].append(y)
        def dfs(i):
            if visit[i] == -1:
                return False
            if visit[i] == 1:
                return True
            visit[i] = -1
            for j in graph[i]:
                if not dfs(j):
                    return False
            visit[i] = 1
            return True
        for i in xrange(numCourses):
            if not dfs(i):
                return False
        return True
    

  1. if node `v` has not been visited, then mark it as `0`.
  2. if node `v` is being visited, then mark it as `-1`. If we find a vertex marked as `-1` in DFS, then their is a ring.
  3. if node `v` has been visited, then mark it as `1`. If a vertex was marked as `1`, then no ring contains `v` or its successors.

 _References:[daoluan.net](http://daoluan.net/blog/map-ring/)_



