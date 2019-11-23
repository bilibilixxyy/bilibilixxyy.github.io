---
layout: post
title: 210. Course Schedule II
---
### Question
There are a total of _n_ courses you have to take, labeled from `0` to `n-1`.

Some courses may have prerequisites, for example to take course 0 you have to
first take course 1, which is expressed as a pair: `[0,1]`

Given the total number of courses and a list of prerequisite **pairs** ,
return the ordering of courses you should take to finish all courses.

There may be multiple correct orders, you just need to return one of them. If
it is impossible to finish all courses, return an empty array.

 **Example 1:**

    
    
     **Input:** 2, [[1,0]] 
    **Output:**[0,1]
    **Explanation:**  There are a total of 2 courses to take. To take course 1 you should have finished   
                 course 0. So the correct course order is [0,1] .

**Example 2:**

    
    
    **Input:** 4, [[1,0],[2,0],[3,1],[3,2]]
    **Output:**[0,1,2,3] or [0,2,1,3]
    **Explanation:**  There are a total of 4 courses to take. To take course 3 you should have finished both     
                 courses 1 and 2. Both courses 1 and 2 should be taken after you finished course 0. 
                 So one correct course order is [0,1,2,3]. Another correct ordering is [0,2,1,3] .

 **Note:**

  1. The input prerequisites is a graph represented by **a list of edges** , not adjacency matrices. Read more about [how a graph is represented](https://www.khanacademy.org/computing/computer-science/algorithms/graph-representation/a/representing-graphs).
  2. You may assume that there are no duplicate edges in the input prerequisites.

### Solution 1
This question asks for an order in which prerequisite courses must be taken
first. This prerequisite relationship reminds one of directed graphs. Then,
the problem reduces to find a topological sort order of the courses, which
would be a DAG if it has a valid order.

    
    
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] incLinkCounts = new int[numCourses];
        List<List<Integer>> adjs = new ArrayList<>(numCourses);
        initialiseGraph(incLinkCounts, adjs, prerequisites);
        //return solveByBFS(incLinkCounts, adjs);
        return solveByDFS(adjs);
    }
    

The first step is to transform it into a directed graph. Since it is likely to
be sparse,we use adjacency list graph data structure. 1 -> 2 means 1 must be
taken before 2.

    
    
    private void initialiseGraph(int[] incLinkCounts, List<List<Integer>> adjs, int[][] prerequisites){
        int n = incLinkCounts.length;
        while (n-- > 0) adjs.add(new ArrayList<>());
        for (int[] edge : prerequisites) {
            incLinkCounts[edge[0]]++;
            adjs.get(edge[1]).add(edge[0]);
        }
    }
    

How can we obtain a topological sort order of a DAG?

We observe that if a node has incoming edges, it has prerequisites. Therefore,
the first few in the order must be those with no prerequisites, i.e. no
incoming edges. Any non-empty DAG must have at least one node without incoming
links. You can draw a small graph to convince yourself. If we visit these few
and remove all edges attached to them, we are left with a smaller DAG, which
is the same problem. This will then give our BFS solution.

    
    
    private int[] solveByBFS(int[] incLinkCounts, List<List<Integer>> adjs){
        int[] order = new int[incLinkCounts.length];
        Queue<Integer> toVisit = new ArrayDeque<>();
        for (int i = 0; i < incLinkCounts.length; i++) {
            if (incLinkCounts[i] == 0) toVisit.offer(i);
        }
        int visited = 0;
        while (!toVisit.isEmpty()) {
            int from = toVisit.poll();
            order[visited++] = from;
            for (int to : adjs.get(from)) {
                incLinkCounts[to]--;
                if (incLinkCounts[to] == 0) toVisit.offer(to);
            }
        }
        return visited == incLinkCounts.length ? order : new int[0]; 
    }
    

