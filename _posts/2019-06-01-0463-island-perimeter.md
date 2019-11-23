---
layout: post
title: 463. Island Perimeter
---
### Question
You are given a map in form of a two-dimensional integer grid where 1
represents land and 0 represents water.

Grid cells are connected horizontally/vertically (not diagonally). The grid is
completely surrounded by water, and there is exactly one island (i.e., one or
more connected land cells).

The island doesn't have "lakes" (water inside that isn't connected to the
water around the island). One cell is a square with side length 1. The grid is
rectangular, width and height don't exceed 100. Determine the perimeter of the
island.



 **Example:**

    
    
     **Input:**
    [[0,1,0,0],
     [1,1,1,0],
     [0,1,0,0],
     [1,1,0,0]]
    
    **Output:** 16
    
    **Explanation:** The perimeter is the 16 yellow stripes in the image below:
    
    ![](https://assets.leetcode.com/uploads/2018/10/12/island.png)
    

### Solution 1
  1. loop over the matrix and count the number of islands;
  2. if the current dot is an island, count if it has any right neighbour or down neighbour;
  3. the result is islands * 4 - neighbours * 2

    
    
    public class Solution {
        public int islandPerimeter(int[][] grid) {
            int islands = 0, neighbours = 0;
    
            for (int i = 0; i < grid.length; i++) {
                for (int j = 0; j < grid[i].length; j++) {
                    if (grid[i][j] == 1) {
                        islands++; // count islands
                        if (i < grid.length - 1 && grid[i + 1][j] == 1) neighbours++; // count down neighbours
                        if (j < grid[i].length - 1 && grid[i][j + 1] == 1) neighbours++; // count right neighbours
                    }
                }
            }
    
            return islands * 4 - neighbours * 2;
        }
    }
    


### Solution 2
Since there are no lakes, every pair of neighbour cells with different values
is part of the perimeter (more precisely, the edge between them is). So just
count the differing pairs, both horizontally and vertically (for the latter I
simply transpose the grid).

    
    
    def islandPerimeter(self, grid):
        return sum(sum(map(operator.ne, [0] + row, row + [0]))
                   for row in grid + map(list, zip(*grid)))


### Solution 3
    
    
    public static int islandPerimeter(int[][] grid) {
            if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
            int result = 0;
            for (int i = 0; i < grid.length; i++) {
                for (int j = 0; j < grid[0].length; j++) {
                    if (grid[i][j] == 1) {
                        result += 4;
                        if (i > 0 && grid[i-1][j] == 1) result -= 2;
                        if (j > 0 && grid[i][j-1] == 1) result -= 2;
                    }
                }
            }
            return result;
        }
    



