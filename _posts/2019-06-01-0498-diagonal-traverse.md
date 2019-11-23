---
layout: post
title: 498. Diagonal Traverse
---
### Question
Given a matrix of M x N elements (M rows, N columns), return all elements of
the matrix in diagonal order as shown in the below image.



 **Example:**

    
    
     **Input:**
    [
     [ 1, 2, 3 ],
     [ 4, 5, 6 ],
     [ 7, 8, 9 ]
    ]
    
    **Output:**  [1,2,4,7,5,3,6,8,9]
    
    **Explanation:**
    ![](https://assets.leetcode.com/uploads/2018/10/12/diagonal_traverse.png)
    



 **Note:**

The total number of elements of the given matrix will not exceed 10,000.

### Solution 1
    
    
        public int[] findDiagonalOrder(int[][] matrix) {
            if (matrix.length == 0) return new int[0];
            int r = 0, c = 0, m = matrix.length, n = matrix[0].length, arr[] = new int[m * n];
            for (int i = 0; i < arr.length; i++) {
                arr[i] = matrix[r][c];
                if ((r + c) % 2 == 0) { // moving up
                    if      (c == n - 1) { r++; }
                    else if (r == 0)     { c++; }
                    else            { r--; c++; }
                } else {                // moving down
                    if      (r == m - 1) { c++; }
                    else if (c == 0)     { r++; }
                    else            { r++; c--; }
                }   
            }   
            return arr;
        }
    


### Solution 2
I don't think this is a hard problem. It is easy to figure out the walk
pattern. Anyway...  
Walk patterns:

  * If out of `bottom border` (row >= m) then row = m - 1; col += 2; change walk direction.
  * if out of `right border` (col >= n) then col = n - 1; row += 2; change walk direction.
  * if out of `top border` (row < 0) then row = 0; change walk direction.
  * if out of `left border` (col < 0) then col = 0; change walk direction.
  * Otherwise, just go along with the current direction.

Time complexity: O(m * n), m = number of rows, n = number of columns.  
Space complexity: O(1).

    
    
    public class Solution {
        public int[] findDiagonalOrder(int[][] matrix) {
            if (matrix == null || matrix.length == 0) return new int[0];
            int m = matrix.length, n = matrix[0].length;
            
            int[] result = new int[m * n];
            int row = 0, col = 0, d = 0;
            int[][] dirs = {{-1, 1}, {1, -1}};
            
            for (int i = 0; i < m * n; i++) {
                result[i] = matrix[row][col];
                row += dirs[d][0];
                col += dirs[d][1];
                
                if (row >= m) { row = m - 1; col += 2; d = 1 - d;}
                if (col >= n) { col = n - 1; row += 2; d = 1 - d;}
                if (row < 0)  { row = 0; d = 1 - d;}
                if (col < 0)  { col = 0; d = 1 - d;}
            }
            
            return result;
        }
    }
    


### Solution 3
Simple two step approach:  
1- Group numbers according to diagonals. Sum of row+col in same diagonal is
same.  
2- Reverse numbers in odd diagonals before adding numbers to result list.

    
    
    class Solution(object):
        def findDiagonalOrder(self, matrix):
            """
            :type matrix: List[List[int]]
            :rtype: List[int]
            """
            result = [ ]
            dd = collections.defaultdict(list)
            if not matrix: return result
            # Step 1: Numbers are grouped by the diagonals.
            # Numbers in same diagonal have same value of row+col
            for i in range(0, len(matrix)):
                for j in range(0, len(matrix[0])):
                    dd[i+j+1].append(matrix[i][j]) # starting indices from 1, hence i+j+1.
            # Step 2: Place diagonals in the result list.
            # But remember to reverse numbers in odd diagonals.
            for k in sorted(dd.keys()):
                if k%2==1: dd[k].reverse()
                result += dd[k]
            return result
    



