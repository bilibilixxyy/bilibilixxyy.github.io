---
layout: post
title: 1103. Moving Stones Until Consecutive
---
### Question
Three stones are on a number line at positions `a`, `b`, and `c`.

Each turn, you pick up a stone at an endpoint (ie., either the lowest or
highest position stone), and move it to an unoccupied position between those
endpoints.  Formally, let's say the stones are currently at positions `x, y,
z` with `x < y < z`.  You pick up the stone at either position `x` or position
`z`, and move that stone to an integer position `k`, with `x < k < z` and `k
!= y`.

The game ends when you cannot make any more moves, ie. the stones are in
consecutive positions.

When the game ends, what is the minimum and maximum number of moves that you
could have made?  Return the answer as an length 2 array: `answer =
[minimum_moves, maximum_moves]`



 **Example 1:**

    
    
     **Input:** a = 1, b = 2, c = 5
    **Output:** [1,2]
    **Explanation:** Move the stone from 5 to 3, or move the stone from 5 to 4 to 3.
    

**Example 2:**

    
    
    **Input:** a = 4, b = 3, c = 2
    **Output:** [0,0]
    **Explanation:** We cannot make any moves.
    

**Example 3:**

    
    
    **Input:** a = 3, b = 5, c = 1
    **Output:** [1,2]
    **Explanation:** Move the stone from 1 to 4; or move the stone from 1 to 2 to 4.
    



 **Note:**

  1. `1 <= a <= 100`
  2. `1 <= b <= 100`
  3. `1 <= c <= 100`
  4. `a != b, b != c, c != a`

### Solution 1
Edge case 1: all three stones are next to each other (`z - x == 2`). Return
`{0, 0}`.  
Edge case 2: two stones are next to each other, or there is only one space in
between. Minimum moves is `1`.

Otherwise; minimum moves are `2`, maximum - `z - x - 2`.

So the position of the middle stone (`y`) only matters for the minimum moves.

    
    
    vector<int> numMovesStones(int a, int b, int c) {
      vector<int> s = { a, b, c };
      sort(begin(s), end(s));
      if (s[2] - s[0] == 2) return { 0, 0 };
      return { min(s[1] - s[0], s[2] - s[1]) <= 2 ? 1 : 2, s[2] - s[0] - 2 };
    }
    

Java version:

    
    
    public int[] numMovesStones(int a, int b, int c) {
      int[] s = { a, b, c };
      Arrays.sort(s);
      if (s[2] - s[0] == 2) return new int[] { 0, 0 };
      return new int[] { Math.min(s[1] - s[0], s[2] - s[1]) <= 2 ? 1 : 2, s[2] - s[0] - 2 };
    }
    


### Solution 2
Max is simple, just calculate the num of vacuum spots between the head -
min(a,b,c) and tail - max(a,b,c)  
Min is max 2, since you can always move the head and tail to the neigbors of
mid, with 2 exceptions:  
a, there is only 1 vacuum btw either of the 2 numbers, then you just need to
move the third number 1 step to the vacuum. for example, 1,3,8, then min is 1  
b, abc is origially connective. min is 0

    
    
           def numMovesStones(self, a, b, c):
            maxi = max(a,b,c) - min(a,b,c) - 2
            mini = max((min(abs(a-b),abs(b-c),abs(a-c), 3) - 1), 1)
            if maxi == 0: mini = 0            
            return [mini,maxi]


### Solution 3
Good code should focus on readability instead of being as short as possible.

    
    
    class Solution:
        def numMovesStones(self, a: int, b: int, c: int) -> List[int]:
            x, y, z = sorted([a, b, c])
            if x + 1 == y == z - 1:
                min_steps = 0
            elif y - x > 2 and z - y > 2:
                min_steps = 2
            else:
                min_steps = 1
            max_steps = z - x - 2
            return [min_steps, max_steps]
    



