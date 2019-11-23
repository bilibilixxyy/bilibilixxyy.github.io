---
layout: post
title: 821. Bricks Falling When Hit
---
### Question
We have a grid of 1s and 0s; the 1s in a cell represent bricks.  A brick will
not drop if and only if it is directly connected to the top of the grid, or at
least one of its (4-way) adjacent bricks will not drop.

We will do some erasures sequentially. Each time we want to do the erasure at
the location (i, j), the brick (if it exists) on that location will disappear,
and then some other bricks may drop because of that erasure.

Return an array representing the number of bricks that will drop after each
erasure in sequence.

    
    
     **Example 1:**
    **Input:** 
    grid = [[1,0,0,0],[1,1,1,0]]
    hits = [[1,0]]
    **Output:** [2]
    **Explanation:**
    If we erase the brick at (1, 0), the brick at (1, 1) and (1, 2) will drop. So we should return 2.
    
    
    **Example 2:**
    **Input:** 
    grid = [[1,0,0,0],[1,1,0,0]]
    hits = [[1,1],[1,0]]
    **Output:** [0,0]
    **Explanation:**
    When we erase the brick at (1, 0), the brick at (1, 1) has already disappeared due to the last move. So each erasure will cause no bricks dropping.  Note that the erased brick (1, 0) will not be counted as a dropped brick.



 **Note:**

  * The number of rows and columns in the grid will be in the range [1, 200].
  * The number of erasures will not exceed the area of the grid.
  * It is guaranteed that each erasure will be different from any other erasure, and located inside the grid.
  * An erasure may refer to a location with no brick - if it does, no bricks drop.

### Solution 1
We can reverse the problem and count how many new no-dropping bricks are added
when we add the bricks reversely. It's just the same of counting dropping
bricks when erase one brick.

Let m, n = len(grid), len(grid[0]).

Here is the detailed solution:

  1. For each hit (i, j), if grid[i][j]==0, set grid[i][j]=-1 otherwise set grid[i][j]=0. Since a hit may happen at an empty position, we need to seperate emptys from bricks.
  2. For i in [0, n], do dfs at grid[i][0] and mark no-dropping bricks. Here we get the grid after all hits.
  3. Then for each hit (i,j) (reversely), first we check grid[i][j]==-1, if yes, it's empty, skip this hit. Then we check whether it's connected to any no-dropping bricks or it's at the top, if not, it can't add any no-dropping bricks, skip this hit. Otherwise we do dfs at grid[i][j], mark new added no-dropping bricks and record amount of them.
  4. Return the amounts of new added no-dropping bricks at each hits.

Here is a example, you can walk from the last step to the first step to see
how we transfer the question:

![image](https://s3-lc-
upload.s3.amazonaws.com/users/luckypants/image_1521450349.png)  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/luckypants/image_1521450376.png)  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/luckypants/image_1521450387.png)  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/luckypants/image_1521450393.png)

Using this method, we only do $O(n)+O(len(hits))$ dfs.

Here is my Python code:

 **EDIT** : Many thanks to @lee215 for pointing mistake out and improving the
