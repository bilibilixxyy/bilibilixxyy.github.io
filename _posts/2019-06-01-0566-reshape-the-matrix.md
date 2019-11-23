---
layout: post
title: 566. Reshape the Matrix
---
### Question
In MATLAB, there is a very useful function called 'reshape', which can reshape
a matrix into a new one with different size but keep its original data.

You're given a matrix represented by a two-dimensional array, and two
**positive** integers **r** and **c** representing the **row** number and
**column** number of the wanted reshaped matrix, respectively.

The reshaped matrix need to be filled with all the elements of the original
matrix in the same **row-traversing** order as they were.

If the 'reshape' operation with given parameters is possible and legal, output
the new reshaped matrix; Otherwise, output the original matrix.

 **Example 1:**  

    
    
     **Input:** 
    nums = 
    [[1,2],
     [3,4]]
    r = 1, c = 4
    **Output:** 
    [[1,2,3,4]]
    **Explanation:**  
     The **row-traversing** of nums is [1,2,3,4]. The new reshaped matrix is a 1 * 4 matrix, fill it row by row by using the previous list.
    

**Example 2:**  

    
    
    **Input:** 
    nums = 
    [[1,2],
     [3,4]]
    r = 2, c = 4
    **Output:** 
    [[1,2],
     [3,4]]
    **Explanation:**  
     There is no way to reshape a 2 * 2 matrix to a 2 * 4 matrix. So output the original matrix.
    

**Note:**  

  1. The height and width of the given matrix is in range [1, 100].
  2. The given r and c are all positive.

### Solution 1
    
    
    public int[][] matrixReshape(int[][] nums, int r, int c) {
        int n = nums.length, m = nums[0].length;
        if (r*c != n*m) return nums;
        int[][] res = new int[r][c];
        for (int i=0;i<r*c;i++) 
            res[i/c][i%c] = nums[i/m][i%m];
        return res;
    }
    


### Solution 2
We can use `matrix[index / width][index % width]` for both the input and the
output matrix.

    
    
    public int[][] matrixReshape(int[][] nums, int r, int c) {
        int m = nums.length, n = nums[0].length;
        if (r * c != m * n)
            return nums;
        int[][] reshaped = new int[r][c];
        for (int i = 0; i < r * c; i++)
            reshaped[i/c][i%c] = nums[i/n][i%n];
        return reshaped;
    }


### Solution 3
####  **Solution 1 -`NumPy`**

When I read "MATLAB", I immediately thought "NumPy". Thanks to @fallcreek for
pointing out `tolist`, makes converting the result to the correct type easier
than what I had originally.

    
    
    import numpy as np
    
    class Solution(object):
        def matrixReshape(self, nums, r, c):
            try:
                return np.reshape(nums, (r, c)).tolist()
            except:
                return nums
    

#### **Solution 2 - Oneliner**

An ugly oneliner :-)

    
    
    def matrixReshape(self, nums, r, c):
        return nums if len(sum(nums, [])) != r * c else map(list, zip(*([iter(sum(nums, []))]*c)))
    

A more readable version of that:

    
    
    def matrixReshape(self, nums, r, c):
        flat = sum(nums, [])
        if len(flat) != r * c:
            return nums
        tuples = zip(*([iter(flat)] * c))
        return map(list, tuples)
    

#### **Solution 3 -`itertools`**

    
    
     def matrixReshape(self, nums, r, c):
        if r * c != len(nums) * len(nums[0]):
            return nums
        it = itertools.chain(*nums)
        return [list(itertools.islice(it, c)) for _ in xrange(r)]



