---
layout: post
title: 1003. Minimum Area Rectangle II
---
### Question
Given a set of points in the xy-plane, determine the minimum area of **any**
rectangle formed from these points, with sides **not necessarily parallel** to
the x and y axes.

If there isn't any rectangle, return 0.



 **Example 1:**

![](https://assets.leetcode.com/uploads/2018/12/21/1a.png)

    
    
     **Input:** [[1,2],[2,1],[1,0],[0,1]]
    **Output:** 2.00000
    **Explanation:**  The minimum area rectangle occurs at [1,2],[2,1],[1,0],[0,1], with an area of 2.
    

**Example 2:**

![](https://assets.leetcode.com/uploads/2018/12/22/2.png)

    
    
    **Input:** [[0,1],[2,1],[1,1],[1,0],[2,0]]
    **Output:** 1.00000
    **Explanation:** The minimum area rectangle occurs at [1,0],[1,1],[2,1],[2,0], with an area of 1.
    

**Example 3:**

![](https://assets.leetcode.com/uploads/2018/12/22/3.png)

    
    
    **Input:** [[0,3],[1,2],[3,1],[1,3],[2,1]]
    **Output:** 0
    **Explanation:** There is no possible rectangle to form from these points.
    

**Example 4:**

![](https://assets.leetcode.com/uploads/2018/12/21/4c.png)

    
    
    **Input:** [[3,1],[1,1],[0,1],[2,1],[3,3],[3,2],[0,2],[2,3]]
    **Output:** 2.00000
    **Explanation:** The minimum area rectangle occurs at [2,1],[2,3],[3,3],[3,1], with an area of 2.
    



 **Note:**

  1. `1 <= points.length <= 50`
  2. `0 <= points[i][0] <= 40000`
  3. `0 <= points[i][1] <= 40000`
  4. All points are distinct.
  5. Answers within `10^-5` of the actual value will be accepted as correct.

### Solution 1
  1. Two diagonals of a rectangle bisect each other, and are of equal length.
  2. The map's key is String including diagonal length and coordinate of the diagonal center; map's value is the index of two points forming the diagonal.

    
    
    class Solution {
        public double minAreaFreeRect(int[][] points) {
            int len = points.length;
            double res = Double.MAX_VALUE;
            if (len < 4) return 0.0;
            Map<String, List<int[]>> map = new HashMap<>(); // int[] is the index of two points forming the diagonal
            for (int i = 0; i < len; i++) {
                for (int j = i + 1; j < len; j++) {
                    long dis = (points[i][0] - points[j][0]) * (points[i][0] - points[j][0]) + (points[i][1] - points[j][1]) * (points[i][1] - points[j][1]);
                    double centerX = (double)(points[j][0] + points[i][0])/2; // centerX and centerY is the coordinate of the diagonal center
                    double centerY = (double)(points[j][1] + points[i][1])/2;
                    String key = "" + dis + "+" + centerX + "+" + centerY; // key includes the length of the diagonal and the coordinate of the diagonal center
                    if (map.get(key) == null) map.put(key, new ArrayList<int[]>());
                    map.get(key).add(new int[]{i,j});
                }
            }
            for (String key : map.keySet()) {
                if (map.get(key).size() > 1) {  
                    List<int[]> list = map.get(key);
                    for (int i = 0; i < list.size(); i++) { // there could be multiple rectangles inside
                        for (int j = i + 1; j < list.size(); j++) {
                            int p1 = list.get(i)[0]; // p1, p2 and p3 are the three vertices of a rectangle
                            int p2 = list.get(j)[0];
                            int p3 = list.get(j)[1];
                            // len1 and len2 are the length of the sides of a rectangle
                            double len1 = Math.sqrt((points[p1][0] - points[p2][0]) * (points[p1][0] - points[p2][0]) +  (points[p1][1] - points[p2][1]) * (points[p1][1] - points[p2][1])); 
                            double len2 = Math.sqrt((points[p1][0] - points[p3][0]) * (points[p1][0] - points[p3][0]) +  (points[p1][1] - points[p3][1]) * (points[p1][1] - points[p3][1]));
                            double area = len1 * len2; 
                            res = Math.min(res, area);
                        }
                    }
                }
            }
            return res == Double.MAX_VALUE ?  0.0 : res;
        }
    }
    


### Solution 2
Diagonals of a rectangle are intersecting in the middle point. For any pair of
points (our first potential diagonal) `{xi, yi} - {xj, yj}`, we find and track
the middle point `{x0, y0}`. Any other pair of points with the same middle
point is the second potential diagonal.

I am using the hash map for O(1) lookups, and I compressing `{x0, y0}`
coordinates into a single integer for the simplicity.  
![image](https://assets.leetcode.com/users/votrubac/image_1546066215.png)  
After we processed all pair of points this way, we analyze potential diagonals
for each middle point. We form two sides of the potential rectangle as `{x11,
y11} - {x21, y21}` and `{x11, y11} - {x22, y22}`, and check if they are
orthogonal (perpendicular). If so, we calculate the area and track the
smallest one.

    
    
    size_t d2(int x1, int y1, int x2, int y2) { 
        return (x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2);
    }
    double minAreaFreeRect(vector<vector<int>>& ps, size_t res = 0) {
      unordered_map<size_t, vector<vector<int>>> m;
      for (auto i = 0; i < ps.size(); ++i)
        for (auto j = i + 1; j < ps.size(); ++j) {
          auto center = ((size_t)(ps[i][0] + ps[j][0]) << 16) + ps[i][1] + ps[j][1];
          m[center].push_back({ ps[i][0], ps[i][1], ps[j][0], ps[j][1] });
        }
      for (auto it = begin(m); it != end(m); ++it)
        for (auto i = 0; i < it->second.size(); ++i)
          for (auto j = i + 1; j < it->second.size(); ++j) {
            auto &p1 = it->second[i], &p2 = it->second[j];
            if ((p1[0] - p2[0]) * (p1[0] - p2[2]) + (p1[1] - p2[1]) * (p1[1] - p2[3]) == 0) {
              auto area = d2(p1[0], p1[1], p2[0], p2[1]) * d2(p1[0], p1[1], p2[2], p2[3]);
              if (res == 0 || res > area) res = area;
            }
          }
      return sqrt(res);
    }
    

As an alternative, we can track both the center and length of the diagonals in
the hash map (only diagonals with the same length can form a rectangle). That
way we can potentially reduce the number of diagonals to compare. We also do
not need to do the orthogonality check anymore.

Thanks [@kaiwensun](https://leetcode.com/kaiwensun/) for this interesting
idea.

    
    
    size_t d2(int x1, int y1, int x2, int y2) {
      return (x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2);
    }
    double minAreaFreeRect(vector<vector<int>>& ps, size_t res = 0) {
      unordered_map<size_t, unordered_map<size_t, vector<vector<int>>>> m;
      for (auto i = 0; i < ps.size(); ++i)
        for (auto j = i + 1; j < ps.size(); ++j) {
          auto center = ((size_t)(ps[i][0] + ps[j][0]) << 16) + ps[i][1] + ps[j][1];
          auto len = d2(ps[i][0], ps[i][1], ps[j][0], ps[j][1]);
          m[center][len].push_back({ ps[i][0], ps[i][1], ps[j][0], ps[j][1] });
        }
      for (auto it_c = begin(m); it_c != end(m); ++it_c)
        for (auto it_l = begin(it_c->second); it_l != end(it_c->second); ++it_l)
          for (auto i = 0; i < it_l->second.size(); ++i)
            for (auto j = i + 1; j < it_l->second.size(); ++j) {
              auto &p1 = it_l->second[i], &p2 = it_l->second[j];
              auto area = d2(p1[0], p1[1], p2[0], p2[1]) * d2(p1[0], p1[1], p2[2], p2[3]);
              if (res == 0 || res > area) res = area;
            }
      return sqrt(res);
    }
    


### Solution 3
  * Dot product of two sides in a rectangle should be zero because a . b = |a| |b| cos(90)
  * If we can extend p3 by the same margin delta(p2 - p1), we can have the fourth point p4.
    * x4 = x3 + (x2 - x1)
    * y4 = y3 + (y2 - y1)
  * If p4 in points, calculate area.

    
    
    class Solution:
        def minAreaFreeRect(self, points):
            mn, st, n = float('inf'), {(x, y) for x, y in points}, len(points) 
            for i in range(n):
                x1, y1 = points[i]
                for j in range(i + 1, n):
                    x2, y2 = points[j]
                    for k in range(j + 1, n):
                        x3, y3 = points[k]
                        if not (x3 - x1) * (x2 - x1) + (y3 - y1) * (y2 - y1) and (x3 + (x2 - x1), y3 + (y2 - y1)) in st:
                            mn = min(mn, ((x2 - x1) ** 2 + (y2 - y1) ** 2) ** 0.5 * ((x3 - x1) ** 2 + (y3 - y1) ** 2) ** 0.5)
            return mn if mn < float("inf") else 0
    