code:

  * I used h[0], h[1] in is_connected, although it works, it's a mistake
  * Use grid[i][j]-=1 to execute hits and grid[i][j]+=1 to add bricks (So when we have repeating hits, only when we add from 0 to 1, it's the true time we hit the brick, following hits are done on empty), it makes the code concise and deals with repeating hits (Although the problem guarantees no repeating hits)

I also simplified some for loops when checking adjacent positions.

    
    
    class Solution:
        def hitBricks(self, grid, hits):
            """
            :type grid: List[List[int]]
            :type hits: List[List[int]]
            :rtype: List[int]
            """
    
            m, n = len(grid), len(grid[0])
            
            # Connect unconnected bricks and 
            def dfs(i, j):
                if not (0<=i<m and 0<=j<n) or grid[i][j]!=1:
                    return 0
                ret = 1
                grid[i][j] = 2
                ret += sum(dfs(x, y) for x, y in [(i-1, j), (i+1, j), (i, j-1), (i, j+1)])
                return ret
            
            # Check whether (i, j) is connected to Not Falling Bricks
            def is_connected(i, j):
                return i==0 or any([0<=x<m and 0<=y<n and grid[x][y]==2 for x, y in [(i-1, j), (i+1, j), (i, j-1), (i, j+1)]])
            
            # Mark whether there is a brick at the each hit
            for i, j in hits:
                grid[i][j] -= 1
                    
            # Get grid after all hits
            for i in range(n):
                dfs(0, i)
            
            # Reversely add the block of each hits and get count of newly add bricks
            ret = [0]*len(hits)
            for k in reversed(range(len(hits))):
                i, j = hits[k]
                grid[i][j] += 1
                if grid[i][j]==1 and is_connected(i, j):
                    ret[k] = dfs(i, j)-1
                
            return ret
    


### Solution 2
I just want to share my thoughts here.

If no bircks drop, then after all operations. The grid will be look like a
pool with multi islands.  
for example:  
0010000100  
0111001110  
1111111111  
after operations: [0,2], [2,4], [1,2], [0,7]  
0000000000  
0101001110  
1111011111  
so total 2 islands.

Then add bricks back reversely.  
[0,7]  
0000000100  
0101001110  
1111011111  
the right island attaches top, and its size is 9, which means 8 bricks drop in
this operation.

[1,2]  
0000000100  
0111001110  
1111011111  
the left island does not reach the top, so no brick drops.

[2,4]  
0000000100  
0111001110  
1111111111  
the left island connects to right island and acttaches top, and left island is
original 7, which means 7 bricks drop in this operation.

[0,2]  
0010000100  
0111001110  
1111111111  
the island size is just enlarged by 1, which means no brick drops.

Hope this helps:)


### Solution 3
  1. when there is a hit, we change grid cell to 0.
  2. we assign all the resulting connecting parts a unique id and judge if it falls.
  3. for each falling parts, we count the number, reset the falling cell = 0.
  4. ++id (prepare to check another resulting parts of this run/start a new run of hit).

    
    
    class Solution {
    public:
        vector<int> dr = {-1, 0, 1, 0};
        vector<int> dc = {0, 1, 0, -1};
        vector<vector<int>> g;
        int vst[201][201], id;
        int n, m;
    		
        vector<int> hitBricks(vector<vector<int>>& grid, vector<vector<int>>& hits) {
            n = grid.size(),m = grid[0].size();
            g.swap(grid);
            vector<int> ret;
            for(auto h:hits){
                int r = h[0], c = h[1];
                int removal = 0;
                if(g[r][c] == 1){
                    g[r][c] = 0;
                    for(int d = 0;d<4;d++){
                        int x = r+dr[d],y = c + dc[d];
                        if (!valid(x, y) || !g[x][y]) continue;
                        ++id; //mark each connecting parts with a unique id in this run
                        if(falling(x,y)) removal += cnt(x,y);
                    }
                }
                ret.push_back(removal);
            }
            return ret;
        }
        bool falling(int r,int c){
            if(!valid(r,c)||!g[r][c]) return true;
            if (vst[r][c] == id) return true; //visited and belongs to the same part this run
            if (r == 0) return false; //connecting 1st row
            vst[r][c] = id;
            for (int d = 0; d < 4; ++d){
                if (!falling(r + dr[d], c + dc[d])) return false;
            } 
            return true;
        }
        int cnt(int r,int c){
            if (!valid(r,c)||!g[r][c]) return 0;
            int ret = 1;
            g[r][c] = 0;
            for(int d = 0; d < 4; ++d){
                ret += cnt(r + dr[d], c + dc[d]);
            }
            return ret;
        }
        bool valid(int r,int c){
            return 0 <= r && r < n && 0 <= c && c < m;
        }
    };
    



