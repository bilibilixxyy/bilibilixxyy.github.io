---
layout: post
title: 1119. Robot Bounded In Circle
---
### Question
On an infinite plane, a robot initially stands at `(0, 0)` and faces north.
The robot can receive one of three instructions:

  * `"G"`: go straight 1 unit;
  * `"L"`: turn 90 degrees to the left;
  * `"R"`: turn 90 degress to the right.

The robot performs the `instructions` given in order, and repeats them
forever.

Return `true` if and only if there exists a circle in the plane such that the
robot never leaves the circle.



 **Example 1:**

    
    
     **Input:** "GGLLGG"
    **Output:** true
    **Explanation:**
    The robot moves from (0,0) to (0,2), turns 180 degrees, and then returns to (0,0).
    When repeating these instructions, the robot remains in the circle of radius 2 centered at the origin.
    

**Example 2:**

    
    
    **Input:** "GG"
    **Output:** false
    **Explanation:**
    The robot moves north indefinitely.
    

**Example 3:**

    
    
    **Input:** "GL"
    **Output:** true
    **Explanation:**
    The robot moves from (0, 0) - > (0, 1) -> (-1, 1) -> (-1, 0) -> (0, 0) -> ...
    



 **Note:**

  1. `1 <= instructions.length <= 100`
  2. `instructions[i]` is in `{'G', 'L', 'R'}`

### Solution 1
I expect this problem to be medium problem.  
![image](https://assets.leetcode.com/users/lee215/image_1557633739.png)

##  **Intuition**

Let chopper help explain.  
(Chopper from one piece, will let him go if illegal)

Starting at the origin and face north `(0,1)`,  
after one sequence of `instructions`,

  1. if chopper return to the origin, he's in an obvious circle.
  2. if chopper finishes with face not towards north,  
it will get back to the initial status in another one or three sequences.  
  

##  **Explanation**

`(x,y)` is the location of chopper.  
`d[i]` is the direction he is facing.  
`i = (i + 1) % 4` will turn right  
`i = (i + 3) % 4` will turn left  
Check the final status after `instructions`.  
  

##  **Complexity**

Time `O(N)`  
Space `O(1)`

  

 **Java:**

    
    
         public boolean isRobotBounded(String ins) {
            int x = 0, y = 0, i = 0, d[][] = {{0, 1}, {1, 0}, {0, -1}, { -1, 0}};
            for (int j = 0; j < ins.length(); ++j)
                if (ins.charAt(j) == 'R')
                    i = (i + 1) % 4;
                else if (ins.charAt(j) == 'L')
                    i = (i + 3) % 4;
                else {
                    x += d[i][0]; y += d[i][1];
                }
            return x == 0 && y == 0 || i > 0;
        }
    

**C++:**

    
    
         bool isRobotBounded(string instructions) {
            int x = 0, y = 0, i = 0;
            vector<vector<int>> d = {{0, 1}, {1, 0}, {0, -1}, { -1, 0}};
            for (char & ins : instructions)
                if (ins == 'R')
                    i = (i + 1) % 4;
                else if (ins == 'L')
                    i = (i + 3) % 4;
                else
                    x += d[i][0], y += d[i][1];
            return x == 0 && y == 0 || i > 0;
        }
    

**Python:**

    
    
        def isRobotBounded( self, instructions):
            x, y, dx, dy = 0, 0, 0, 1
            for i in instructions:
                if i == 'R': dx, dy = dy, -dx
                if i == 'L': dx, dy = -dy, dx
                if i == 'G': x, y = x + dx, y + dy
            return (x, y) == (0, 0) or (dx, dy) != (0,1)
    


### Solution 2
This problem is easy. Just return true if the final position does not change
or the facing direction is different from the beginning.  
However, this problem is tricky. And I don't think a tricky problem can test
or improve a developer's programming skill.  
I find more and more tricky problems recently. I feel it is not a good trend
if LeetCode goes deeper into it.


### Solution 3
In order for the robot to stay within a circle, you need to move in a cycle.
The only way you move in a cycle is if you end where you start (the origin at
(0, 0)).

The minimum number of instructions you need to repeat is 4 in order to figure
out if you're in a cycle.

For example, if each instruction only rotates 90 degrees, you need to repeat
the instructions 4 times to possibly end where you start.  
If each instruction rotates 180 degrees, you need to repeat the instructions 2
times to possibly end where you start.  
If each instruction rotates 270 degrees, you need to repeat the instruction 4
times to possibly end where you start.

Im the real word, you could stop the algo if you have repeated the
instructions 2x and it ends on (0,0), but here it is still O(N).

    
    
    class Solution(object):
        def isRobotBounded(self, instructions):
            """
            :type instructions: str
            :rtype: bool
            """
            #up, left, down, right
            dir, start, d = ([-1, 0], [0, -1], [1, 0], [0, 1]), [0,0], 0
            for i in range(4):
                for x in instructions:
                    if x == "G":
                        start[0] += dir[d][0]
                        start[1] += dir[d][1]
                    if x == "L": d = (d + 1) % 4 
                    if x == "R": d = (d - 1) % 4
            return True if start == [0,0] else False 
            
    



