---
layout: post
title: 1094. Matrix Cells in Distance Order
---
### Question
We are given a matrix with `R` rows and `C` columns has cells with integer
coordinates `(r, c)`, where `0 <= r < R` and `0 <= c < C`.

Additionally, we are given a cell in that matrix with coordinates `(r0, c0)`.

Return the coordinates of all cells in the matrix, sorted by their distance
from `(r0, c0)` from smallest distance to largest distance.  Here, the
distance between two cells `(r1, c1)` and `(r2, c2)` is the Manhattan
distance, `|r1 - r2| + |c1 - c2|`.  (You may return the answer in any order
that satisfies this condition.)



 **Example 1:**

    
    
     **Input:** R = 1, C = 2, r0 = 0, c0 = 0
    **Output:** [[0,0],[0,1]]
    **Explanation:** The distances from (r0, c0) to other cells are: [0,1]
    

**Example 2:**

    
    
    **Input:** R = 2, C = 2, r0 = 0, c0 = 1
    **Output:** [[0,1],[0,0],[1,1],[1,0]]
    **Explanation:** The distances from (r0, c0) to other cells are: [0,1,1,2]
    The answer [[0,1],[1,1],[0,0],[1,0]] would also be accepted as correct.
    

**Example 3:**

    
    
    **Input:** R = 2, C = 3, r0 = 1, c0 = 2
    **Output:** [[1,2],[0,2],[1,1],[0,1],[1,0],[0,0]]
    **Explanation:** The distances from (r0, c0) to other cells are: [0,1,1,2,2,3]
    There are other answers that would also be accepted as correct, such as [[1,2],[1,1],[0,2],[1,0],[0,1],[0,0]].
    



 **Note:**

  1. `1 <= R <= 100`
  2. `1 <= C <= 100`
  3. `0 <= r0 < R`
  4. `0 <= c0 < C`

### Solution 1
    public int[][] allCellsDistOrder(int R, int C, int r0, int c0) {
        boolean[][] visited = new boolean[R][C];
        int[][] result = new int[R * C][2];
        int i = 0;
        Queue<int[]> queue = new LinkedList<int[]>();
        queue.offer(new int[]{r0, c0});
        while (!queue.isEmpty()) {
          int[] cell = queue.poll();
          int r = cell[0];
          int c = cell[1];
    
          if (r < 0 || r >= R || c < 0 || c >= C) {
            continue;
          }
          if (visited[r][c]) {
            continue;
          }
    
          result[i] = cell;
          i++;
          visited[r][c] = true;
    
          queue.offer(new int[]{r, c - 1});
          queue.offer(new int[]{r, c + 1});
          queue.offer(new int[]{r - 1, c});
          queue.offer(new int[]{r + 1, c});
        }
        return result;
      }
    


### Solution 2
The max distance is `R + C`, and the result array's length is `R * C`. Since
the distance is limited (generally, compared with the cell count), we can use
Counting Sort (計數排序) to solve it efficiently.

Time complexity is `O(R * C)`, i.e. `O(n)`.

>  **66 / 66** test cases passed.  
>  **Status** : Accepted  
>  **Runtime** : 4 ms  
>  **Memory Usage** : 38.5 MB

    
    
    class Solution {
        public int[][] allCellsDistOrder(int R, int C, int r0, int c0) {
            int[] counter = new int[R + C + 1];
            for (int r = 0; r < R; r++) {
                for (int c = 0; c < C; c++) {
                    int dist = Math.abs(r - r0) + Math.abs(c - c0);
                    counter[dist + 1] += 1;
                }
            }
            
            for (int i = 1; i < counter.length; i++) {
                counter[i] += counter[i - 1];
            }
            
            int[][] ans = new int[R * C][];
            for (int r = 0; r < R; r++) {
                for (int c = 0; c < C; c++) {
                    int dist = Math.abs(r - r0) + Math.abs(c - c0);
                    ans[counter[dist]] = new int[] { r, c };
                    counter[dist]++;
                }
            }
            
            return ans;
        }
    }
    


### Solution 3
# Solution

  1. Increment the distance from 0 till `R + C`.
  2. From our point, generate all points with the distance.
  3. Add valid poits (within our grid) to the result.

The image below demonstrates the generation of points with distance 5:  
![image](https://assets.leetcode.com/users/votrubac/image_1555825343.png)

    
    
    vector<vector<int>> allCellsDistOrder(int R, int C, int r0, int c0) {
      vector<vector<int>> res = { { r0, c0 } };
      auto max_d = max({ r0 + c0, c0 + R - r0, r0 + C - c0, R - r0 + C - c0 });
      for (auto d = 1; d <= max_d; ++d) {
        for (int x = d; x >= -d; --x) {
          auto r1 = r0 + x, c1_a = c0 + d - abs(x), c1_b = c0 + abs(x) - d;
          if (r1 >= 0 && r1 < R) {
            if (c1_a >= 0 && c1_a < C) res.push_back({ r1, c1_a });
            if (c1_a != c1_b && c1_b >= 0 && c1_b < C) res.push_back({ r1, c1_b });
          }
        }
      }
      return res;
    }
    

# Complexity Analysis

Runtime: _O((R + C) ^ 2)_ , which is linear to the total number of cells. We
analyze maximum 4(R + C - 2)(R + C - 2) cells once.  
Memory: _O(1)_. We do not use any additional memory (except for returning the
result, which does not count).



