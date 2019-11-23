---
layout: post
title: 240. Search a 2D Matrix II
---
### Question
Write an efficient algorithm that searches for a value in an _m_ x _n_ matrix.
This matrix has the following properties:

  * Integers in each row are sorted in ascending from left to right.
  * Integers in each column are sorted in ascending from top to bottom.

 **Example:**

Consider the following matrix:

    
    
    [
      [1,   4,  7, 11, 15],
      [2,   5,  8, 12, 19],
      [3,   6,  9, 16, 22],
      [10, 13, 14, 17, 24],
      [18, 21, 23, 26, 30]
    ]
    

Given target = `5`, return `true`.

Given target = `20`, return `false`.

### Solution 1
We start search the matrix from top right corner, initialize the current
position to top right corner, if the target is greater than the value in
current position, then the target can not be in entire row of current position
because the row is sorted, if the target is less than the value in current
position, then the target can not in the entire column because the column is
sorted too. We can rule out one row or one column each time, so the time
complexity is O(m+n).

    
    
    public class Solution {
        public boolean searchMatrix(int[][] matrix, int target) {
            if(matrix == null || matrix.length < 1 || matrix[0].length <1) {
                return false;
            }
            int col = matrix[0].length-1;
            int row = 0;
            while(col >= 0 && row <= matrix.length-1) {
                if(target == matrix[row][col]) {
                    return true;
                } else if(target < matrix[row][col]) {
                    col--;
                } else if(target > matrix[row][col]) {
                    row++;
                }
            }
            return false;
        }
    }


### Solution 2
    
    
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size();
        if (m == 0) return false;
        int n = matrix[0].size();
    
        int i = 0, j = n - 1;
        while (i < m && j >= 0) {
            if (matrix[i][j] == target)
                return true;
            else if (matrix[i][j] > target) {
                j--;
            } else 
                i++;
        }
        return false;
    }


### Solution 3
Search from the **top-right** element and reduce the search space by one row
or column at each time.

    
    
    [[  1,  4,  7, 11, 15],
     [ 2,  5,  8, 12, 19], 
     [ 3,  6,  9, 16, 22],
     [10, 13, 14, 17, 24],
     [18, 21, 23, 26, 30]]
    

Suppose we want to search for `12` in the above matrix. compare `12` with the
top-right element `nums[0][4] = 15`. Since `12 < 15`, `12` cannot appear in
the column of `15` since all elements in that column are greater than or equal
to `15`. Now we reduce the search space by one column (the last column).

We further compare `12` with the top-right element of the remaining matrix,
which is `nums[0][3] = 11`. Since `12 > 11`, `12` cannot appear in the row of
`11` since all elements in this row are less than or equal to `11` (the last
column has been discarded). Now we reduce the search space by one row (the
first row).

We move on to compare `12` with the top-right element of the remaining matrix,
which is `nums[1][3] = 12`. Since it is equal to `12`, we return `true`.

    
    
    class Solution {
    public:
        bool searchMatrix(vector<vector<int>>& matrix, int target) {
            int m = matrix.size(), n = m ? matrix[0].size() : 0, r = 0, c = n - 1;
            while (r < m && c >= 0) {
                if (matrix[r][c] == target) {
                    return true;
                }
                matrix[r][c] > target ? c-- : r++;
            }
            return false;
        }
    };
    



