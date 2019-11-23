---
layout: post
title: 928. Surface Area of 3D Shapes
---
### Question
On a `N * N` grid, we place some `1 * 1 * 1 `cubes.

Each value `v = grid[i][j]` represents a tower of `v` cubes placed on top of
grid cell `(i, j)`.

Return the total surface area of the resulting shapes.



 **Example 1:**

    
    
     **Input:** [[2]]
    **Output:** 10
    

**Example 2:**

    
    
    **Input:** [[1,2],[3,4]]
    **Output:** 34
    

**Example 3:**

    
    
    **Input:** [[1,0],[0,2]]
    **Output:** 16
    

**Example 4:**

    
    
    **Input:** [[1,1,1],[1,0,1],[1,1,1]]
    **Output:** 32
    

**Example 5:**

    
    
    **Input:** [[2,2,2],[2,1,2],[2,2,2]]
    **Output:** 46
    



 **Note:**

  * `1 <= N <= 50`
  * `0 <= grid[i][j] <= 50`

### Solution 1
 **Intuition** :  
For each tower, its surface area is `4 * v + 2`  
However, 2 adjacent tower will hide the area of connected part.  
The hidden part is `min(v1, v2)` and we need just minus this area * 2

 **Time Complexity** :  
O(N^2)

 **C++:**

    
    
        int surfaceArea(vector<vector<int>> grid) {
            int res =  0, n = grid.size();
            for (int i = 0; i < n; ++i) {
                for (int j = 0; j < n; ++j) {
                    if (grid[i][j]) res += grid[i][j] * 4 + 2;
                    if (i) res -= min(grid[i][j], grid[i - 1][j]) * 2;
                    if (j) res -= min(grid[i][j], grid[i][j - 1]) * 2;
                }
            }
            return res;
        }
    

**Java:**

    
    
        public int surfaceArea(int[][] grid) {
            int res =  0, n = grid.length;
            for (int i = 0; i < n; ++i) {
                for (int j = 0; j < n; ++j) {
                    if (grid[i][j] > 0) res += grid[i][j] * 4 + 2;
                    if (i > 0) res -= Math.min(grid[i][j], grid[i - 1][j]) * 2;
                    if (j > 0) res -= Math.min(grid[i][j], grid[i][j - 1]) * 2;
                }
            }
            return res;
        }
    

**Python:**

    
    
        def surfaceArea(self, grid):
            n, res = len(grid),  0
            for i in range(n):
                for j in range(n):
                    if grid[i][j]: res += 2 + grid[i][j] * 4
                    if i: res -= min(grid[i][j], grid[i - 1][j]) * 2
                    if j: res -= min(grid[i][j], grid[i][j - 1]) * 2
            return res
    

**1-line Python:**

    
    
        def surfaceArea(self,  grid):
            return sum(v * 4 + 2 for row in grid for v in row if v) - sum(min(a, b) * 2 for row in grid + zip(*grid) for a, b in zip(row, row[1:]))
    


### Solution 2
I think this problem should be labeled as Medium. (This is under the
assumption that problems are manually labeled.) . I took a long time to solve
this problem. I may have been misled by initial intuition which was to find
the maximum of each row and the maximum of each column. That gives me _part_
of the surface area visible from x and y. I developed a complex way of
counting the surface area not visible from x and y. The surface area of z is
just the count of nonzero values * 2.

I have read others' solution and, in hindsight, they are much more straight
forward. Essentially, compute the surface area of each grid but substract the
overlapping areas.


### Solution 3
  1. Surface area of a cube = 6*(side)^2 (In this problem side = 1)
  2. If the value in grid[i][j] > 1 i.e. top/bottom will overlap; subtract 2
  3. check for previous row and previous column for any overlapping too

    
    
    class Solution {
        public int surfaceArea(int[][] grid) {
            int res = 0;
            
            if(grid == null || grid.length == 0 || grid[0].length==0) {
                return res;
            }
            
            for(int i=0;i<grid.length;i++) {
                for(int j=0;j<grid[i].length;j++) {
                    res+=grid[i][j]*6;
                    
                    if(grid[i][j] > 1) {
                        res-=(grid[i][j]-1)*2;
                    }
                    
                    if(i>=0 && i-1>=0) {
                        res-=Math.min(grid[i][j], grid[i-1][j])*2;
                    }
                    
                    if(j>=0 && j-1>=0) {
                        res-=Math.min(grid[i][j], grid[i][j-1])*2;
                    }
                }
            }
            
            return res;
        }
    }
    



