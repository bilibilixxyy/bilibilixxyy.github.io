---
layout: post
title: 363. Max Sum of Rectangle No Larger Than K
---
### Question
Given a non-empty 2D matrix _matrix_ and an integer _k_ , find the max sum of
a rectangle in the _matrix_ such that its sum is no larger than _k_.

 **Example:**

    
    
     **Input:** matrix = [[1,0,1],[0,-2,3]], k = 2
    **Output:** 2 
    **Explanation:**  Because the sum of rectangle [[0, 1], [-2, 3]] is 2,
                 and 2 is the max number no larger than k (k = 2).

 **Note:**

  1. The rectangle inside the matrix must have an area > 0.
  2. What if the number of rows is much larger than the number of columns?

### Solution 1
The naive solution is brute-force, which is O((mn)^2). In order to be more
efficient, I tried something similar to Kadane's algorithm. The only
difference is that here we have upper bound restriction K. Here's the easily
understanding video link for the problem "find the max sum rectangle in 2D
array": [Maximum Sum Rectangular Submatrix in Matrix dynamic programming/2D
kadane](https://www.youtube.com/watch?v=yCQN096CwWM) (Trust me, it's really
easy and straightforward).

Once you are clear how to solve the above problem, the next step is to find
the max sum no more than K in an array. This can be done within O(nlogn), and
you can refer to this article: [max subarray sum no more than
k](https://www.quora.com/Given-an-array-of-integers-A-and-an-integer-k-find-a-
subarray-that-contains-the-largest-sum-subject-to-a-constraint-that-the-sum-
is-less-than-k).

For the solution below, I assume that the number of rows is larger than the
number of columns. Thus in general time complexity is O[min(m,n)^2 * max(m,n)
* log(max(m,n))], space O(max(m, n)).

    
    
    int maxSumSubmatrix(vector<vector<int>>& matrix, int k) {
        if (matrix.empty()) return 0;
        int row = matrix.size(), col = matrix[0].size(), res = INT_MIN;
        for (int l = 0; l < col; ++l) {
            vector<int> sums(row, 0);
            for (int r = l; r < col; ++r) {
                for (int i = 0; i < row; ++i) {
                    sums[i] += matrix[i][r];
                }
                
                // Find the max subarray no more than K 
                set<int> accuSet;
                accuSet.insert(0);
                int curSum = 0, curMax = INT_MIN;
                for (int sum : sums) {
                    curSum += sum;
                    set<int>::iterator it = accuSet.lower_bound(curSum - k);
                    if (it != accuSet.end()) curMax = std::max(curMax, curSum - *it);
                    accuSet.insert(curSum);
                }
                res = std::max(res, curMax);
            }
        }
        return res;
    }
    


### Solution 2
    
    
    /* first  consider the situation matrix is 1D
        we can save every sum of 0~i(0<=i<len) and binary search previous sum to find 
        possible result for every index, time complexity is O(NlogN).
        so in 2D matrix, we can sum up all values from row i to row j and create a 1D array 
        to use 1D array solution.
        If col number is less than row number, we can sum up all values from col i to col j 
        then use 1D array solution.
    */
    public int maxSumSubmatrix(int[][] matrix, int target) {
        int row = matrix.length;
        if(row==0)return 0;
        int col = matrix[0].length;
        int m = Math.min(row,col);
        int n = Math.max(row,col);
        //indicating sum up in every row or every column
        boolean colIsBig = col>row;
        int res = Integer.MIN_VALUE;
        for(int i = 0;i<m;i++){
            int[] array = new int[n];
            // sum from row j to row i
            for(int j = i;j>=0;j--){
                int val = 0;
                TreeSet<Integer> set = new TreeSet<Integer>();
                set.add(0);
                //traverse every column/row and sum up
                for(int k = 0;k<n;k++){
                    array[k]=array[k]+(colIsBig?matrix[j][k]:matrix[k][j]);
                    val = val + array[k];
                    //use  TreeMap to binary search previous sum to get possible result 
                    Integer subres = set.ceiling(val-target);
                    if(null!=subres){
                        res=Math.max(res,val-subres);
                    }
                    set.add(val);
                }
            }
        }
        return res;
    }


### Solution 3
Decide to post because I was actually asked this question during my interview!  
There is a simple version of O(n^4).  
The idea is to calculate every rectangle [[r1,c1], [r2,c2]], and simply pick
the max area <= k.  
An improved version takes O(n^3logn). It borrows the idea to find max subarray
with sum <= k in 1D array, and apply here: we find all rectangles bounded
between r1 & r2, with columns from 0 to end. Pick a pair from tree.  
I remember the interviewer said there could be an even better solution, but I
haven't figured that out...

Solution I, O(n^4):

    
    
    public int maxSumSubmatrix(int[][] matrix, int k) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0)
            return 0;
        int rows = matrix.length, cols = matrix[0].length;
        int[][] areas = new int[rows][cols];
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                int area = matrix[r][c];
                if (r-1 >= 0)
                    area += areas[r-1][c];
                if (c-1 >= 0)
                    area += areas[r][c-1];
                if (r-1 >= 0 && c-1 >= 0)
                    area -= areas[r-1][c-1];
                areas[r][c] = area;
            }
        }
        int max = Integer.MIN_VALUE;
        for (int r1 = 0; r1 < rows; r1++) {
            for (int c1 = 0; c1 < cols; c1++) {
                for (int r2 = r1; r2 < rows; r2++) {
                    for (int c2 = c1; c2 < cols; c2++) {
                        int area = areas[r2][c2];
                        if (r1-1 >= 0)
                            area -= areas[r1-1][c2];
                        if (c1-1 >= 0)
                            area -= areas[r2][c1-1];
                        if (r1-1 >= 0 && c1 -1 >= 0)
                            area += areas[r1-1][c1-1];
                        if (area <= k)
                            max = Math.max(max, area);
                    }
                }
            }
        }
        return max;
    }
    

Solution II (O(n^3logn)

    
    
    public int maxSumSubmatrix(int[][] matrix, int k) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0)
            return 0;
        int rows = matrix.length, cols = matrix[0].length;
        int[][] areas = new int[rows][cols];
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                int area = matrix[r][c];
                if (r-1 >= 0)
                    area += areas[r-1][c];
                if (c-1 >= 0)
                    area += areas[r][c-1];
                if (r-1 >= 0 && c-1 >= 0)
                    area -= areas[r-1][c-1];
                areas[r][c] = area;
            }
        }
        int max = Integer.MIN_VALUE;
        for (int r1 = 0; r1 < rows; r1++) {
            for (int r2 = r1; r2 < rows; r2++) {
                TreeSet<Integer> tree = new TreeSet<>();
                tree.add(0);    // padding
                for (int c = 0; c < cols; c++) {
                    int area = areas[r2][c];
                    if (r1-1 >= 0)
                        area -= areas[r1-1][c];
                    Integer ceiling = tree.ceiling(area - k);
                    if (ceiling != null)
                        max = Math.max(max, area - ceiling);
                    tree.add(area);
                }
            }
        }
        return max;
    }



