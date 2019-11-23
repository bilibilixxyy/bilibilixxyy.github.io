---
layout: post
title: 787. Sliding Puzzle
---
### Question
On a 2x3 `board`, there are 5 tiles represented by the integers 1 through 5,
and an empty square represented by 0.

A move consists of choosing `0` and a 4-directionally adjacent number and
swapping it.

The state of the board is _solved_ if and only if the `board` is
`[[1,2,3],[4,5,0]].`

Given a puzzle board, return the least number of moves required so that the
state of the board is solved. If it is impossible for the state of the board
to be solved, return -1.

 **Examples:**

    
    
     **Input:** board = [[1,2,3],[4,0,5]]
    **Output:** 1
    **Explanation:** Swap the 0 and the 5 in one move.
    
    
    
    **Input:** board = [[1,2,3],[5,4,0]]
    **Output:** -1
    **Explanation:** No number of moves will make the board solved.
    
    
    
    **Input:** board = [[4,1,2],[5,0,3]]
    **Output:** 5
    **Explanation:** 5 is the smallest number of moves that solves the board.
    An example path:
    After move 0: [[4,1,2],[5,0,3]]
    After move 1: [[4,1,2],[0,5,3]]
    After move 2: [[0,1,2],[4,5,3]]
    After move 3: [[1,0,2],[4,5,3]]
    After move 4: [[1,2,0],[4,5,3]]
    After move 5: [[1,2,3],[4,5,0]]
    
    
    
    **Input:** board = [[3,2,4],[1,5,0]]
    **Output:** 14
    

**Note:**

  * `board` will be a 2 x 3 array as described above.
  * `board[i][j]` will be a permutation of `[0, 1, 2, 3, 4, 5]`.

### Solution 1
Algorithm:

Consider each state in the board as a graph node, we just need to find out the
min distance between start node and final target node "123450". Since it's a
single point to single point questions, Dijkstra is not needed here. We can
simply use BFS, and also count the level we passed. Every time we swap 0
position in the String to find the next state. Use a hashTable to store the
visited states.

    
    
    public int slidingPuzzle(int[][] board) {
            String target = "123450";
            String start = "";
            for (int i = 0; i < board.length; i++) {
                for (int j = 0; j < board[0].length; j++) {
                    start += board[i][j];
                }
            }
            HashSet<String> visited = new HashSet<>();
            // all the positions 0 can be swapped to
            int[][] dirs = new int[][] { { 1, 3 }, { 0, 2, 4 },
                    { 1, 5 }, { 0, 4 }, { 1, 3, 5 }, { 2, 4 } };
            Queue<String> queue = new LinkedList<>();
            queue.offer(start);
            visited.add(start);
            int res = 0;
            while (!queue.isEmpty()) {
                // level count, has to use size control here, otherwise not needed
                int size = queue.size();
                for (int i = 0; i < size; i++) {
                    String cur = queue.poll();
                    if (cur.equals(target)) {
                        return res;
                    }
                    int zero = cur.indexOf('0');
                    // swap if possible
                    for (int dir : dirs[zero]) {
                        String next = swap(cur, zero, dir);
                        if (visited.contains(next)) {
                            continue;
                        }
                        visited.add(next);
                        queue.offer(next);
    
                    }
                }
                res++;
            }
            return -1;
        }
    
        private String swap(String str, int i, int j) {
            StringBuilder sb = new StringBuilder(str);
            sb.setCharAt(i, str.charAt(j));
            sb.setCharAt(j, str.charAt(i));
            return sb.toString();
        }
    


### Solution 2
# Update:

  1.  **distance** in the follows changed to **displacement** to avoid confusion:
  2. see after the code.

