---
layout: post
title: 803. Cheapest Flights Within K Stops
---
### Question
There are `n` cities connected by `m` flights. Each fight starts from city `u
`and arrives at `v` with a price `w`.

Now given all the cities and flights, together with starting city `src` and
the destination `dst`, your task is to find the cheapest price from `src` to
`dst` with up to `k` stops. If there is no such route, output `-1`.

    
    
     **Example 1:**
    **Input:** 
    n = 3, edges = [[0,1,100],[1,2,100],[0,2,500]]
    src = 0, dst = 2, k = 1
    **Output:** 200
    **Explanation:** 
    The graph looks like this:
    ![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/02/16/995.png)
    
    The cheapest price from city 0 to city 2 with at most 1 stop costs 200, as marked red in the picture.
    
    
    **Example 2:**
    **Input:** 
    n = 3, edges = [[0,1,100],[1,2,100],[0,2,500]]
    src = 0, dst = 2, k = 0
    **Output:** 500
    **Explanation:** 
    The graph looks like this:
    ![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/02/16/995.png)
    
    The cheapest price from city 0 to city 2 with at most 0 stop costs 500, as marked blue in the picture.

 **Note:**

  * The number of nodes `n` will be in range `[1, 100]`, with nodes labeled from `0` to `n`` - 1`.
  * The size of `flights` will be in range `[0, n * (n - 1) / 2]`.
  * The format of each flight will be `(src, ``dst``, price)`.
  * The price of each flight will be in the range `[1, 10000]`.
  * `k` is in the range of `[0, n - 1]`.
  * There will not be any duplicated flights or self cycles.

### Solution 1
 **Idea**  
It happen to be the same idea of Dijkstra's algorithm, but we need to keep the
path.  
 **More**  
More helpful and detailed explanation here:  
[https://en.wikipedia.org/wiki/Dijkstra's_algorithm](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm)

Python:

    
    
        def findCheapestPrice(self, n, flights, src, dst, k):
            f = collections.defaultdict(dict)
            for a, b, p in flights:
                f[a][b] = p
            heap = [(0, src, k + 1)]
            while heap:
                p, i, k = heapq.heappop(heap)
                if i == dst:
                    return p
                if k > 0:
                    for j in f[i]:
                        heapq.heappush(heap, (p + f[i][j], j, k - 1))
            return -1
    

Thanks sur081547 for  
Java version:

    
    
        public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
            Map<Integer, Map<Integer, Integer>> prices = new HashMap<>();
            for (int[] f : flights) {
                if (!prices.containsKey(f[0])) prices.put(f[0], new HashMap<>());
                prices.get(f[0]).put(f[1], f[2]);
            }
            Queue<int[]> pq = new PriorityQueue<>((a, b) -> (Integer.compare(a[0], b[0])));
            pq.add(new int[] {0, src, k + 1});
            while (!pq.isEmpty()) {
                int[] top = pq.remove();
                int price = top[0];
                int city = top[1];
                int stops = top[2];
                if (city == dst) return price;
                if (stops > 0) {
                    Map<Integer, Integer> adj = prices.getOrDefault(city, new HashMap<>());
                    for (int a : adj.keySet()) {
                        pq.add(new int[] {price + adj.get(a), a, stops - 1});
                    }
                }
            }
            return -1;
        }
    


### Solution 2
    
    
    class Solution {
    public:
        //bellman ford.
        //just run it k+1 iterations.
        int findCheapestPrice(int n, vector<vector<int>>& a, int src, int sink, int k) {
            
            vector<int> c(n, 1e8);
            c[src] = 0;
            
            for(int z=0; z<=k; z++){
                vector<int> C(c);
                for(auto e: a)
                    C[e[1]] = min(C[e[1]], c[e[0]] + e[2]);
                c = C;
            }
            return c[sink] == 1e8 ? -1 : c[sink];
        }
    };
    


### Solution 3
The key point for this problem using Dijkstra's Algorithm is to have two
different weights on each edge, because if only consider cost, intermediate
cases with higher cost but fewer stops which can further become the shortest
valid solution will be discarded.

I use a minHeap as the conventional Dijkstra's Algorithm does. The difference
is that conventional Dijkstra's Algorithm would remove higher cost with fewer
stops cases from heap and only offer cases with lower cost into heap, but I
keep all valid intermediate cases in heap and offer higher cost but fewer
stops cases into heap as well.

    
    
    class Solution {
        private class City implements Comparable<City>{
            int id;
            int costFromSrc;
            int stopFromSrc;
            public City(int id, int costFromSrc, int stopFromSrc){
                this.id = id;
                this.costFromSrc = costFromSrc;
                this.stopFromSrc = stopFromSrc;
            }
            public boolean equals(City c){
                if(c instanceof City)
                    return this.id == c.id;
                return false;
            }
            public int compareTo(City c){
                return this.costFromSrc - c.costFromSrc;
            }
        }
        public int findCheapestPrice(int n, int[][] flights, int src, int dst, int K) {
            int[][] srcToDst = new int[n][n];
            for(int i = 0; i < flights.length; i++)
                srcToDst[flights[i][0]][flights[i][1]] = flights[i][2]; 
    						
            PriorityQueue<City> minHeap = new PriorityQueue();
            minHeap.offer(new City(src,0,0));
    				
            int[] cost = new int[n];
            Arrays.fill(cost, Integer.MAX_VALUE);
            cost[src] = 0;
            int[] stop = new int[n];
            Arrays.fill(stop, Integer.MAX_VALUE);
            stop[src] = 0;
    				
            while(!minHeap.isEmpty()){
                City curCity = minHeap.poll();
                if(curCity.id == dst) return curCity.costFromSrc;
                if(curCity.stopFromSrc == K + 1) continue;
                int[] nexts = srcToDst[curCity.id];
                for(int i = 0; i < n; i++){
                    if(nexts[i] != 0){
                        int newCost = curCity.costFromSrc + nexts[i];
                        int newStop = curCity.stopFromSrc + 1;
                        if(newCost < cost[i]){
                            minHeap.offer(new City(i, newCost, newStop));
                            cost[i] = newCost;
                        }
                        else if(newStop < stop[i]){
                            minHeap.offer(new City(i, newCost, newStop));
                            stop[i] = newStop;
                        }
                    }
                }
            }
            
            return cost[dst] == Integer.MAX_VALUE? -1:cost[dst];
        }
    }
    



