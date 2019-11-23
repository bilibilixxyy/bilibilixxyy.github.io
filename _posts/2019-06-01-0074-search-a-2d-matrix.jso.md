---
layout: post
title: 74. Search a 2D Matrix
---
### Question
Write an efficient algorithm that searches for a value in an _m_ x _n_ matrix.
This matrix has the following properties:

  * Integers in each row are sorted from left to right.
  * The first integer of each row is greater than the last integer of the previous row.

 **Example 1:**

    
    
     **Input:**
    matrix = [
      [1,   3,  5,  7],
      [10, 11, 16, 20],
      [23, 30, 34, 50]
    ]
    target = 3
    **Output:** true
    

**Example 2:**

    
    
    **Input:**
    matrix = [
      [1,   3,  5,  7],
      [10, 11, 16, 20],
      [23, 30, 34, 50]
    ]
    target = 13
    **Output:** false

### Solution 1
Use binary search.

n * m matrix convert to an array => matrix[x][y] => a[x * m + y]

an array convert to n * m matrix => a[x] =>matrix[x / m][x % m];

    
    
    class Solution {
    public:
        bool searchMatrix(vector<vector<int> > &matrix, int target) {
            int n = matrix.size();
            int m = matrix[0].size();
            int l = 0, r = m * n - 1;
            while (l != r){
                int mid = (l + r - 1) >> 1;
                if (matrix[mid / m][mid % m] < target)
                    l = mid + 1;
                else 
                    r = mid;
            }
            return matrix[r / m][r % m] == target;
        }
    };


### Solution 2
    
    
    /**
     *  Do binary search in this "ordered" matrix
     */
    public boolean searchMatrix(int[][] matrix, int target) {
    	
    	int row_num = matrix.length;
    	int col_num = matrix[0].length;
    	
    	int begin = 0, end = row_num * col_num - 1;
    	
    	while(begin <= end){
    		int mid = (begin + end) / 2;
    		int mid_value = matrix[mid/col_num][mid%col_num];
    		
    		if( mid_value == target){
    			return true;
    		
    		}else if(mid_value < target){
    			//Should move a bit further, otherwise dead loop.
    			begin = mid+1;
    		}else{
    			end = mid-1;
    		}
    	}
    	
    	return false;
    }


### Solution 3
It is basically an advanced version of the binary search

    
    
    class Solution:
        # @param matrix, a list of lists of integers
        # @param target, an integer
        # @return a boolean
        # 8:21
        def searchMatrix(self, matrix, target):
            if not matrix or target is None:
                return False
    
            rows, cols = len(matrix), len(matrix[0])
            low, high = 0, rows * cols - 1
            
            while low <= high:
                mid = (low + high) / 2
                num = matrix[mid / cols][mid % cols]
    
                if num == target:
                    return True
                elif num < target:
                    low = mid + 1
                else:
                    high = mid - 1
            
            return False