Another way to think about it is the last few in the order must be those which
are not prerequisites of other courses. Thinking it recursively means if one
node has unvisited child node, you should visit them first before you put this
node down in the final order array. This sounds like the post-order of a DFS.
Since we are putting nodes down in the reverse order, we should reverse it
back to correct ordering or use a stack.

    
    
    private int[] solveByDFS(List<List<Integer>> adjs) {
        BitSet hasCycle = new BitSet(1);
        BitSet visited = new BitSet(adjs.size());
        BitSet onStack = new BitSet(adjs.size());
        Deque<Integer> order = new ArrayDeque<>();
        for (int i = adjs.size() - 1; i >= 0; i--) {
            if (visited.get(i) == false && hasOrder(i, adjs, visited, onStack, order) == false) return new int[0];
        }
        int[] orderArray = new int[adjs.size()];
        for (int i = 0; !order.isEmpty(); i++) orderArray[i] = order.pop();
        return orderArray;
    }
    
    private boolean hasOrder(int from, List<List<Integer>> adjs, BitSet visited, BitSet onStack, Deque<Integer> order) {
        visited.set(from);
        onStack.set(from);
        for (int to : adjs.get(from)) {
            if (visited.get(to) == false) {
                if (hasOrder(to, adjs, visited, onStack, order) == false) return false;
            } else if (onStack.get(to) == true) {
                return false;
            }
        }
        onStack.clear(from);
        order.push(from);
        return true;
    }


### Solution 2
    
    
    public int[] findOrder(int numCourses, int[][] prerequisites) { 
        if (numCourses == 0) return null;
        // Convert graph presentation from edges to indegree of adjacent list.
        int indegree[] = new int[numCourses], order[] = new int[numCourses], index = 0;
        for (int i = 0; i < prerequisites.length; i++) // Indegree - how many prerequisites are needed.
            indegree[prerequisites[i][0]]++;    
    
        Queue<Integer> queue = new LinkedList<Integer>();
        for (int i = 0; i < numCourses; i++) 
            if (indegree[i] == 0) {
                // Add the course to the order because it has no prerequisites.
                order[index++] = i;
                queue.offer(i);
            }
    
        // How many courses don't need prerequisites. 
        while (!queue.isEmpty()) {
            int prerequisite = queue.poll(); // Already finished this prerequisite course.
            for (int i = 0; i < prerequisites.length; i++)  {
                if (prerequisites[i][1] == prerequisite) {
                    indegree[prerequisites[i][0]]--; 
                    if (indegree[prerequisites[i][0]] == 0) {
                        // If indegree is zero, then add the course to the order.
                        order[index++] = prerequisites[i][0];
                        queue.offer(prerequisites[i][0]);
                    }
                } 
            }
        }
    
        return (index == numCourses) ? order : new int[0];
    }


### Solution 3
Similar to [Course Schedule](https://leetcode.com/problems/course-schedule/),
during the process of visiting the nodes, store them in order to a `vector`.
In DFS, you may need an additional `reverse`.

 **BFS**

    
    
     class Solution {
    public:
        vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
            graph g = buildGraph(numCourses, prerequisites);
            vector<int> degrees = computeIndegrees(g);
            vector<int> order;
            for (int i = 0; i < numCourses; i++) {
                int j = 0;
                for (; j < numCourses; j++) {
                    if (!degrees[j]) {
                        order.push_back(j);
                        break;
                    }
                }
                if (j == numCourses) {
                    return {};
                }
                degrees[j]--;
                for (int v : g[j]) {
                    degrees[v]--;
                }
            }        
            return order;
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

    
    
     class Solution {
    public:
        vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
            graph g = buildGraph(numCourses, prerequisites);
            vector<int> order;
            vector<bool> todo(numCourses, false), done(numCourses, false);
            for (int i = 0; i < numCourses; i++) {
                if (!done[i] && !acyclic(g, todo, done, i, order)) {
                    return {};
                }
            }
            reverse(order.begin(), order.end());
            return order;
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
        
        bool acyclic(graph& g, vector<bool>& todo, vector<bool>& done, int node, vector<int>& order) {
            if (todo[node]) {
                return false;
            }
            if (done[node]) {
                return true;
            }
            todo[node] = done[node] = true;
            for (int neigh : g[node]) {
                if (!acyclic(g, todo, done, neigh, order)) {
                    return false;
                }
            }
            order.push_back(node);
            todo[node] = false;
            return true;
        }
    };
    



