---
layout: post
title: 921. Spiral Matrix III
---
### Question
On a 2 dimensional grid with `R` rows and `C` columns, we start at `(r0, c0)`
facing east.

Here, the north-west corner of the grid is at the first row and column, and
the south-east corner of the grid is at the last row and column.

Now, we walk in a clockwise spiral shape to visit every position in this grid.

Whenever we would move outside the boundary of the grid, we continue our walk
outside the grid (but may return to the grid boundary later.)

Eventually, we reach all `R * C` spaces of the grid.

Return a list of coordinates representing the positions of the grid in the
order they were visited.



 **Example 1:**

    
    
     **Input:** R = 1, C = 4, r0 = 0, c0 = 0
    **Output:** [[0,0],[0,1],[0,2],[0,3]]
    
    ![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/08/24/example_1.png)
    



**Example 2:**

    
    
    **Input:** R = 5, C = 6, r0 = 1, c0 = 4
    **Output:** [[1,4],[1,5],[2,5],[2,4],[2,3],[1,3],[0,3],[0,4],[0,5],[3,5],[3,4],[3,3],[3,2],[2,2],[1,2],[0,2],[4,5],[4,4],[4,3],[4,2],[4,1],[3,1],[2,1],[1,1],[0,1],[4,0],[3,0],[2,0],[1,0],[0,0]]
    
    ![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/08/24/example_2.png)
    



 **Note:**

  1. `1 <= R <= 100`
  2. `1 <= C <= 100`
  3. `0 <= r0 < R`
  4. `0 <= c0 < C`

### Solution 1
 **Intuition** :  
Take steps one by one.  
If the location is inside of grid, add it to `res`.  
But how to simulate the path?

It seems to be annoying, but if we oberserve the path:

move right `1` step, turn right  
move down `1` step, turn right  
move left `2` steps, turn right  
move top `2` steps, turn right,  
move right `3` steps, turn right  
move down `3` steps, turn right  
move left `4` steps, turn right  
move top `4` steps, turn right,

we can find the sequence of steps: 1,1,2,2,3,3,4,4,5,5....

So there are two thing to figure out:

  1. how to generate sequence 1,1,2,2,3,3,4,4,5,5
  2. how to turn right?

 **Generate sequence 1,1,2,2,3,3,4,4,5,5**  
Let `n` be index of this sequence.  
Then `A0 = 1`, `A1 = 1`, `A2 = 2` ......  
We can include that `An = n / 2 + 1`

 **How to turn right?**  
By cross product:  
Assume current direction is (x, y) in plane, which is (x, y, 0) in space.  
Then the direction after turn right (x, y, 0) × (0, 0, 1) = (y, -x, 0)  
Translate to code: `tmp = x; x = y; y = -tmp;`

By arrays of arrays:  
The directions order is (0,1),(1,0),(0,-1),(-1,0), then repeat.  
Just define a variable.

 **Time Complexity** :  
O(max(M,N) ^ 2)

 **C++:**

    
    
        vector<vector< int>> spiralMatrixIII(int R, int C, int r, int c) {
            vector<vector<int>> res = {{r, c}};
            int x = 0, y = 1, tmp;
            for (int n = 0; res.size() < R * C; n++) {
                for (int i = 0; i < n / 2 + 1; i++) {
                    r += x, c += y;
                    if (0 <= r && r < R && 0 <= c && c < C)
                        res.push_back({r, c});
                }
                tmp = x, x = y, y = -tmp;
            }
            return res;
        }
    

**Java:**

    
    
         public int[][] spiralMatrixIII(int R, int C, int r, int c) {
            int[][] res = new int[R * C][2];
            res[0] = new int[] {r, c};
            int x = 0, y = 1, n = 0, i = 0, tmp, j = 1;
            while (j < R * C) {
                r += x; c += y; i++;
                if (0 <= r && r < R && 0 <= c && c < C)
                    res[j++] = new int[] {r, c};
                if (i == n / 2 + 1) {
                    i = 0; n++; tmp = x; x = y; y = -tmp;
                }
            }
            return res;
        }
    

**Python:**

    
    
        def spiralMatrixIII(self, R, C, r, c):
            res = [[r, c]]
            x, y, n, i =  0, 1, 0, 0
            while len(res) < R * C:
                r, c, i = r + x, c + y, i + 1
                if 0 <= r < R and 0 <= c < C:
                    res.append([r, c])
                if i == n / 2 + 1:
                    x, y, n, i = y, -x, n + 1, 0
            return res
    


### Solution 2
    
    
        public int[][] spiralMatrixIII(int R, int C, int r0, int c0) {
            int[][] dirt = new int[][]{{0, 1}, {1, 0}, {0, -1}, {-1, 0}}; // east, south, west, north
            List<int[]> res = new ArrayList<>();
            int len = 0, d = 0; // move <len> steps in the <d> direction
            res.add(new int[]{r0, c0});
            while (res.size() < R * C) {
                if (d == 0 || d == 2) len++; // when move east or west, the length of path need plus 1 
                for (int i = 0; i < len; i++) {
                    r0 += dirt[d][0];
                    c0 += dirt[d][1];
                    if (r0 >= 0 && r0 < R && c0 >= 0 && c0 < C) // check valid
                        res.add(new int[]{r0, c0});
                }
                d = (d + 1) % 4; // turn to next direction
            }
            return res.toArray(new int[R * C][2]);
        }
    


### Solution 3
Put all valid coordinates to a list `res`

Sort all coordinates by the following rule:

  0. Change coordinates to a relative coordinates to center.

  1. Sort ascending by the distance to the center `max(abs(x), abs(y))`  
If `max(abs(x), abs(y)) == 0`, it's the center.  
If `max(abs(x), abs(y)) == 1`, it's in the first layer around the center

  2. Sort descending by the angle to the center `max(abs(x), abs(y))`

 **Python:**

    
    
        def spiralMatrixIII( self, R, C, r, c):
            def key((x, y)):
                x, y = x - r, y - c
                return (max(abs(x), abs(y)), -((math.atan2(-1, 1) - math.atan2(x, y)) % (math.pi * 2)))
            return sorted([(i, j) for i in xrange(R) for j in xrange(C)], key=key)
    