Convert array to string, e.g., [[1,2,3],[4,0,5]] -> "123405", hence the
corresponding potential swap displacements are: -1, 1, -3, 3. Also note,
charAt(2) and charAt(3) are not adjacent in original 2 dimensional int array
and therefore are not valid swaps.

    
    
        private static final int[] d = { 1, -1, 3, -3 }; // potential swap displacements.
        public int slidingPuzzle(int[][] board) {
            // convert board to string - initial state.
            String s = Arrays.deepToString(board).replaceAll("\[|\]|,|\s", "");
            Queue<String> q = new LinkedList<>(Arrays.asList(s)); // add initial state to queue.
            Set<String> seen = new HashSet<>(q); // used to avoid duplicates
            int ans = 0; // record the # of rounds of Breadth Search
            while (!q.isEmpty()) { // Not traverse all states yet?
                // loop used to control search breadth.
                for (int sz = q.size(); sz > 0; --sz) { 
                    String str = q.poll();
                    if (str.equals("123450")) { return ans; } // found target.
                    int i = str.indexOf('0'); // locate '0'
                    for (int k = 0; k < 4; ++k) { // traverse all options.
                        int j = i + d[k]; // potential swap index.
                        // conditional used to avoid invalid swaps.
                        if (j < 0 || j > 5 || i == 2 && j == 3 || i == 3 && j == 2) { continue; } 
                        char[] ch = str.toCharArray();
                        // swap ch[i] and ch[j]:  ch[i] = ch[j], ch[j] = '0'. Updated per @caowang888's suggestion. 
                        ch[i] = ch[j];
                        ch[j] = '0';
                        s = String.valueOf(ch); // a new candidate state.
                        if (seen.add(s)) { q.offer(s); } //Avoid duplicate.
                    }
                }
                ++ans; // finished a round of Breadth Search, plus 1.
            }
            return -1;
        }
    

**Analysis:**  
There are at most 6! permutation of the 6 numbers: 0~5. For each permustion,
cost spaceO(6); String.indexOf() and String.equals() cost time O(6).
Therefore, space and time both cost 6 * 6! = 4320.

**Time & space: O(4320)**.

Feel free to let me know if you can find a tighter bound.

**Update** :  
2  
**Question: Can you explain how did u determine the potential swap distance?**

**Answer:**

When you map a 2 dimensional ( row * column ) matrix to 1 dimensional array,
the swap to left and right are still -1 and 1 respectively in terms of index
change in 1-D array. In contrast, the swap to up and down are -column and
column as of index change.

**That is, for any given index (i, j) in the matrix, it will be mapped to (i *
column + j) in the array. Since in the matrix (i, j) could swap with (i, j -
1), (i, j + 1), (i - 1, j), and (i + 1, j), in the array (i * column + j)
would swap with (i * column + j - 1), (i * column + j + 1), ((i - 1) * column
+ j) and ((i + 1) * column + j) accordingly.**

**We can easily conclude the swap displacement are -1, 1, -column, and column
correspondingly.**

e.g.: Let's consider (1, 2) in the following 3 * 5 matrix

    
    
    column # -->               0     1     2     3     4
                          0  (0,0) (0,1) (0,2) (0,3) (0,4)
                          1  (1,0) (1,1) (1,2) (1,3) (1,4)
                          2  (2,0) (2,1) (2,2) (2,3) (2,4)
    

we can swap (1, 2) with (1, 1), (1, 3), (0, 2), and (2, 2)

When mapped to 1 dimensional array, it can be obtained that (1 * 5 + 2) swap
with (1 * 5 + 2 - 1), (1 * 5 + 2 + 1), ((1 - 1) * 5 + 2) and ((1 + 1) * 5 +
2):

    
    
      0    1    2    3    4    5    6    7    8    9   10   11   12   13   
    (0,0)(0,1)(0,2)(0,3)(0,4)(1,0)(1,1)(1,2)(1,3)(1,4)(2,0)(2,1)(2,2)(2,3)
    
     14
    (2,4)
    

Obviously, we can swap 7 with, 6 (= 7 - 1), 8 (= 7 + 1), 2 (= 7 - 5), 12 (= 7
+ 5), where 5 is matrix column (width).

 **The displacements in the above case are -1, 1, -5, and 5.**

Hope the above answers your question.


### Solution 3
    
    
    class Solution:
        def slidingPuzzle(self, board):
            moves, used, cnt = {0: {1, 3}, 1:{0, 2, 4}, 2:{1, 5}, 3:{0, 4}, 4:{1, 3, 5}, 5:{2, 4}}, set(), 0
            s = "".join(str(c) for row in board for c in row)
            q = [(s, s.index("0"))]
            while q:
                new = []
                for s, i in q:
                    used.add(s)
                    if s == "123450":
                        return cnt
                    arr = [c for c in s]
                    for move in moves[i]:
                        new_arr = arr[:]
                        new_arr[i], new_arr[move] = new_arr[move], new_arr[i]
                        new_s = "".join(new_arr)
                        if new_s not in used:
                            new.append((new_s, move))
                cnt += 1
                q = new
            return -1
    



