---
layout: post
title: 744. Network Delay Time
---
### Question
There are `N` network nodes, labelled `1` to `N`.

Given `times`, a list of travel times as **directed** edges `times[i] = (u, v,
w)`, where `u` is the source node, `v` is the target node, and `w` is the time
it takes for a signal to travel from source to target.

Now, we send a signal from a certain node `K`. How long will it take for all
nodes to receive the signal? If it is impossible, return `-1`.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2019/05/23/931_example_1.png)

    
    
     **Input:** times = [[2,1,1],[2,3,1],[3,4,1]], N = 4, K = 2
    **Output:** 2
    



 **Note:**

  1. `N` will be in the range `[1, 100]`.
  2. `K` will be in the range `[1, N]`.
  3. The length of `times` will be in the range `[1, 6000]`.
  4. All edges `times[i] = (u, v, w)` will have `1 <= u, v <= N` and `0 <= w <= 100`.

### Solution 1
 **C++**

    
    
     class Solution {
    public:
        int networkDelayTime(vector<vector<int>>& times, int N, int K) {
            vector<int> dist(N + 1, INT_MAX);
            dist[K] = 0;
            for (int i = 0; i < N; i++) {
                for (vector<int> e : times) {
                    int u = e[0], v = e[1], w = e[2];
                    if (dist[u] != INT_MAX && dist[v] > dist[u] + w) {
                        dist[v] = dist[u] + w;
                    }
                }
            }
    
            int maxwait = 0;
            for (int i = 1; i <= N; i++)
                maxwait = max(maxwait, dist[i]);
            return maxwait == INT_MAX ? -1 : maxwait;
        }
    };
    


### Solution 2
  * Heap

    
    
    class Solution:
        def networkDelayTime(self, times, N, K):
            q, t, adj = [(0, K)], {}, collections.defaultdict(list)
            for u, v, w in times:
                adj[u].append((v, w))
            while q:
                time, node = heapq.heappop(q)
                if node not in t:
                    t[node] = time
                    for v, w in adj[node]:
                        heapq.heappush(q, (time + w, v))
            return max(t.values()) if len(t) == N else -1
    

  * Queue

    
    
    class Solution:
        def networkDelayTime(self, times, N, K):
            t, graph, q = [0] + [float("inf")] * N, collections.defaultdict(list), collections.deque([(0, K)])
            for u, v, w in times:
                graph[u].append((v, w))
            while q:
                time, node = q.popleft()
                if time < t[node]:
                    t[node] = time
                    for v, w in graph[node]:
                        q.append((time + w, v))
            mx = max(t)
            return mx if mx < float("inf") else -1
    


### Solution 3
I think bfs and djikstra are very similar problems. It's just that djikstra
cost is different compared with bfs, so use priorityQueue instead a Queue for
a standard bfs search.

    
    
    class Solution {
        public int networkDelayTime(int[][] times, int N, int K) {
            Map<Integer, Map<Integer,Integer>> map = new HashMap<>();
            for(int[] time : times){
                map.putIfAbsent(time[0], new HashMap<>());
                map.get(time[0]).put(time[1], time[2]);
            }
            
            //distance, node into pq
            Queue<int[]> pq = new PriorityQueue<>((a,b) -> (a[0] - b[0]));
            
            pq.add(new int[]{0, K});
            
            boolean[] visited = new boolean[N+1];
            int res = 0;
            
            while(!pq.isEmpty()){
                int[] cur = pq.remove();
                int curNode = cur[1];
                int curDist = cur[0];
                if(visited[curNode]) continue;
                visited[curNode] = true;
                res = curDist;
                N--;
                if(map.containsKey(curNode)){
                    for(int next : map.get(curNode).keySet()){
                        pq.add(new int[]{curDist + map.get(curNode).get(next), next});
                    }
                }
            }
            return N == 0 ? res : -1;
                
        }
    }
    



