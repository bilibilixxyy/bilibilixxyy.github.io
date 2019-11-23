---
layout: post
title: 1043. Grid Illumination
---
### Question
On a `N x N` grid of cells, each cell `(x, y)` with `0 <= x < N` and `0 <= y <
N` has a lamp.

Initially, some number of lamps are on.  `lamps[i]` tells us the location of
the `i`-th lamp that is on.  Each lamp that is on illuminates every square on
its x-axis, y-axis, and both diagonals (similar to a Queen in chess).

For the i-th query `queries[i] = (x, y)`, the answer to the query is 1 if the
cell (x, y) is illuminated, else 0.

After each query `(x, y)` [in the order given by `queries`], we turn off any
lamps that are at cell `(x, y)` or are adjacent 8-directionally (ie., share a
corner or edge with cell `(x, y)`.)

Return an array of answers.  Each value `answer[i]` should be equal to the
answer of the `i`-th query `queries[i]`.



 **Example 1:**

    
    
     **Input:** N = 5, lamps = [[0,0],[4,4]], queries = [[1,1],[1,0]]
    **Output:** [1,0]
    **Explanation:**
    Before performing the first query we have both lamps [0,0] and [4,4] on.
    The grid representing which cells are lit looks like this, where [0,0] is the top left corner, and [4,4] is the bottom right corner:
    1 1 1 1 1
    1 1 0 0 1
    1 0 1 0 1
    1 0 0 1 1
    1 1 1 1 1
    Then the query at [1, 1] returns 1 because the cell is lit.  After this query, the lamp at [0, 0] turns off, and the grid now looks like this:
    1 0 0 0 1
    0 1 0 0 1
    0 0 1 0 1
    0 0 0 1 1
    1 1 1 1 1
    Before performing the second query we have only the lamp [4,4] on.  Now the query at [1,0] returns 0, because the cell is no longer lit.
    



 **Note:**

  1. `1 <= N <= 10^9`
  2. `0 <= lamps.length <= 20000`
  3. `0 <= queries.length <= 20000`
  4. `lamps[i].length == queries[i].length == 2`

### Solution 1
>  _I did not look at the problem constraints and implemented a simple
solution where everything is tracked in a grid. Obviously, I got MLE. Stepping
on the same rake again and again; could have saved 20 minutes in the contest
if I read the description carefully..._

