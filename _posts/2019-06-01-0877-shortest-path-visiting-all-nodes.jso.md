---
layout: post
title: 877. Shortest Path Visiting All Nodes
---
### Question
An undirected, connected graph of N nodes (labeled `0, 1, 2, ..., N-1`) is
given as `graph`.

`graph.length = N`, and `j != i` is in the list `graph[i]` exactly once, if
and only if nodes `i` and `j` are connected.

Return the length of the shortest path that visits every node. You may start
and stop at any node, you may revisit nodes multiple times, and you may reuse
edges.



 **Example 1:**

    
    
     **Input:** [[1,2,3],[0],[0],[0]]
    **Output:** 4
    **Explanation** : One possible path is [1,0,2,0,3]

**Example 2:**

    
    
    **Input:** [[1],[0,2,4],[1,3,4],[2],[1,2]]
    **Output:** 4
    **Explanation** : One possible path is [0,1,4,2,3]
    



 **Note:**

  1. `1 <= graph.length <= 12`
  2. `0 <= graph[i].length < graph.length`

### Solution 1
<https://www.youtube.com/watch?v=Zx8rhXFichQ>

Not doing a good job this time. Wasted a lot of time debugging on some stupid
mistakes in problem 4, and debugging on LeetCode web UI is definitely not a
great experience :p

Hope it's helpful.


### Solution 2
Idea is to use `BFS` to traverse the graph. Since all edges are weighted 1, we
can use a Queue (instead of a PriorityQueue sorted by cost). Since all edges
are weighted 1, then closer nodes will always be evaluated before farther
ones.

In order to represent a path, I used a combination of 3 variables:

  1. `int bitMask`: mask of all the nodes we visited so far: `0 -> not visited`, `1 -> visited` (Originally this was `Set<Integer>`of all nodes we visited so far, but since the Solution `TLE` and `N <= 12`, it turns out we can use a bitMask and `32 bits` total in an Integer can cover all the possibilities. This is a small speed up optimization.)
  2. `int curr`: current node we are on
  3. `int cost`: the total cost in the path.

Each path taken will have a unique combination of these 3 variables.

We initialize our queue to contain N possible paths each starting from
[0,N-1]. This is because we can start at any of N possible Nodes.

At each step, we remove element from the `queue` and see if we have covered
all 12 nodes in our `bitMask`. If we cover all nodes, we return the cost of
the path immediately. Since we are using `BFS`, this is _guranteed to be path
with the lowest cost._

Otherwise, we get all the neighbors of current node, and for each neighbor,
set the Node to visited in `bitMask`, and then add it back into the queue.

In order to prevent duplicate paths from being visited, we use a `Set<Tuple>`
to store the `Set<Path>` that we have visited before. Since we don't really
need the `cost` here, I set `cost` to 0 for elements stored in `Set`. _You
could also set the actual cost value here, it wouldn't make a difference :)_

    
    
     class Solution {
        
        public int shortestPathLength(int[][] graph) {
            
            int N = graph.length;
            
            Queue<Tuple> queue = new LinkedList<>();
            Set<Tuple> set = new HashSet<>();
    
            for(int i = 0; i < N; i++){
                int tmp = (1 << i);
                set.add(new Tuple(tmp, i, 0));
                queue.add(new Tuple(tmp, i, 1));
            }
            
            while(!queue.isEmpty()){
                Tuple curr = queue.remove();
        
                if(curr.bitMask == (1 << N) - 1){
                    return curr.cost - 1;
                } else {
                    int[] neighbors = graph[curr.curr];
                    
                    for(int v : neighbors){
                        int bitMask = curr.bitMask;
                        bitMask = bitMask | (1 << v);
                        
                        Tuple t = new Tuple(bitMask, v, 0);
                        if(!set.contains(t)){
                            queue.add(new Tuple(bitMask, v, curr.cost + 1));
                            set.add(t);
                        }         
                    }
                }
            }
            return -1;
        }
    }
    
    class Tuple{
        int bitMask;
        int curr;
        int cost;
        
        public Tuple(int bit, int n, int c){
            bitMask = bit;
            curr = n;
            cost = c;
        }
        
        public boolean equals(Object o){
            Tuple p = (Tuple) o;
            return bitMask == p.bitMask && curr == p.curr && cost == p.cost;
        }
        
        public int hashCode(){
            return 1331 * bitMask + 7193 * curr + 727 * cost;
        }
    }
    


### Solution 3
Using a two-dimensional matrix to record the distance from a point through
those points.  
Example:  
dp[0][3(00000...00011)] = 2 means the distance starting point 0 through 0, 1
is 2  
dp[1][7(00000...00111)] = 3 means the distance starting point 1 through 0, 1,
2 is 3.

After BFS, we will know the distance through all nodes, compare
dp[0][11111...11111](it means that start point 0 through all nodes),
dp[1][11111...11111]  
..., dp[n-1][11111...11111], we will get the result.

    
    
    class Solution {
        public int shortestPathLength(int[][] graph) {
    	int[][] dp = new int[graph.length][1<<graph.length];
            Queue<State> queue =  new LinkedList<>();
            for (int i = 0; i < graph.length; i++) {
                Arrays.fill(dp[i], Integer.MAX_VALUE);
                dp[i][1<<i]=0;
                queue.offer(new State(1<<i, i));
            }
            
            while (!queue.isEmpty()) {
                State state = queue.poll();
                
                for (int next : graph[state.source]) {
                    int nextMask = state.mask | 1 << next;
                    if (dp[next][nextMask] > dp[state.source][state.mask]+1) {
                        dp[next][nextMask] = dp[state.source][state.mask]+1;
                        queue.offer(new State(nextMask, next));
                    }
                }
            }
            
            int res = Integer.MAX_VALUE;
            for (int i = 0; i < graph.length; i++) {
                res = Math.min(res, dp[i][(1<<graph.length)-1]);
            }
            return res;
    	}
    
    	class State {
    		public int mask, source;
    		public State(int m, int s) {
    			mask=m;
    			source=s;
    		}
    	}
    }
    



