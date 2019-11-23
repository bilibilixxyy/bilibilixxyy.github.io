---
layout: post
title: 919. Projection Area of 3D Shapes
---
### Question
On a `N * N` grid, we place some `1 * 1 * 1 `cubes that are axis-aligned with
the x, y, and z axes.

Each value `v = grid[i][j]` represents a tower of `v` cubes placed on top of
grid cell `(i, j)`.

Now we view the  _projection_  of these cubes onto the xy, yz, and zx planes.

A projection is like a shadow, that maps our 3 dimensional figure to a 2
dimensional plane.

Here, we are viewing the "shadow" when looking at the cubes from the top, the
front, and the side.

Return the total area of all three projections.



 **Example 1:**

    
    
     **Input:** [[2]]
    **Output:** 5
    

**Example 2:**

    
    
    **Input:** [[1,2],[3,4]]
    **Output:** 17
    **Explanation:**
    Here are the three projections ("shadows") of the shape made with each axis-aligned plane.
    ![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/08/02/shadow.png)
    

**Example 3:**

    
    
    **Input:** [[1,0],[0,2]]
    **Output:** 8
    

**Example 4:**

    
    
    **Input:** [[1,1,1],[1,0,1],[1,1,1]]
    **Output:** 14
    

**Example 5:**

    
    
    **Input:** [[2,2,2],[2,1,2],[2,2,2]]
    **Output:** 21
    



 **Note:**

  * `1 <= grid.length = grid[0].length <= 50`
  * `0 <= grid[i][j] <= 50`

### Solution 1
The problem description is very ambiguous and confusing. I actually spent 90%
of my time to understand what does the input imply when I tried to solve this
problem. If you want to make problems complicated to solve, you should
increase the complexity by making the algorithm itself hard to implement or
think of, not making bad wordings and trying to confuse people. If I met this
type of problem description in an actual interview, I might not even proceed
with it.


### Solution 2
can any body explain me the means of the first input?


### Solution 3
 **Update:**  
(If you have no difficulty to understand the problem description, just skip
the update part)

It appears that lots of people complain that they do NOT understand the
problem description. In fact, I did NOT either at first glance.

In my view, the description omits a key point: **each unit (1 X 1) grid cell
referred by grid[i][j] is placed on x-y plane such that its bottom left corner
is on (i, j),** though we can guess it from the picture and context.

In addition, if the input changed into a matrix, it could be easier to
establish the relation of the input and the geometric shape.

Let's use the input: [[1,2],[3,4]] as our case, and its corresponding matrix
is as follows:  
[[1, 2],  
[3, 4]]  
In more details, please see the left 2 pictures below:

grid[ **0** ][ **0** ] = **1:** There is **1** unit of cube placed on a unit
cell with bottom left corner at **(0, 0)** , and the other 3 corners, in
clockwise order, are at (0, 1), (1,1), (1,0).

grid[ **0** ][ **1** ] = **2** : There are **2** units of cube placed on a
unit cell with bottom left corner at **(0, 1)** , and the other 3 corners, in
clockwise order, are at (0, 2), (1,2), (1,1).

grid[ **1** ][ **1** ] = **3** : There are **3** units of cube placed on a
unit cell with bottom left corner at **(1, 1)** , and the other 3 corners, in
clockwise order, are at (1, 2), (2,2), (2,1).

grid[ **1** ][ **0** ] = **4:** There are **4** units of cube placed on a unit
cell with bottom left corner at **(1, 0)** , and the other 3 corners, in
clockwise order, are at (1, 1), (2,1), (2,0).

![image](https://s3-lc-
upload.s3.amazonaws.com/users/rock/image_1533633857.png)

Hope the above can clarify possible confusion about understanding the problem.

 **end of update.**

Note: for a N * N grid, i & j limits are both **grid.length**.

  1. get max out of grid **[j]** [i] (j = 0 ~ grid.length), the projection in x direction (on y-z plane)
  2. get max out of grid[i] **[j]** (j = 0 ~ grid.length), the projection in y direction (on x-z plane)

    
    
         public int projectionArea(int[][] grid) {
            int z = 0, x = 0, y = 0;
            for (int i = 0; i < grid.length; ++i) {
                int mx = 0, my = 0; // max of x & y values.
                for (int j = 0; j < grid.length; ++j) {
                    mx = Math.max(mx, grid[j][i]); // see above 1
                    my = Math.max(my, grid[i][j]); // see above 2
                    if (grid[i][j] > 0) { ++z; } // only the cell with positive value has a projection area of size 1 in z direction (on x-y plane). 
                }
                x += mx; y += my; // x & y projection accumulation.
            }
            return x + y + z;
        }
    



