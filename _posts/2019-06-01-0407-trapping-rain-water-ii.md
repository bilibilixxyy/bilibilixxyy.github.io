---
layout: post
title: 407. Trapping Rain Water II
---
### Question
Given an `m x n` matrix of positive integers representing the height of each
unit cell in a 2D elevation map, compute the volume of water it is able to
trap after raining.



 **Note:**

Both _m_ and _n_ are less than 110. The height of each unit cell is greater
than 0 and is less than 20,000.



 **Example:**

    
    
    Given the following 3x6 height map:
    [
      [1,4,3,1,3,2],
      [3,2,1,3,2,4],
      [2,3,3,2,3,1]
    ]
    
    Return 4.
    

![](https://assets.leetcode.com/uploads/2018/10/13/rainwater_empty.png)

The above image represents the elevation map
`[[1,4,3,1,3,2],[3,2,1,3,2,4],[2,3,3,2,3,1]]` before the rain.



![](https://assets.leetcode.com/uploads/2018/10/13/rainwater_fill.png)

After the rain, water is trapped between the blocks. The total volume of water
trapped is 4.

### Solution 1
A visualization of the most popular priority queue algorithm.  
<https://www.youtube.com/watch?v=cJayBq38VYw>


### Solution 2
Source code from:  
[https://github.com/shawnfan/LintCode/blob/master/Java/Trapping Rain Water
II.java](https://github.com/shawnfan/LintCode/blob/master/Java/Trapping%20Rain%20Water%20II.java)

    
    
    public class Solution {
    
        public class Cell {
            int row;
            int col;
            int height;
            public Cell(int row, int col, int height) {
                this.row = row;
                this.col = col;
                this.height = height;
            }
        }
    
        public int trapRainWater(int[][] heights) {
            if (heights == null || heights.length == 0 || heights[0].length == 0)
                return 0;
    
            PriorityQueue<Cell> queue = new PriorityQueue<>(1, new Comparator<Cell>(){
                public int compare(Cell a, Cell b) {
                    return a.height - b.height;
                }
            });
            
            int m = heights.length;
            int n = heights[0].length;
            boolean[][] visited = new boolean[m][n];
    
            // Initially, add all the Cells which are on borders to the queue.
            for (int i = 0; i < m; i++) {
                visited[i][0] = true;
                visited[i][n - 1] = true;
                queue.offer(new Cell(i, 0, heights[i][0]));
                queue.offer(new Cell(i, n - 1, heights[i][n - 1]));
            }
    
            for (int i = 0; i < n; i++) {
                visited[0][i] = true;
                visited[m - 1][i] = true;
                queue.offer(new Cell(0, i, heights[0][i]));
                queue.offer(new Cell(m - 1, i, heights[m - 1][i]));
            }
    
            // from the borders, pick the shortest cell visited and check its neighbors:
            // if the neighbor is shorter, collect the water it can trap and update its height as its height plus the water trapped
           // add all its neighbors to the queue.
            int[][] dirs = new int[][]{{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
            int res = 0;
            while (!queue.isEmpty()) {
                Cell cell = queue.poll();
                for (int[] dir : dirs) {
                    int row = cell.row + dir[0];
                    int col = cell.col + dir[1];
                    if (row >= 0 && row < m && col >= 0 && col < n && !visited[row][col]) {
                        visited[row][col] = true;
                        res += Math.max(0, cell.height - heights[row][col]);
                        queue.offer(new Cell(row, col, Math.max(heights[row][col], cell.height)));
                    }
                }
            }
            
            return res;
        }
    }
    


### Solution 3
First let's recall the solution to the 1-D "Trapping Rain Water" problem:

    
    
    public int trap(int[] height) {
        int res = 0, l = 0, r = height.length - 1;
            
        while (l < r) {
            if (height[l] <= height[r]) {
                if (l + 1 < r) {
                    res += Math.max(0, height[l] - height[l + 1]);
                    height[l + 1] = Math.max(height[l], height[l + 1]);
                }
                    
                l++;
                    
            } else {
                if (l < r - 1) {
                    res += Math.max(0, height[r] - height[r - 1]);
                    height[r - 1] = Math.max(height[r], height[r - 1]);
                }
                    
                r--;
            }
        }
            
        return res;
    }
    

The code above is not optimized but it does show the most prominent features
of the solution to the "Trapping Rain Water" problem:

  1. **Rain water "container"** : To hold the rain water, we need some sort of "container", which will be defined by its boundary.

  2. [**Liebig's law of the minimum**](https://en.wikipedia.org/wiki/Liebig%27s_law_of_the_minimum): The maximum height of water for current container is determined by the lowest part of its boundary.

  3. **Boundary replacement** : The above "law of the minimum" applies only to neighbors of the lowest part of current boundary. After that the lowest part will be replaced by its neighbors. The height of the new parts of the boundary will be the larger one of the old boundary height and the height from the elevation map.

In the 1-D case, the boundary of the container contains only two points, i.e.,
the left and right edges (l and r in the above code) from the elevation map.
We then apply the "law of the minimum" to find the minimum height of the
boundary (corresponding to the two cases involving comparing height[l] and
height[r]). After that we replace the old part of the boundary with its
neighbors and continue until the container shrinks to a point.

Now let's turn to the 2-D case.

First, what will the boundary of the container look like? Apparently it will
no longer just be two edges, but instead, an enclosed area. Since our input is
a matrix, the initial boundary will be the four edges of the matrix, which
forms a rectangle.

Second, how do we apply the "law of the minimum"? This involves finding the
minimum of the boundary, which is no longer as straightforward as in the 1-D
case due to the rectangular boundary. Fortunately we have a data structure
called "PriorityQueue" which can help find the minimum in log(queue_size)
time.

Lastly, how do we replace the boundaries? Keep in mind we have 2-D elevation
map now so each cell can have at most four neighbors. Also there are
possibilities that one cell is neighbor of multiple cells on the boundary so
we need to keep track of which cell has been visited to avoid repetition.

The following is the java solution according to the above analyses:

    
    
    int[][] dirs = new int[][] {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
        
    public int trapRainWater(int[][] heightMap) {
        int m = heightMap.length;
        int n = (m == 0 ? 0 : heightMap[0].length);
        int res = 0;
            
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[2] - b[2]);
        boolean[][] visited = new boolean[m][n];
            
        for (int i = 0; i < m; i++) {
            pq.offer(new int[] {i, 0, heightMap[i][0]});
            pq.offer(new int[] {i, n - 1, heightMap[i][n - 1]});
            visited[i][0] = visited[i][n - 1] = true;
        }
            
        for (int j = 1; j < n - 1; j++) {
            pq.offer(new int[] {0, j, heightMap[0][j]});
            pq.offer(new int[] {m - 1, j, heightMap[m - 1][j]});
            visited[0][j] = visited[m - 1][j] = true;
        }
            
        while (!pq.isEmpty()) {
            int[] cell = pq.poll();
            	
            for (int[] d : dirs) {
                int i = cell[0] + d[0], j = cell[1] + d[1];
                if (i < 0 || i >= m || j < 0 || j >= n || visited[i][j]) continue;
                res += Math.max(0, cell[2] - heightMap[i][j]);
                pq.offer(new int[] {i, j, Math.max(heightMap[i][j], cell[2])});
                visited[i][j] = true;
            }
        }
            
        return res;
    }
    

The element in the PriorityQueue is an array with three elements encoding the
row number, column number and the height of the cell on the boundary. We first
build the initial boundary by enqueuing all cells on the outer edges of the
input matrix and mark them as visited. We then apply the "law of the minimum"
by polling the cell with minimum height on the boundary. After obtaining the
water contained for each of its neighbor, we replace it with its neighbor and
form a new boundary. Again we continue until the container shrinks to a point.

The time complexity is O(mnlog(mn)) and space complexity is O(mn). Space
complexity is straightforward. For time complexity, we can think as follows:
each cell will be pushed into and popped out of the queue only once, which
will cost O(log(queue_size)). So the total will be O(mnlog(queue_size)). The
queue size is apparently limited by the total number of cells, which is mn.
Therefore the total time complexity is O(mnlog(mn)).



