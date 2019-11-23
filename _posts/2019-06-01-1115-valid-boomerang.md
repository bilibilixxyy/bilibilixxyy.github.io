---
layout: post
title: 1115. Valid Boomerang
---
### Question
A _boomerang_ is a set of 3 points that are all distinct and **not** in a
straight line.

Given a list of three points in the plane, return whether these points are a
boomerang.



 **Example 1:**

    
    
     **Input:** [[1,1],[2,3],[3,2]]
    **Output:** true
    

**Example 2:**

    
    
    **Input:** [[1,1],[2,2],[3,3]]
    **Output:** false



 **Note:**

  1. `points.length == 3`
  2. `points[i].length == 2`
  3. `0 <= points[i][j] <= 100`

### Solution 1
Assuming three points are A, B, C.

The first idea is that, calculate the area of ABC.  
We can reuse the conclusion and prove in [812\. Largest Triangle
Area](https://leetcode.com/problems/largest-triangle-
area/discuss/122711/C++JavaPython-Solution-with-Explanation-and-Prove)

The other idea is to calculate the slope of AB and AC.  
`K_AB = (p[0][0] - p[1][0]) / (p[0][1] - p[1][1])`  
`K_AC = (p[0][0] - p[2][0]) / (p[0][1] - p[2][1])`

We check if `K_AB != K_AC`, instead of calculate a fraction.

Time `O(1)` Space `O(1)`

  

 **Java:**

    
    
         public boolean isBoomerang(int[][] p) {
            return (p[0][0] - p[1][0]) * (p[0][1] - p[2][1]) != (p[0][0] - p[2][0]) * (p[0][1] - p[1][1]);
        }
    

**C++:**

    
    
         bool isBoomerang(vector<vector<int>>& p) {
            return (p[0][0] - p[1][0]) * (p[0][1] - p[2][1]) != (p[0][0] - p[2][0]) * (p[0][1] - p[1][1]);
        }
    

**Python:**

    
    
        def isBoomerang(self, p):
             return (p[0][0] - p[1][0]) * (p[0][1] - p[2][1]) != (p[0][0] - p[2][0]) * (p[0][1] - p[1][1])
    


### Solution 2
# Intuition

In other words, we need to return true if the triangle area is not zero.

For the detailed explanation, see the comment by
[EOAndersson](https://leetcode.com/eoandersson/) below.

# Solution

Calculate the area of the triangle: `x1 * (y2 - y3) + x2 * (y3 - y1) + x3 *
(y1 - y2)` and compare it to zero.

    
    
    bool isBoomerang(vector<vector<int>>& p) {
      return p[0][0] * (p[1][1] - p[2][1]) + p[1][0] * (p[2][1] - p[0][1]) + p[2][0] * (p[0][1] - p[1][1]) != 0;
    }
    


### Solution 3
    
    
    class Solution {
    public:
    	bool isBoomerang(vector<vector<int>>& points) {
    		if(points.size() != 3)
    			return false;
    
    		// formula for slope = (y2 - y1)/(x2 - x1)
    		// assume the slope of the line using points 0 and 1 (a/b) and that of using points 0  and 2 (c / d)
    		// avoid dividing, just compare a/b and c/d if(a * d > c * b)==> first fraction is greater otherwise second
    		// if slopes of the both lines (p0--p1 and p0--p2) are equal then three points form a straight line (cannot form a triangle)
    		return ((points[1][1] - points[0][1]) * (points[2][0] - points[0][0])) != ((points[2][1] - points[0][1]) * (points[1][0] - points[0][0]));
    	}
    };



