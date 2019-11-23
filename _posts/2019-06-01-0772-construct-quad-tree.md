---
layout: post
title: 772. Construct Quad Tree
---
### Question
We want to use quad trees to store an `N x N` boolean grid. Each cell in the
grid can only be true or false. The root node represents the whole grid. For
each node, it will be subdivided into four children nodes **until the values
in the region it represents are all the same**.

Each node has another two boolean attributes : `isLeaf` and `val`. `isLeaf` is
true if and only if the node is a leaf node. The `val` attribute for a leaf
node contains the value of the region it represents.

Your task is to use a quad tree to represent a given grid. The following
example may help you understand the problem better:

Given the `8 x 8` grid below, we want to construct the corresponding quad
tree:

![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/02/01/962_grid.png)

It can be divided according to the definition above:

![](https://s3-lc-
upload.s3.amazonaws.com/uploads/2018/02/01/962_grid_divided.png)



The corresponding quad tree should be as following, where each node is
represented as a `(isLeaf, val)` pair.

For the non-leaf nodes, `val` can be arbitrary, so it is represented as `*`.

![](https://s3-lc-
upload.s3.amazonaws.com/uploads/2018/02/01/962_quad_tree.png)

**Note:**

  1. `N` is less than `1000` and guaranteened to be a power of 2.
  2. If you want to know more about the quad tree, you can refer to its [wiki](https://en.wikipedia.org/wiki/Quadtree).

### Solution 1
    
    
        public Node construct(int[][] g) { return build(0, 0, g.length - 1, g.length - 1, g);}
    
        Node build(int r1, int c1, int r2, int c2, int[][] g) {
            if (r1 > r2 || c1 > c2) return null;
            boolean isLeaf = true;
            int val = g[r1][c1];
            for (int i = r1; i <= r2; i++)
                for (int j = c1; j <= c2; j++)
                    if (g[i][j] != val) {
                        isLeaf = false;
                        break;
                    }
            if (isLeaf)
                return new Node(val == 1, true, null, null, null, null);
            int rowMid = (r1 + r2) / 2, colMid = (c1 + c2) / 2;
            return new Node(false, false,
                build(r1, c1, rowMid, colMid, g),//top left 
                build(r1, colMid + 1, rowMid, c2, g),//top right
                build(rowMid + 1, c1, r2, colMid, g),//bottom left 
                build(rowMid + 1, colMid + 1, r2, c2, g));//bottom right
        }


### Solution 2
    
    
    class Solution {
        public Node construct(int[][] grid) {
            return helper(grid, 0, 0, grid.length);
        }
        private Node helper(int[][] grid, int x, int y, int len) {
            if (len == 1) {
                return new Node(grid[x][y] != 0, true, null, null, null, null);
            }
            Node result = new Node();
            Node topLeft = helper(grid, x, y, len / 2);
            Node topRight = helper(grid, x, y + len / 2, len / 2);
            Node bottomLeft = helper(grid, x + len / 2, y, len / 2);
            Node bottomRight = helper(grid, x + len / 2, y + len / 2, len / 2);
            if (topLeft.isLeaf && topRight.isLeaf && bottomLeft.isLeaf && bottomRight.isLeaf
               && topLeft.val == topRight.val && topRight.val == bottomLeft.val && bottomLeft.val == bottomRight.val) {
                result.isLeaf = true;
                result.val = topLeft.val;
            } else {
                result.topLeft = topLeft;
                result.topRight = topRight;
                result.bottomLeft = bottomLeft;
                result.bottomRight = bottomRight;
            }
            return result;
        }
    }
    


### Solution 3
    
    
    def construct(self, grid):
    	if not grid: return None
    	if self.isLeaf(grid):
    		return Node(grid[0][0] == 1, True, None, None, None, None)
    	n = len(grid)
    	return Node('*',
    				False,
    				self.construct([row[:n/2] for row in grid[:n/2]]),
    				self.construct([row[n/2:] for row in grid[:n/2]]),
    				self.construct([row[:n/2] for row in grid[n/2:]]),
    				self.construct([row[n/2:] for row in grid[n/2:]]))
    
    def isLeaf(self, grid):
        return all(grid[i][j] == grid[0][0] 
            for i in range(len(grid)) for j in range(len(grid[i])))
    



