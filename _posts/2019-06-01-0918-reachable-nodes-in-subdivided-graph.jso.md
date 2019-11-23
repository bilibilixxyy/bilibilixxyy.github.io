---
layout: post
title: 918. Reachable Nodes In Subdivided Graph
---
### Question
Starting with an  **undirected** graph (the "original graph") with nodes from
`0` to `N-1`, subdivisions are made to some of the edges.

The graph is given as follows: `edges[k]` is a list of integer pairs `(i, j,
n)` such that `(i, j)` is an edge of the original graph,

and `n` is the total number of **new** nodes on that edge.  

Then, the edge `(i, j)` is deleted from the original graph, `n` new nodes
`(x_1, x_2, ..., x_n)` are added to the original graph,

and `n+1` new edges `(i, x_1), (x_1, x_2), (x_2, x_3), ..., (x_{n-1}, x_n),
(x_n, j)` are added to the original graph.

Now, you start at node `0` from the original graph, and in each move, you
travel along one edge.

Return how many nodes you can reach in at most `M` moves.



 **Example 1:**

    
    
     **Input:**edges =  [[0,1,10],[0,2,1],[1,2,2]], M = 6, N = 3
    **Output:** 13
    **Explanation:**
    The nodes that are reachable in the final graph after M = 6 moves are indicated below.
    ![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/08/01/origfinal.png)
    

**Example 2:**

    
    
    **Input:**edges =  [[0,1,4],[1,2,6],[0,2,8],[1,3,1]], M = 10, N = 4
    **Output:** 23



 **Note:**

  1. `0 <= edges.length <= 10000`
  2. `0 <= edges[i][0] < edges[i][1] < N`
  3. There does not exist any `i != j` for which `edges[i][0] == edges[j][0]` and `edges[i][1] == edges[j][1]`.
  4. The original graph has no parallel edges.
  5. `0 <= edges[i][2] <= 10000`
  6. `0 <= M <= 10^9`
  7. `1 <= N <= 3000`
  8. A reachable node is a node that can be travelled to using at most M moves starting from node 0.

### Solution 1
This seems like a standard Dijkstra problem. Remember to calculate move even
cannot reach the next node.

    
    
    class Solution {
        public int reachableNodes(int[][] edges, int M, int N) {
            int[][] graph = new int[N][N];
            for (int i = 0; i < N; i++) {
                Arrays.fill(graph[i], -1);
            }
            for (int[] edge : edges) {
                graph[edge[0]][edge[1]] = edge[2];
                graph[edge[1]][edge[0]] = edge[2];
            }
            int result = 0;
            PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> (b[1] - a[1]));
            boolean[] visited = new boolean[N];
            pq.offer(new int[]{0, M});
            while (!pq.isEmpty()) {
                int[] cur = pq.poll();
                int start = cur[0];
                int move = cur[1];
                if (visited[start]) {
                    continue;
                }
                visited[start] = true;
                result++;
                for (int i = 0; i < N; i++) {
                    if (graph[start][i] > -1) {
                        if (move > graph[start][i] && !visited[i]) {
                            pq.offer(new int[]{i, move - graph[start][i] - 1});
                        }
                        graph[i][start] -= Math.min(move, graph[start][i]);
                        result += Math.min(move, graph[start][i]);
                    }
                }
            }
            return result;
        }
    }
    


### Solution 2
**Intuition** :  
Store `edges` to another 2D hashtable `e`, so that we can easier get length
between two node by `e[i][j]`.  
`seen[i]` means that we can arrive at node `i` and have `seen[i]` moves left.

We use a dijkstra algorithm in this solution.  
Priority queue `pq` store states `(moves left, node index)`.  
So every time when we pop from `pq`, we get the state with the most moves
left.

In the end, we calculate the number of nodes that we can reach.

`res = seen.length`  
For every edge `e[i][j]`:  
`res += min(seen.getOrDefault(i, 0) + seen.getOrDefault(j, 0), e[i][j])`

 **Time Complexity** :  
Reminded by @kAc:  
Dijkstra + Heap is O(E log E)  
Dijkstra + Fibonacci heap is O(N log N + E)

 **C++:**

    
    
         int reachableNodes(vector<vector<int>> edges, int M, int N) {
            unordered_map<int, unordered_map<int, int>> e;
            for (auto v : edges) e[v[0]][v[1]] = e[v[1]][v[0]] = v[2];
            priority_queue<pair<int, int>> pq;
            pq.push({M, 0});
            unordered_map<int, int> seen;
            while (pq.size()) {
                int moves = pq.top().first, i = pq.top().second;
                pq.pop();
                if (!seen.count(i)) {
                    seen[i] = moves;
                    for (auto j : e[i]) {
                        int moves2 = moves - j.second - 1;
                        if (!seen.count(j.first) && moves2 >= 0)
                            pq.push({ moves2, j.first});
                    }
                }
            }
            int res = seen.size();
            for (auto v : edges) {
                int a = seen.find(v[0]) == seen.end() ? 0 : seen[v[0]];
                int b = seen.find(v[1]) == seen.end() ? 0 : seen[v[1]];
                res += min(a + b, v[2]);
            }
            return res;
        }
    

