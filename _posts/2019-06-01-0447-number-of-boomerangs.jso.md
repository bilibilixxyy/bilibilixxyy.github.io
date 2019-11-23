---
layout: post
title: 447. Number of Boomerangs
---
### Question
Given _n_ points in the plane that are all pairwise distinct, a "boomerang" is
a tuple of points `(i, j, k)` such that the distance between `i` and `j`
equals the distance between `i` and `k` ( **the order of the tuple matters**
).

Find the number of boomerangs. You may assume that _n_ will be at most **500**
and coordinates of points are all in the range **[-10000, 10000]**
(inclusive).

 **Example:**

    
    
     **Input:**
    [[0,0],[1,0],[2,0]]
    
    **Output:**
    2
    
    **Explanation:**
    The two boomerangs are **[[1,0],[0,0],[2,0]]** and **[[1,0],[2,0],[0,0]]**
    

### Solution 1
    
    
     public int numberOfBoomerangs(int[][] points) {
        int res = 0;
    
        Map<Integer, Integer> map = new HashMap<>();
        for(int i=0; i<points.length; i++) {
            for(int j=0; j<points.length; j++) {
                if(i == j)
                    continue;
                
                int d = getDistance(points[i], points[j]);                
                map.put(d, map.getOrDefault(d, 0) + 1);
            }
            
            for(int val : map.values()) {
                res += val * (val-1);
            }            
            map.clear();
        }
        
        return res;
    }
    
    private int getDistance(int[] a, int[] b) {
        int dx = a[0] - b[0];
        int dy = a[1] - b[1];
        
        return dx*dx + dy*dy;
    }
    
    Time complexity:  O(n^2)
    Space complexity: O(n)


### Solution 2
for each point, create a hashmap and count all points with same distance. If
for a point p, there are k points with distance d, number of boomerangs
corresponding to that are k*(k-1). Keep adding these to get the final result.

    
    
            res = 0
            for p in points:
                cmap = {}
                for q in points:
                    f = p[0]-q[0]
                    s = p[1]-q[1]
                    cmap[f*f + s*s] = 1 + cmap.get(f*f + s*s, 0)
                for k in cmap:
                    res += cmap[k] * (cmap[k] -1)
            return res
    
    


### Solution 3
    
    
    int numberOfBoomerangs(vector<pair<int, int>>& points) {
        int booms = 0;
        for (auto &p : points) {
            unordered_map<double, int> ctr(points.size());
            for (auto &q : points)
                booms += 2 * ctr[hypot(p.first - q.first, p.second - q.second)]++;
        }
        return booms;
    }
    

Try each point as the "axis" of the boomerang, i.e., the "i" part of the
triple. Group its distances to all other points by distance, counting the
boomerangs as we go. No need to avoid q == p, as it'll be alone in the
distance == 0 group and thus won't influence the outcome.

Submitted five times, accepted in 1059, 1022, 1102, 1026 and 1052 ms, average
is 1052.2 ms. The initial capacity for `ctr` isn't necessary, just helps make
it fast. Without it, I got accepted in 1542, 1309, 1302, 1306 and 1338 ms.



