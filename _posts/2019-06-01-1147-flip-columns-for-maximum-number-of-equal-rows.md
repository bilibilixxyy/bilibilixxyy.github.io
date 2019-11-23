---
layout: post
title: 1147. Flip Columns For Maximum Number of Equal Rows
---
### Question
Given a `matrix` consisting of 0s and 1s, we may choose any number of columns
in the matrix and flip **every**  cell in that column.  Flipping a cell
changes the value of that cell from 0 to 1 or from 1 to 0.

Return the maximum number of rows that have all values equal after some number
of flips.



 **Example 1:**

    
    
     **Input:** [[0,1],[1,1]]
    **Output:** 1
    **Explanation:** After flipping no values, 1 row has all values equal.
    

**Example 2:**

    
    
    **Input:** [[0,1],[1,0]]
    **Output:** 2
    **Explanation:** After flipping values in the first column, both rows have equal values.
    

**Example 3:**

    
    
    **Input:** [[0,0,0],[0,0,1],[1,1,0]]
    **Output:** 2
    **Explanation:** After flipping values in the first two columns, the last two rows have equal values.
    



 **Note:**

  1. `1 <= matrix.length <= 300`
  2. `1 <= matrix[i].length <= 300`
  3. All `matrix[i].length`'s are equal
  4. `matrix[i][j]` is `0` or `1`

### Solution 1
Assume the `i-th` row is an all-0s row after flipping `x` columns.

  1. If there are any other all-0s row, say `j-th` row, then the `j-th` row before flipping should be the same as the `i-th` row.
  2. If there are any other all-1s row, say `k-th` row, then the `k-th` row before flipping should be totally different from the `i-th` row.

For example:

    
    
     [1,0,0,1,0]                                      [0,0,0,0,0]  // all-0s
     [1,0,0,1,0]  after flipping 0-th and 4-th rows   [0,0,0,0,0]  // all-0s
     [1,0,1,1,1] -----------------------------------> [0,0,1,0,1]
     [0,1,1,0,1]                                      [1,1,1,1,1]  // all-1s
     [1,0,0,1,1]                                      [0,0,0,0,1]
     
     1st, 2nd, and 4th rows have all values equal.
    

After flipping, the 1st and 2nd rows are all-0s, and the 4th row are all-1s.
We can find that before flipping:  
the 2nd row is the same as the 1st row.  
the 4th row is totally different from the 1st row.

So, the problem is transformed to: **Find the i-th row, which has the most
same or totaly different rows in the matrix.**

\--

Java:

    
    
    class Solution {
        public int maxEqualRowsAfterFlips(int[][] matrix) {
            int ans = 0;
            int m = matrix.length, n = matrix[0].length;
            int[] flip = new int[n];
            for(int i = 0; i < m; i++) {
                int cnt = 0;
                for(int j = 0; j < n; j++) flip[j] = 1 - matrix[i][j];
                for(int k = 0; k < m; k++) {
                    if(Arrays.equals(matrix[k], matrix[i]) || Arrays.equals(matrix[k], flip)) cnt++;
                }
                ans = Math.max(ans, cnt);
            }
            return ans;
        }
    }
    


### Solution 2
**Python:**

    
    
        def max EqualRowsAfterFlips(self, A):
            return max(collections.Counter(tuple(x ^ r[0] for x in r) for r in A).values())
    


### Solution 3
    
    
    public int maxEqualRowsAfterFlips(int[][] matrix) {
            Map<String, Integer> map = new HashMap<>();
            
            for (int[] row : matrix) {
                StringBuilder sb1 = new StringBuilder();
                StringBuilder sb2 = new StringBuilder();
                
                for (int r : row) {
                    sb1.append(r);
                    sb2.append(1 - r);
                }
                String str1 = sb1.toString();
                String str2 = sb2.toString();
                
                map.put(str1, map.getOrDefault(str1,0) + 1);
                map.put(str2, map.getOrDefault(str2,0) + 1);
            }
            
            int res = 0;
            for(int v : map.values()) res = Math.max(res, v);
            
            return res;
        }



