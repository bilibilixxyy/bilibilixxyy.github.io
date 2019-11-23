---
layout: post
title: 976. Minimum Area Rectangle
---
### Question
Given a set of points in the xy-plane, determine the minimum area of a
rectangle formed from these points, with sides parallel to the x and y axes.

If there isn't any rectangle, return 0.



 **Example 1:**

    
    
     **Input:** [[1,1],[1,3],[3,1],[3,3],[2,2]]
    **Output:** 4
    

**Example 2:**

    
    
    **Input:** [[1,1],[1,3],[3,1],[3,3],[4,1],[4,3]]
    **Output:** 2
    



 **Note** :

  1. `1 <= points.length <= 500`
  2. `0 <= points[i][0] <= 40000`
  3. `0 <= points[i][1] <= 40000`
  4. All points are distinct.

### Solution 1
    
    
    class Solution {
        public int minAreaRect(int[][] points) {
            Map<Integer, Set<Integer>> map = new HashMap<>();
            for (int[] p : points) {
                if (!map.containsKey(p[0])) {
                    map.put(p[0], new HashSet<>());
                }
                map.get(p[0]).add(p[1]);
            }
            int min = Integer.MAX_VALUE;
            for (int[] p1 : points) {
                for (int[] p2 : points) {
                    if (p1[0] == p2[0] || p1[1] == p2[1]) { // if have the same x or y
                        continue;
                    }
                    if (map.get(p1[0]).contains(p2[1]) && map.get(p2[0]).contains(p1[1])) { // find other two points
                        min = Math.min(min, Math.abs(p1[0] - p2[0]) * Math.abs(p1[1] - p2[1]));
                    }
                }
            }
            return min == Integer.MAX_VALUE ? 0 : min;
        }
    }
    


### Solution 2
`N = 500` is really loose.  
Brute force, check all pairs of points.  
`O(N^2)` , 1000ms ~ 1200ms

    
    
        def minAreaRect(self, points):
            seen = set()
            res = float('inf')
            for x1, y1 in points:
                for x2, y2 in seen:
                    if (x1, y2) in seen and (x2, y1) in seen:
                        area = abs(x1 - x2) * abs(y1 - y2)
                        if area and area < res:
                            res = area
                seen.add((x1, y1))
            return res if res < float('inf') else 0
    

Another idea is that,  
For each `x` value in sorted order, check all `y` pairs.

In most cases, all points randomly distribute.  
Only 500 points in 40001 * 40001 possible coordinates.  
No two points will have the same `x` value or `y` value.  
There will be no rectangle and the result is `0`.  
This will be `O(N)` solution.

However, it seems that, in the test cases, it has a really big amount of
rectangles.  
In these worst cases, the time complexity is `O(nx * nx * ny)` < `O(N ^ 1.5)`.

In the extreme worst cases, like all points have `x = 0` or `y = 0`  
Time complexity will be `O(N^2)`

`O(N^1.5)`, 80ms

    
    
        def minAreaRect(self, points):
            n = len(points)
            nx = len(set(x for x, y in points))
            ny = len(set(y for x, y in points))
            if nx == n or ny == n:
                return 0
    
            p = collections.defaultdict(list)
            if nx > ny:
                for x, y in points:
                    p[x].append(y)
            else:
                for x, y in points:
                    p[y].append(x)
    
            lastx = {}
            res = float('inf')
            for x in sorted(p):
                p[x].sort()
                for i in range(len(p[x])):
                    for j in range(i):
                        y1, y2 = p[x][j], p[x][i]
                        if (y1, y2) in lastx:
                            res = min(res, (x - lastx[y1, y2]) * (y2 - y1))
                        lastx[y1, y2] = x
            return res if res < float('inf') else 0
    


### Solution 3
  1. Collect x coords in a hash map.
  2. For each x coord, collect y coords in a set (sorted).
  3. Go through each pair of x coords in the hash map. Ignore x coords that have less than 2 y-s.
  4. Intersect two sets to get common y coords for this pair of x coords.
  5. Since y coords are sorted, you only need to calculate the area for the `y[k] - y[k - 1]` pairs.

    
    
    int minAreaRect(vector<vector<int>>& points, int res = INT_MAX) {
      unordered_map<int, set<int>> x;
      for (auto p : points) x[p[0]].insert(p[1]);
      for (auto i = x.begin(); i != x.end(); ++i)
        for (auto j = next(i); j != x.end(); ++j) {
          if (i->second.size() < 2 || j->second.size() < 2) continue;
          vector<int> y;
          set_intersection(begin(i->second), end(i->second),
            begin(j->second), end(j->second), back_inserter(y));
          for (int k = 1; k < y.size(); ++k)
            res = min(res, abs(j->first - i->first) * (y[k] - y[k - 1]));
        }
      return res == INT_MAX ? 0 : res;
    }
    

## Complexity Analysis

Runtime: _O(n * n)_. We check each x coordinate agains all other x
coordinates. The set intersection is linear.  
Memory: _O(n)_. We store all points in our sets.