We can use a similar technique as in
[N-Queens](https://leetcode.com/problems/n-queens/). We track illuminated `x`
and `y` coordinates, as well as two diagonals. Ascending (left-to-right)
diagonal `a_d` is identified by the sum of the coordinates, and descending
`d_d` \- by their difference.

For example, for the light in position `[3, 2]`, we would record `++x[3]`,
`++y[2]`, `++a_d[5]`, and `++d_d[1]`. Since lamps can be removed later, we use
hashmap (not a hashset) to track how many lamps are illuminating coordinates
and diagonals. Also, we use another hashmap `ls` to track lamp positions.

![image](https://assets.leetcode.com/users/votrubac/image_1550986316.png)

Now, when we process the queries, we just need to check if any coordinate or
diagonal is illuminated. If it is, we need to check if there are lamps needing
to be turned off. When turning off a lamp, we remove it's coordinates from
`ls` and decrement the count of the corresponding coordinates and diagonals.

> Note that if the queried position is not illuminated, we do not need to
check for lamps.

    
    
    vector<int> gridIllumination(int N, vector<vector<int>>& lamps, vector<vector<int>>& queries) {
      vector<int> res;
      unordered_map<int, int> x, y, a_d, d_d;
      unordered_map<int, unordered_set<int>> ls;
      for (auto l : lamps) {
        auto i = l[0], j = l[1];
        ++x[i], ++y[j], ++a_d[i + j], ++d_d[i - j], ls[i].insert(j);
      }
      for (auto q : queries) {
        auto i = q[0], j = q[1];
        if (x[i] || y[j] || a_d[i + j] || d_d[i - j]) {
          res.push_back(1);
          for (auto li = i - 1; li <= i + 1; ++li)
            for (auto lj = j - 1; lj <= j + 1; ++lj){
              if (ls[li].erase(lj)) {
                --x[li], --y[lj], --a_d[li + lj], --d_d[li - lj];
              }
          }
        }
        else res.push_back(0);
      }
      return res;
    }
    

You can also use `set<pair<int, int>>` to track lamps. I found the runtime be
similar to `unordered_map<int, unordered_set<int>>`. Alternatively, you can
define a hash function for the pair (STL does not provide one) and use
`unordered_set<pair<int, int>>`. This implementation is ~10% faster comparing
to hashmap and set.

    
    
    struct pairHash {
      size_t operator()(const pair<int, int> &x) const { return x.first ^ x.second; }
    };
    vector<int> gridIllumination(int N, vector<vector<int>>& lamps, vector<vector<int>>& queries) {
      vector<int> res;
      unordered_map<int, int> x, y, a_d, d_d;
      unordered_set<pair<int, int>, pairHash> ls;
      for (auto l : lamps) {
        auto i = l[0], j = l[1];
        ++x[i], ++y[j], ++a_d[i + j], ++d_d[i - j], ls.insert({ i, j });
      }
      for (auto q : queries) {
        auto i = q[0], j = q[1];
        if (x[i] || y[j] || a_d[i + j] || d_d[i - j]) {
          res.push_back(1);
          for (int li = i - 1; li <= i + 1; ++li)
            for (int lj = j - 1; lj <= j + 1; ++lj) {
              if (ls.erase({li, lj})) {
                --x[li], --y[lj], --a_d[li + lj], --d_d[li - lj];
              }
            }
        }
        else res.push_back(0);
      }
      return res;
    }
    


### Solution 2
[https://www.youtube.com/watch?v=EJjB2WI7EdM&feature=youtu.be](https://www.youtube.com/watch?v=EJjB2WI7EdM&feature=youtu.be)


### Solution 3
The basic idea is:

  * The row, column or diagonal will remain illuminated if there are > 0 lamps on that particular row, column or diagonal
  * all the diagonals with slope= 1, can be represented by `x= y+c` i.e. they have x-y = constant
  * all the diagonals with slope= -1, can be represented by `x= -y+c` i.e they have x+y = constant
  * store the counts in separate maps
  * When a lamp is turned off, the count of lamps in respective row, column or diagonal decreases by 1

    
    
    class Solution {
        int[][] dirs = {{0,1}, {0,-1}, {1,0}, {-1,0}, {1,1}, {1,-1}, {-1,1}, {-1,-1}, {0,0}};
        
    	public int[] gridIllumination(int N, int[][] lamps, int[][] queries) {
            Map<Integer, Integer> m1 = new HashMap();       // row number to count of lamps
            Map<Integer, Integer> m2 = new HashMap();       // col number to count of lamps
            Map<Integer, Integer> m3 = new HashMap();       // diagonal x-y to count of lamps
            Map<Integer, Integer> m4 = new HashMap();       // diagonal x+y to count of lamps
            Map<Integer, Boolean> m5 = new HashMap();       // whether lamp is  ON|OFF
            
            // increment counters while adding lamps
            for(int i=0; i<lamps.length; i++){
                int x = lamps[i][0];
                int y = lamps[i][1];
                m1.put(x, m1.getOrDefault(x, 0) + 1);
                m2.put(y, m2.getOrDefault(y, 0) + 1);
                m3.put(x-y, m3.getOrDefault(x-y, 0) + 1);
                m4.put(x+y, m4.getOrDefault(x+y, 0) + 1);
                m5.put(N*x + y, true);
            }
    
            int[] ans = new int[queries.length];
            // address queries
            for(int i=0; i<queries.length; i++){
                int x = queries[i][0];
                int y = queries[i][1];
                
                ans[i] = (m1.getOrDefault(x, 0) > 0 || m2.getOrDefault(y, 0) > 0 || m3.getOrDefault(x-y, 0) > 0 || m4.getOrDefault(x+y, 0) > 0) ? 1 : 0;            
                // switch off the lamps, if any
                for(int d=0; d<dirs.length; d++){
                    int x1 = x + dirs[d][0];
                    int y1 = y + dirs[d][1];
    				if(m5.containsKey(N*x1 + y1) && m5.get(N*x1 + y1)){
                        // the lamp is on, turn it off, so decrement the count of the lamps
                        m1.put(x1, m1.getOrDefault(x1, 1) - 1);
                        m2.put(y1, m2.getOrDefault(y1, 1) - 1);
                        m3.put(x1 - y1, m3.getOrDefault(x1 - y1, 1) - 1);
                        m4.put(x1 + y1, m4.getOrDefault(x1 + y1, 1) - 1);
                        m5.put(N*x1+y1, false);
                    }
                }
            }
            
            return ans;
        }
    }
    

##### Update:

Following redundant code removed, Thanks to [j46](https://leetcode.com/j46/)
for pointing it out!

    
    
    	// following condition updated
    	if(isValid(x1, y1) && m5.containsKey(N*x1 + y1) && m5.get(N*x1 + y1)){
    	// removed the function for checking if the co-ordinates are valid, it is taken care by the .containsKey
        private boolean isValid(int x, int y){
            return (x >= 0 && x < MAX && y>=0 && y< MAX);    // MAX was class variable equal to N
        }
    

The exact complexity in terms of number of lamps, `L` and number of queries,
`Q` is as follows:  
 **Time Complexity:** `O(L+Q)`  
 **Space Complexity:** `O(L)`



