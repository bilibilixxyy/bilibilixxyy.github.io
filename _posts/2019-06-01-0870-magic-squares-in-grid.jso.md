---
layout: post
title: 870. Magic Squares In Grid
---
### Question
A 3 x 3 magic square is a 3 x 3 grid filled with distinct numbers **from 1 to
9** such that each row, column, and both diagonals all have the same sum.

Given an `grid` of integers, how many 3 x 3 "magic square" subgrids are there?
(Each subgrid is contiguous).



 **Example 1:**

    
    
     **Input:** [[4,3,8,4],
            [9,5,1,9],
            [2,7,6,2]]
    **Output:** 1
    **Explanation:**
    The following subgrid is a 3 x 3 magic square:
    438
    951
    276
    
    while this one is not:
    384
    519
    762
    
    In total, there is only one magic square inside the given grid.
    

**Note:**

  1. `1 <= grid.length <= 10`
  2. `1 <= grid[0].length <= 10`
  3. `0 <= grid[i][j] <= 15`

### Solution 1
The intuition is brute force, don't need any tricky.  
One thing to pay attention: A 3 x 3 magic square is a 3 x 3 grid filled with
distinct numbers from **1 to 9**.  
I just find many sumbmission ignoring this condition.

Here I just want share two observatons with this **1-9** condition:

Assume a magic square:  
a1,a2,a3  
a4,a5,a6  
a7,a8,a9

`a2 + a5 + a8 = 15`  
`a4 + a5 + a6 = 15`  
`a1 + a5 + a9 = 15`  
`a3 + a5 + a7 = 15`

Accumulate all, then we have:  
`sum(ai) + 3 * a5 = 60`  
`3 * a5 = 15`  
`a5 = 5`

The center of magic square must be 5.

Another observation for other 8 numbers:  
The even must be in the corner, and the odd must be on the edge.  
And it must be in a order like "43816729" （clockwise or anticlockwise)

    
    
        def numMagicSquaresInside(self, g):
            def isMagic(i, j):
                s = "".join(str(g[i + x / 3][j + x % 3]) for x in [0, 1, 2, 5, 8, 7, 6, 3])
                return g[i][j] % 2 == 0 and (s in "43816729" * 2 or s in "43816729"[::-1] * 2)
            return sum(isMagic(i, j) for i in range(len(g) - 2) for j in range(len(g[0]) - 2) if g[i + 1][j + 1] == 5)
    


### Solution 2
Problem said it is distinct numbers from 1 to 9, I think it indicate grid
contain only 1 to 9 .  
When I failed in test case [[10, 3, 5], [1, 6. 11], [7, 9, 2]], I got confused
and tired more than 10 times.  
0 <= grid[i][j] <= 15 should move to the start of the problem and highlight
instead of pub it in note.


### Solution 3
The center of the square must be 5.  
And I judge whether **1.** the square contains number 1~9. **2.** the four
sides and two diagonal lines all equal to 15.  
Ugly and straightforward solution.

    
    
    public int numMagicSquaresInside(int[][] grid) {
            int cnt=0;
            for(int i=0;i<=grid.length-3;i++)
                for(int j=0;j<=grid[0].length-3;j++)
                    if(helper(i,j,grid)) cnt++;
                
            return cnt;
        }  
        
        private boolean helper(int x,int y,int[][] grid){
            if(grid[x+1][y+1]!=5) return false;
            
            int[] valid=new int[16];
            
            for(int i=x;i<=x+2;i++)
                for(int j=y;j<=y+2;j++)
                    valid[grid[i][j]]++;
                
            for (int v = 1; v <= 9; v++)
                if (valid[v] != 1) return false;
            
            if((grid[x][y]+grid[x][y+1]+grid[x][y+2])!=15)         return false;
            if((grid[x][y]+grid[x+1][y+1]+grid[x+2][y+2])!=15)     return false;
            if((grid[x][y]+grid[x+1][y]+grid[x+2][y])!=15)         return false;
            if((grid[x+2][y]+grid[x+2][y+1]+grid[x+2][y+2])!=15)   return false;
            if((grid[x][y+2]+grid[x+1][y+2]+grid[x+2][y+2])!=15)   return false;
            if((grid[x][y+2]+grid[x+1][y+1]+grid[x+2][y])!=15)     return false;
            return true;
        }
    