**Java:**

    
    
        public int reachableNodes(int[][] edges, int M, int N) {
            HashMap<Integer, HashMap<Integer, Integer>> e =  new HashMap<>();
            for (int i = 0; i < N; ++i) e.put(i, new HashMap<>());
            for (int[] v : edges) {
                e.get(v[0]).put(v[1], v[2]);
                e.get(v[1]).put(v[0], v[2]);
            }
            PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> (b[0] - a[0]));
            pq.offer(new int[] {M, 0});
            HashMap<Integer, Integer> seen = new HashMap<>();
            while (!pq.isEmpty()) {
                int moves = pq.peek()[0], i = pq.peek()[1];
                pq.poll();
                if (!seen.containsKey(i)) {
                    seen.put(i,moves);
                    for (int j : e.get(i).keySet()) {
                        int moves2 = moves - e.get(i).get(j) - 1;
                        if (!seen.containsKey(j) && moves2 >= 0)
                            pq.offer(new int[] { moves2, j});
                    }
                }
            }
            int res = seen.size();
            for (int[] v : edges) {
                int a = seen.getOrDefault(v[0],0);
                int b = seen.getOrDefault(v[1],0);
                res += Math.min(a + b, v[2]);
            }
            return res;
        }
    

**Python:**

    
    
        def reachableNodes(self, edges, M, N):
            e = collections.defaultdict(dict)
             for i, j, l in edges: e[i][j] = e[j][i] = l
            pq = [(-M, 0)]
            seen = {}
            while pq:
                moves, i = heapq.heappop(pq)
                if i not in seen:
                    seen[i] = -moves
                    for j in e[i]:
                        moves2 = -moves - e[i][j] - 1
                        if j not in seen and moves2 >= 0:
                            heapq.heappush(pq, (-moves2, j))
            res = len(seen)
            for i, j, k in edges:
                res += min(seen.get(i, 0) + seen.get(j, 0), e[i][j])
            return res
    


### Solution 3
Thanks @wangzi6147 for the original post.

 **Logical Thinking**  
The problem is to get maximum number of nodes I can reach from node 0 within M
moves. If I figure out each node's shortest distance to src, the one with
distance <= M moves should be reachable. In this way, the problem can be
regarded as a single-source shortest-path problem, which can be solved by
**Dijkstra's Algorithm**.

Instead of maintaining a MinHeap which keeps track of shortest distances to
the source, we maintain a **MaxHeap** that keeps track of maximum moves
remained for each node. Since for a node,

    
    
    moves remained + distance from current  node to source = M
    

The bigger moves remained is, the smaller the distance will be. Thus, the
MaxHeap can also promise the shortest distance.

We rebuild the graph to a weighted graph such that weight of an edge is total
number of new nodes on that edge.

**Clear Code**

    
    
         public int reachableNodes(int[][] edges, int M, int N) {
            int[][] graph = new int[N][N];
    
            for (int[] tmp : graph) {
                Arrays.fill(tmp, -1);
            }
            for (int[] edge: edges) { // e.g. graph[0][1] = 4
                graph[edge[0]][edge[1]] = edge[2];
                graph[edge[1]][edge[0]] = edge[2];
            }
            int result = 0;
            
            boolean[] visited = new boolean[N];
            PriorityQueue<int[]> pq_node_movesRemained = new PriorityQueue<>((a, b) -> (b[1] - a[1]));
            pq_node_movesRemained.add(new int[] {0, M});
            
            while (!pq_node_movesRemained.isEmpty()) {
                int[] tmp = pq_node_movesRemained.poll();
                int node = tmp[0];
                int movesRemained = tmp[1];
                if (visited[node]) { 
                    continue;
                }
                visited[node] = true;
                result++;
                for (int nei = 0; nei < N; nei++) {
                    if (graph[node][nei] != -1) {
                        if (!visited[nei] && movesRemained >= graph[node][nei] + 1) {
                            pq_node_movesRemained.add(new int[]{nei, movesRemained - graph[node][nei] - 1});
                        }
                        int movesCost = Math.min(movesRemained, graph[node][nei]);
                        graph[nei][node] -= movesCost;
                        result += movesCost;
                    }
                }
            }
            
            return result;
        }
    

**I would appreciate your VOTE UP ;)**



