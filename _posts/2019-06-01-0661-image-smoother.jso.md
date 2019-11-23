---
layout: post
title: 661. Image Smoother
---
### Question
Given a 2D integer matrix M representing the gray scale of an image, you need
to design a smoother to make the gray scale of each cell becomes the average
gray scale (rounding down) of all the 8 surrounding cells and itself. If a
cell has less than 8 surrounding cells, then use as many as you can.

 **Example 1:**  

    
    
     **Input:**
    [[1,1,1],
     [1,0,1],
     [1,1,1]]
    **Output:**
    [[0, 0, 0],
     [0, 0, 0],
     [0, 0, 0]]
    **Explanation:**
    For the point (0,0), (0,2), (2,0), (2,2): floor(3/4) = floor(0.75) = 0
    For the point (0,1), (1,0), (1,2), (2,1): floor(5/6) = floor(0.83333333) = 0
    For the point (1,1): floor(8/9) = floor(0.88888889) = 0
    

**Note:**  

  1. The value in the given matrix is in the range of [0, 255].
  2. The length and width of the given matrix are in the range of [1, 150].

### Solution 1
Derived from StefanPochmann's idea in "game of life": the board has ints in
[0, 255], hence only 8-bit is used, we can use the middle 8-bit to store the
new state (average value), replace the old state with the new state by
shifting all values 8 bits to the right.

    
    
        vector<vector<int>> imageSmoother(vector<vector<int>>& M) {
            int m = M.size(), n = M[0].size();
            if (m == 0 || n == 0) return {{}};
            vector<vector<int>> dirs = {{0,1},{0,-1},{1,0},{-1,0},{-1,-1},{1,1},{-1,1},{1,-1}};
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    int sum = M[i][j], cnt = 1;
                    for (int k = 0; k < dirs.size(); k++) {
                        int x = i + dirs[k][0], y = j + dirs[k][1];
                        if (x < 0 || x > m - 1 || y < 0 || y > n - 1) continue;
                        sum += (M[x][y] & 0xFF);
                        cnt++;
                    }
                    M[i][j] |= ((sum / cnt) << 8);
                }
            }
             for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    M[i][j] >>= 8;
                }
             }
            return M;
        }
    
    


### Solution 2
Is there a way to reword the question? I do not understand what is being
asked.


### Solution 3
Here we have a check function to check the boundary and a inner double loop to
traverse the 9 potential candidates:

    
    
    public class ImageSmoother {
    
        public int[][] imageSmoother(int[][] M) {
            if (M == null) return null;
            int rows = M.length;
            if (rows == 0) return new int[0][];
            int cols = M[0].length;
    
            int result[][] = new int[rows][cols];
    
            for (int row = 0; row < rows; row++) {
                for (int col = 0; col < cols; col++) {
                    int count = 0;
                    int sum = 0;
                    for (int incR : new int[]{-1, 0, 1}) {
                        for (int incC : new int[]{-1, 0, 1}) {
                            if (isValid(row + incR, col + incC, rows, cols)) {
                                count++;
                                sum += M[row + incR][col + incC];
                            }
                        }
                    }
                    result[row][col] = sum / count;
                }
            }
    
            return result;
    
        }
    
        private boolean isValid(int x, int y, int rows, int cols) {
            return x >= 0 && x < rows && y >= 0 && y < cols;
        }
    }
    



