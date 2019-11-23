---
layout: post
title: 895. Shortest Path to Get All Keys
---
### Question
We are given a 2-dimensional `grid`. `"."` is an empty cell, `"#"` is a wall,
`"@"` is the starting point, (`"a"`, `"b"`, ...) are keys, and (`"A"`, `"B"`,
...) are locks.

We start at the starting point, and one move consists of walking one space in
one of the 4 cardinal directions.  We cannot walk outside the grid, or walk
into a wall.  If we walk over a key, we pick it up.  We can't walk over a lock
unless we have the corresponding key.

For some 1 <= K <= 6, there is exactly one lowercase and one uppercase letter
of the first `K` letters of the English alphabet in the grid.  This means that
there is exactly one key for each lock, and one lock for each key; and also
that the letters used to represent the keys and locks were chosen in the same
order as the English alphabet.

Return the lowest number of moves to acquire all keys.  If it's impossible,
return `-1`.



 **Example 1:**

    
    
     **Input:** ["@.a.#","###.#","b.A.B"]
    **Output:** 8
    

**Example 2:**

    
    
    **Input:** ["@..aA","..B#.","....b"]
    **Output:** 6
    



 **Note:**

  1. `1 <= grid.length <= 30`
  2. `1 <= grid[0].length <= 30`
  3. `grid[i][j]` contains only` '.'`, `'#'`, `'@'`, `'a'-``'f``'` and `'A'-'F'`
  4. The number of keys is in `[1, 6]`.  Each key has a different letter and opens exactly one lock.

### Solution 1
  1. Use Bit to represent the keys.
  2. Use `State` to represent visited states.

    
    
    class Solution {
        class State {
            int keys, i, j;
            State(int keys, int i, int j) {
                this.keys = keys;
                this.i = i;
                this.j = j;
            }
        }
        public int shortestPathAllKeys(String[] grid) {
            int x = -1, y = -1, m = grid.length, n = grid[0].length(), max = -1;
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    char c = grid[i].charAt(j);
                    if (c == '@') {
                        x = i;
                        y = j;
                    }
                    if (c >= 'a' && c <= 'f') {
                        max = Math.max(c - 'a' + 1, max);
                    }
                }
            }
            State start = new State(0, x, y);
            Queue<State> q = new LinkedList<>();
            Set<String> visited = new HashSet<>();
            visited.add(0 + " " + x + " " + y);
            q.offer(start);
            int[][] dirs = new int[][]{{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
            int step = 0;
            while (!q.isEmpty()) {
                int size = q.size();
                while (size-- > 0) {
                    State cur = q.poll();
                    if (cur.keys == (1 << max) - 1) {
                        return step;
                    }
                    for (int[] dir : dirs) {
                        int i = cur.i + dir[0];
                        int j = cur.j + dir[1];
                        int keys = cur.keys;
                        if (i >= 0 && i < m && j >= 0 && j < n) {
                            char c = grid[i].charAt(j);
                            if (c == '#') {
                                continue;
                            }
                            if (c >= 'a' && c <= 'f') {
                                keys |= 1 << (c - 'a');
                            }
                            if (c >= 'A' && c <= 'F' && ((keys >> (c - 'A')) & 1) == 0) {
                                continue;
                            }
                            if (!visited.contains(keys + " " + i + " " + j)) {
                                visited.add(keys + " " + i + " " + j);
                                q.offer(new State(keys, i, j));
                            }
                        }
                    }
                }
                step++;
            }
            return -1;
        }
    }
    


### Solution 2
<https://www.youtube.com/watch?v=_NSRwuCwIQk>

Hope it's helpful.


### Solution 3
  1. Get the starting point, and how many keys are in the grid.
  2. BFS. Find all the keys.

Trick1: I have a string called keys which not only have keys, but also all the
chars that you can step on. I appended Keys to that string when I got one.  
Trick2: In order not to repeat past moves, I have a set called moves. I check
if I have already went to that location with current key string.

If I run out of options, I will then return -1

Time complexity: O(mn2^k)  
k: number of keys  
n: column length  
m: row length

    
    
    class Solution(object):
        def shortestPathAllKeys(self, grid):
            """
            :type grid: List[str]
            :rtype: int
            """
            n, m = len(grid), len(grid[0])
            numOfKeys = 0
            direc = [[0,1],[0,-1],[1,0],[-1,0]]
            moves = set()
            
            for i in range(n):
                for j in range(m):
                    if grid[i][j] == '@':
                        starti = i
                        startj = j
                    elif grid[i][j] in "abcdef":
                        numOfKeys += 1
            
            deque = collections.deque()
            deque.append([starti, startj, 0, ".@abcdef", 0])
            
            while deque:
                i, j, steps, keys, collectedKeys = deque.popleft()
    
                if grid[i][j] in "abcdef" and grid[i][j].upper() not in keys:
                    keys += grid[i][j].upper()
                    collectedKeys += 1
                
                if collectedKeys == numOfKeys:
                    return steps
    
                for x, y in direc:
                    ni = i+x
                    nj = j+y
                    if 0<=ni<n and 0<=nj<m and grid[ni][nj] in keys:
                        if (ni, nj, keys) not in moves:
                            moves.add((ni,nj,keys))
                            deque.append([ni, nj, steps + 1, keys, collectedKeys])
                    
            return -1
    

edit: thanks to @yecye, I change the time complexity from O(kmn) to O(mn2^k)



